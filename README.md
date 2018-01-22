# nweb
a tiny c implementation of a http server

nweb stands for Nigel's Web server but I some times call it a nano web server (i.e. very small).

## how to use
`./nweb [port] [root directory]`
`./nweb 8181 /home/nwebdir`

## how to compile
`gcc -O -DLINUX nweb.c -o nweb`

-----
this is part of [the nmon project](https://sourceforge.net/projects/nmon) from Nigel Griffiths
