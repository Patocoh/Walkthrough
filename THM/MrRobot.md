Room: https://tryhackme.com/room/mrrobot

We Start this Machine with Reconnaissance to follow with escalation.

# Reconnaissance

We will use NMAP and depending on that a web directory enumeration as well as a manual enum.

```sudo nmap -v -sV -sC -Pn -T4 -p- -oG mrrobot.nmap 10.10.36.150```

Output of NMAP:

![image](https://user-images.githubusercontent.com/71237545/115782862-29fa0e80-a38a-11eb-8ff1-4e935b21c6e8.png)

During the manual enumeration I found something interesting under robots.txt (this is always something good to do when you scan websites)
From the site we can see the following commands:

![image](https://user-images.githubusercontent.com/71237545/115782923-3bdbb180-a38a-11eb-8627-3af77636aa33.png)

Each one show us a different output.

| IP | Type of Output |
| ------------- | ------------- |
| http://IP/prepare | 	video |
| http://IP/fsociaty | 	video |
| http://IP/inform | 	pictures |
| http://IP/question | 	pictures |
| http://IP/wakeup | 	video |
| http://IP/join | 	Request of email and nothing else. |

Under robots.txt we can find the first key. And also important information.
Also checking the source code of question, I found this runs a CMS! A Wordpress. So, we can look for the login site.

http://IP/wp-login.php and  use wpscan with the important information we got from robots to check.

I assume the username should be Elliot, cause, well he's the star of the show. But also looking at the login.

![image](https://user-images.githubusercontent.com/71237545/115783106-75acb800-a38a-11eb-8c07-725f7b98a3af.png)

With this information we can see that elliot is a valid user.

```wpscan --url http://10.10.129.129/wp-login.php --passwords fsocity.dic --usernames elliot```

And after a looooooooooooooooot of time we get the password!!

![image](https://user-images.githubusercontent.com/71237545/115783127-7ba29900-a38a-11eb-9cdf-1445fa5af2f0.png)

Once we go in we can check for our reverse shell from a template, specifically the 404.php
We edit it with the editor under appearance to insert our reverse shell.

![image](https://user-images.githubusercontent.com/71237545/115783157-83623d80-a38a-11eb-8ef6-04ed16c30e64.png)

After we use curl or open the url on a browser we can get our reverse shell.
Curl [IP/wp-content/theme/url.php]

![image](https://user-images.githubusercontent.com/71237545/115783180-88bf8800-a38a-11eb-826d-a21e9ce421dc.png)

# Escalation

Looking under the files we can find the second key but we can't open it. So, we get an interesting file with valid information. However, we have to decrypt it.
Hash-cat for the win !
With this we get the user credentials!
Now we finally get the second key.

After this we will proceed to look for files to escalate privileges.
We pass linpeas to run it under /tmp 
So checking the files. We can see something interesting to do with the nmap command.

![image](https://user-images.githubusercontent.com/71237545/115783207-9117c300-a38a-11eb-8cb6-5b342faf1e2f.png)

With this I look up on GTFobins funding we can use an interesting privesc

![image](https://user-images.githubusercontent.com/71237545/115783219-95dc7700-a38a-11eb-9855-86af4d6a83e5.png)

And with this we can look for the 3rd key and we complete this room.

And with this we can look for the 3rd key and we complete this room.

![image](https://user-images.githubusercontent.com/71237545/115783236-9a089480-a38a-11eb-8478-ad8895b285bf.png)

