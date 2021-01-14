<!--
.. title: Prevent TeXShop from stealing focus from an external editor
.. slug: keep-texshop-from-stealing-focus
.. date: 2015-04-08 21:44:25 UTC-05:00
.. tags: LaTeX, TeXShop
.. link: 
.. description: 
.. type: text
-->

I use [Vim](http://www.vim.org/) as my editor of choice for nearly all text editing activities including writing LaTeX documents.  My usual workflow is to use a split window setup with `tmux` and run `latexmk -pvc` on my LaTeX file in one split-pane while editing the source file in the other split-pane.  If your not familiar with `latexmk` it is a Perl script that, in a smart way, using minimal number of compile runs, keeps your LaTeX document output up-to-date with the correct cross-references, citations, etc.  The `-pvc` option keeps it running in the background and recompiles with a detected change in *any* of the project files.  I then use [TeXShop](http://pages.uoregon.edu/koch/texshop/) as a PDF viewer because it has the nice ability to detect changes to the PDF output and autoupdate.  Mac OS X's built-in Preview will do this as well, but you must click the window to enable the refresh.  However, by default, TeXShop will steal focus from the editor window.  This is annoying causing me to click or Command-Tab back to the Terminal to continue typing.  I found a fix in a [comment on Stack Exchange](http://tex.stackexchange.com/questions/43057/macosx-pdf-viewer-automatic-reload-on-file-modification).  If you type

````bash
defaults write TeXShop BringPdfFrontOnAutomaticUpdate NO
````

in the Terminal window this will disable the focus-stealing behavior and leave the window in the background so that it doesn't disrupt continuous editing.
