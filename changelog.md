* nweb.c -- a tiny c implementation of a http server
nweb stands for Nigel's Web server but I some times call it a nano web server (i.e. very small).


# changelog
This file contains lots of hints & tips on compiling and running the nweb code

## version 24
* Website move due to it be oddly dropped with no warning
* Due to feedback `chdir()` was changed to `chroot()`
  This massively increases security as clever URL trickiery can never get a non-home directory file sent to the browser.
* Please note this code is to illustrate the algorythm of a webserver in a minimum number of lines of code - it is not ment to be absolutely perfect C code. 


## version 23

* Bug fixed: was duplicating errors in the nweb.log file. thanks to Kieran Grant for stopping this and pointing out the fix.

* Added support for favicon.ico if nothing elase this will stop annoying errors in the log file.  Most browsers on first encountering a webpage also ask for this file.  The name mean favourite icon - Wikipedia has more on this.  It is a tiny Bit Map image (normally called .bmp and
BMP editors can be used to create one - I used Windows Paint)
I uses a very simple 16x16 pixels and 256 colour to keep the size and complexity down.
To add support I added .ico file extension to the allowed extensions data structure and the sample favicon.ico file.
This is then displyed by the browser at the little graphic next to the URL. The one I made looks like this withe blue background.
```
+---+
|n  |
|Web|
+---+
```

## version 22

This nweb is a web server that will respond to simple web browser 
requests for static files.
This version is exactly 200 lines long. 
I started with a target 100 lines and it worked fine too but then 
added comments, file type checks,
security checks, sensible directory checks and logging.

To compile this you need a basic C compiler use: cc nweb22.c -o nweb
If you want it to run faster you could include optimisation: 
cc -O2 nweb22.c -o nweb

Then to run it as root for the test files:
1) Place the index.html file and nigel.jpg in to a sensible directory 
	Note: not any system directories like / or /tmp 
	A director in /home would be a good idea - below we use /home/nigel/web

2) Make these files readable
	chmod ugo+r /home/nigel/web

3) Decide the port number
	Port number 80 is the web server default and assumed by web browsers.
	you might have to be the root user to use that  port number.
	If the machine is already running a web server then it will 
	probably be using port 80 so you can't us 80.
	I test using port 8181 but you have to make up your mind.

4) Start the nweb server:  /home/nigel/bin/nweb 80   /home/nigel/web
	or if using 8181   /home/nigel/bin/nweb 8181 /home/nigel/web

	If the program and files are all in /home/nigel/all
	You could use:
		cd /home/nigel/all
		rm -f nweb.log
		./nweb 80 .
		ps -ef | grep nweb 
		tail -f nweb.log

	See below for explanations for the rm, ps and tail

5) Note that any suitable files in the directory 2nd argument above 
could be served by the nmon web server - so don't have anything secret 
in that directory!

6) Use a browser to test it if using port 80 on a machine hostname 
of abc123.com browser to:
	http://abc123.com/index.html

   If using port 8181
	http://abc123.com:8181/index.html

7) Running nweb as a regualr user
You can run nweb as a regular user on some operating systems but not all. I find using sudo works on some: 	`sudo /home/nigel/bin/nweb 80   /home/nigel/web`. If you try and the log file reports socect connections errors worth 
	trying the 8181 port or higher numbers.  This is because 
	lower port numbers are reserved for root user use.

8) VERY IMPORTANT
nweb will behave like a daemon process, so it disconnects from the 
users command shell, goes into the back ground, closes input and 
output I/O & protects itself from you logging off.  
It will look like it stopped, when it is in fact still running in 
the background - check with `ps -ef | grep nweb`
Also note you will not see errors or warnings messages - they go in the log file. Look in the nweb.log file for problems starting up or browsers connecting and requesting pages with: `cat nweb.log` or: `tail -f nweb.log`. I find it good to remove the nweb.log before I start nweb or it is easy to get confused with previous error messages in the log.

9) As nweb runs as a daemon process it will try to run forever and not conntected to your user or terminal session.  Logging out will not effect it. To stop nweb you have to stop it by a KILL signal. 
Find the nweb process with: `ps -ef | grep nweb`
Then use: `kill -9 PID ` to stop the nweb process.


10) nweb.log Log file - what to look for
Good example

Here is an edited down sample nweb.log file. 
Note the first line = "starting" looks good as there is no following error.

```
 INFO: nweb starting:8181:8913126
 INFO: request:GET /index.html HTTP/1.1 [[1KB of deleted request stuff here]]:2
 INFO: SEND:index.html:2
 INFO: Header:HTTP/1.1 200 OK
Server: nweb/21.0
Content-Length: 239
Connection: close
Content-Type: text/html

 INFO: request:GET /nigel.jpg HTTP/1.1  [[1KB of deleted request stuff here]]:3
 INFO: SEND:nigel.jpg:3
 INFO: Header:HTTP/1.1 200 OK
Server: nweb/21.0
Content-Length: 10184
Connection: close
Content-Type: image/jpg
``` 

Bad example
A failure to start due to the port number is not aallowed or in use looks like:
```
 INFO: nweb starting:80:8323296
ERROR: system call:bind Errno=13 exiting pid=8323296
```
