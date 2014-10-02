# papertemplate

Simple template system for conference paper.
It includes a make file with the following options: 

### make clean
Removes all pesky helper files that LaTeX creates. So you can check into the svn or git without so much versioning problems.

### make pdf
Generates a pdf-file from the latex document set in the make file.

### make bib
Recompiles the bib tex file. 

### make pandoc
Removes all sub-tex files used as input in the main file. Converts all markdown text files into latex files. Makes a pdf of all files.




