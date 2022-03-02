 ## Room: [retro](https://tryhackme.com/room/retro)

### Reconnaissance

[[Nmap]] 
Using the command for normal enumeration.
	# sudo nmap -sS -p- -vvv -Pn --max-rate 500 retro.thm
	![[Pasted image 20220115084928.png]]
	
Then with the specific port nmap
	# nmap -A -p80,3389 -v -oN scan.nmap retro.thm
	![[Pasted image 20220115085502.png]]

For the port 80, and using [[Gobuster]] it's possible to see hidden directories.
	# gobuster dir -u 'http://retro.thm/' -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt  -t 50 -x .html,.js,.php,.bak,.old -o gobuster1.txt

Checking the site a wordpress CMS was visible through wappalizer. and with this wpscan worked good for enumeration.
	![[Pasted image 20220115085823.png]]
	# wpscan --url http://retro.thm/retro/ --api-token token -e vp,u --stealthy

From web enumeration and under the "Read Player One" page, a password was found. which led to access the dashboard of the account.
	![[Pasted image 20220115092238.png]]
	
Using the Theme editor, a PHP code to run commands on the system through a web shell.
	![[Pasted image 20220115094530.png]]
	![[Pasted image 20220115094544.png]]
With this it was possible to enumerate the system and proceed.
Also using freerdp the first flag appear under desktop.
	![[Pasted image 20220115100747.png]]
	
### Privilege Escalation

To escalate privileges it was passed through winpeas to enumerate the system.
	
By downloading the following exe and passing to the system, later unziping it and later on, executing it, a terminal with privileges was obtained.
	https://github.com/SecWiki/windows-kernel-exploits/blob/master/CVE-2017-0213/CVE-2017-0213_x64.zip 
	![[Pasted image 20220115111629.png]]

An d further we can get both flags.
	![[Pasted image 20220115111752.png]]
	
