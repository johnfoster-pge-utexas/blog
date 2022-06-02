<!--
.. title: Run Peridigm (and other scientific HPC codes) without building via Docker
.. slug: peridigm-without-building-via-Docker
.. date: 2015-04-22 10:44:25 UTC-05:00
.. tags: Docker, Peridigm, Trilinos, HPC, MPI
.. link: 
.. description: 
.. type: text
-->

**UPDATE 6/1/2022:** This post has been edited replacing the original docker
image location at `johntfoster/peridigm` with the current location at
`peridigm/peridigm`.  Additionally, the discussion related to parallel
computing can now be accomplished much easier by using [docker-compose](https://docs.docker.com/compose/). See the [Running Simulations with Peridigm Docker Image](https://github.com/peridigm/peridigm/blob/master/doc/RunningSimulationsDocker.md) section of the Peridigm repository for more details.

---

If your a software engineer working in web development or cloud software deployment, you would have had to have your head in the sand for the last two years to have not heard of [Docker](http://www.docker.com) by now.  Briefly, Docker is a light-weight application container deployment service (think super low-overhead virtual machine, usually meant to contain one application and its dependencies), based on Linux containers.  While there are [100s of articles](https://www.google.com/search?q=docker&oq=docker++&aqs=chrome..69i57j69i60l2j0l3.2551j1j9&sourceid=chrome&es_sm=119&ie=UTF-8) describing Docker, its uses, many services, and even companies springing up around it; my colleagues in the scientific computing/high-performance computing (HPC) world are either not aware of Docker or don't see the utility, because it's hard to find significant information for or uses of Docker in this field.  This post will hopefully demonstrate some of the utility of the service and promote its use in HPC and in academic settings.  Additionally, as a contributor to the open-source software [Peridigm](http://peridigm.sandia.gov), my hope it that the path demonstrated here might lower the courage required to try out the software and promote new users.

To provide some background/context for my own interest in Docker, in my current role as an academic advisor to graduate students and in my previous career as a researcher at Sandia National Laboratories I have often been a user and/or developer of scientific software that has a fairly complex dependent application stack. To use *Peridigm*, a massively parallel computational peridynamic mechanics simulation code, as an example, we have a large dependency on [Trilinos](http://trilinos.org).  The packages we use from Trilinos have additional dependencies on a message passing interface (MPI) implementation (e.g. [OpenMPI](http://www.open-mpi.org)), [Boost](http://www.boost.org), and [NetCDF](http://www.unidata.ucar.edu/software/netcdf/).  NetCDF has a dependency on [HDF5](https://hdfgroup.org/HDF5/), HDF5 on [zlib](http://www.zlib.net/), etc.  All of these need a C/C++ compiler for building, of course, and if we are using the Intel compilers we might as well go ahead and use [MKL](https://software.intel.com/en-us/intel-mkl) libraries for efficiency.  Trilinos uses [CMake](http://www.cmake.org) as a build system, so we need that around as well, at least during the build process. I'm sure there are others I am not thinking of at this moment.  Figuring out and understanding these dependencies and learning to debug build issues can take a really long time for a budding computational scientist to master.  Problems can be really compounded in a large multi-user HPC cluster where many different versions of compilers and libraries are present.  Using UNIX [modules](http://modules.sourceforge.net/) goes a long way towards keeping things straight, but problems still arise that can be time consuming to troubleshoot.  I usually have new graduate students go through the process of building *Peridigm* and all the dependencies for their own learning experience when they first join my group, in every case they struggle considerably the first few times the have to build the entire suite of software on a new machine.  In some cases, particularly with MS level students, it can be a serious impediment to progress, as they are very short time lines to make meaningful research progress, and oftentimes they are only going to be end-users of the code, not developers.  Almost certainly they are not going to make changes to any of the packages in Trilinos or any of the other dependencies outside of *Peridigm*.  Enter Docker.

Docker allows for the ability to run applications in prebuilt containers on any Linux machine with Docker installed (or even Mac OS X and Windows via [Boot2Docker](http://boot2docker.io/)).  If your Linux distribution doesn't already have it installed, just use your package manager (e.g. `apt-get install docker` on Debian/Ubuntu based machines and `yum install docker` on Fedora/Redhat based machines).  Docker is so ubiquitous at this point there are even entire Linux distributions like [CoreOS](https://coreos.com/) being built specifically to maximize its strengths.  Additionally, there is the [Docker Hub Registry](https://registry.hub.docker.com/) which provides [Github](http://github.com) like `push/pull` operations and cloud storage of prebuilt images.  Of course, you or your organization can host your own Docker image registry as well (note: think of a *Docker image* like a C++ class, and a *Docker container* as a C++ object, or an *instance* of the image that runs).  You can derive images from one another and this is what I have done in setting up an image of *Peridigm*.  First, I have an image that starts with a baseline Debian based Linux and installs NetCDF and its dependencies.  I have two tagged versions, a standard NetCDF build and a *largefiles* patched version which makes the [large file modifications](https://peridigm.sandia.gov/content/netcdf) as suggested in the *Peridigm* build documentation. The NetCDF largefile image can be pulled to a Docker users local machine from my public Docker Hub Registry with

````bash
docker pull peridigm/netcdf:largefiles
````

the NetCDF image is built automatically via continuous integration with a [Github repository](https://github.com/johntfoster/docker-netcdf) that contains a [Dockerfile](https://docs.docker.com/reference/builder/) with instructions for the image build process.  I then derive a Trilinos image from the NetCDF largefiles image.  This image contains only the Trilinos packages enabled such that *Peridigm* can be built.  The Trilinos image can be pulled to a local machine with

````bash
docker pull peridigm/trilinos
````

this Trilinos image could be used to derive other images for other Trilinos based application codes which utilize the same dependent packages as *Peridigm*.  The `cmake` build script which shows the active Trilinos packages in this image can be viewed [here](https://github.com/johntfoster/docker-trilinos/blob/master/trilinos-debian-cmake.sh).  This image is also built via continuous integration with [this Github repository](https://github.com/johntfoster/docker-trilinos) which can easily be modified to include more Trilinos packages.  Finally, the *Peridigm* image can be pulled to a local machine with

````bash
docker pull peridigm/peridigm
````

this image is built from the Dockerfile [found here](https://github.com/johntfoster/docker-peridigm), however it must be modified slightly to work with a local *Peridigm* source distribution if you want to build your own image because I do not want to distribute the *Peridigm* software source code as that is done by Sandia National Laboratories (see [here](http://peridigm.sandia.gov) if interested in getting the source). To run *Peridigm* you simply have to run the command:


````bash
docker run --name peridigm0 -d -v `pwd`:/output peridigm/peridigm \ 
       Peridigm fragmenting_cylinder.peridigm 
````

Even if you have not pulled the images locally from the Docker Hub Registry, this command will initiate the download process and  run the command after downloading.  Because the image contains all the necessary dependencies, it is around 2GB in size; therefore, it takes a while to download on the first execution.  I have not made any attempts to optimize the image size, but it's likely it could be made smaller to speed this process up.  Once you have the image locally, launching a container to run a simulation takes only milliseconds.  The `--name` option gives the container the name `peridigm0`, this could be any name you choose and naming the container is not required, but makes for an easy way to stop the execution if you wish.  If you need to stop the execution, simply run `docker stop peridigm0`.  The `-d` option "detaches" the terminal so that the process runs in the background, you can reattach if needed with `docker attach peridigm0`.  The `-v` option is critical for retrieving the data generated by the simulation.  Docker handles data a little strangely, so you have to mount a local volume, in this case the current working directory returned by `pwd`, but in general it could any `/path/to/data/storage`.  The local path is mounted to the volume `/output` that has been created in the *Peridigm* Docker image.  You must place your input file, in this case `fragmenting_cylinder.peridigm` from the examples distributed with *Peridigm*, in your local shared mount for the *Peridigm* executable located in the Docker image to access it. As the simulation runs, you will see an output file `fragmenting_cylinder.e` appear in the shared mount.  The other two arguments, `peridigm/peridigm` and `Peridigm` are the image name in the Docker Hub Registry and the executable, respectively.


###To quickly recap, if all you want to test out *Peridigm* quickly, follow these 3 steps:

1. Install Docker via your package manager on Linux or utilize Boot2Docker on Mac OS X and Windows.

2. Place a *Peridigm* input file such as `fragmenting_cylinder.perdigm` in a directory.

3. Run the `docker run ...` command above replacing `pwd` with the directory name where you placed the file in 2., if not running from the current working directory.


That's it.  No compiling, no dependencies.  Most of the performance studies I've seen report a very small, 1-2% hit from the Dockerized version of an application over a natively installed application stack.


Of course, *Peridigm* is meant to be run in a massively parallel setting.  A Docker container can take advantage of some multithreading, therefore you can also just run an MPI job right inside a Docker container, for *Peridigm* we can simply run

````bash
docker run --name peridigm0 -d -v `pwd`:/output peridigm/peridigm \
       mpiexec -np 2 Peridigm fragmenting_cylinder.peridigm 
````

for a possibly small performance gain.  If you run this command, you should now see the domain decomposed output files, e.g. `fragmenting_cylinder.e.0` and  `fragmenting_cylinder.e.1` appear in your mounted directory.  However, this alone will not give you the true gains you expect form multicore CPUs or close to what you would see in a massively parallel HPC cluster.  We can spawn a virtual cluster of Docker containers, from the `peridigm/peridigm` image and use MPI to communicate between them.  To do this, we launch our virtual cluster with `docker run ...` but this time without any execution command.  The default is for these containers to be ssh servers, so they will sit there idle until we need them for an MPI task.  For example, if we want a 2 node job, we can run

````bash
docker run --name peridigm0 -d -P -h node0 -v `pwd`:/output peridigm/peridigm
docker run --name peridigm1 -d -P -h node1 -v `pwd`:/output peridigm/peridigm
````

this launches containers named `peridigm0` and `peridigm1` that will have local (to their containers) machine names `node0` and `node1`.  Now we can find the containers IP addresses with the following command

````bash
docker inspect -f "{{ .NetworkSettings.IPAddress }}" peridigm0
docker inspect -f "{{ .NetworkSettings.IPAddress }}" peridigm1
````

These commands will return the ip addresses, just for demonstration purposes, let's assume that they are `10.1.0.124` and `10.1.0.125` respectively.  Now we can launch `mpiexec` to run *Peridigm* on our virtual cluster.  This time the containers are already running, so all we have to is run

````bash
docker exec peridigm0 mpiexec -np 2 -host 10.1.0.124,10.1.0.125 \
       Peridigm fragmenting_cylinder.peridigm
````

which executes `mpiexec` on the container named `peridigm0` and passes a host list to make MPI aware of where the nodes waiting for tasks are.  You can have fine grained control over `mpirun` as well, for example if you wanted to run 2 MPI tasks per Docker container, you could do something like

````bash
docker exec peridigm0 mpiexec -np 4 -num-proc 2 -host 10.1.0.124,10.1.0.125 \
       Peridigm fragmenting_cylinder.peridigm
````

when your finished you can stop and remove the containers with

````bash
docker stop peridigm0 peridigm1
docker rm -v peridigm0 peridigm1
````

the `-v` option to `docker rm` ensures that the mounted volumes are removed as well and there not any orphaned file system volume shares. I have written a Python script (shown below) that automates the whole process for an arbitrary number of Docker containers in a virtual cluster.

<script src="http://gist-it.sudarmuthu.com/https://github.com/johntfoster/docker-peridigm/blob/master/pdrun?footer=minimal"></script>

You can utilize this script to run *Peridigm* simulations on a virtual Docker cluster with the following command:

````bash
./pdrun --np 4 fragmenting_cylinder.peridigm --path=`pwd`
````

please consult the documentation of the script for more info about the options using `./pdrun -h`.  There are really only a few detials in between what I've done here and being able to deploy this on a production HPC cluster.  The main difference would be that you would use a resource manager, e.g. [SLURM](http://slurm.schedmd.com/) to schedule running the Docker containers.  You would also want to have both your mounted volume and a copy of the Docker image on a shared filesystem across all nodes of the cluster.  Did I mention you can [deploy your own private registry server?](https://docs.Docker.com/registry/deploying/)

The last point I would like to make about Docker is that, while I demonstrated that end users can benefit by eliminating the entire build process and move right to running the simulation code, also developers could greatly benefit from using a common development image and requiring only support for the Docker image, as it can be quickly deployed and tested against all platforms.  How many times have your heard, even from experienced developers, after a commit fails in continuous integration, "It worked on my machine...".  Docker offers a solution to those problems and many others.

