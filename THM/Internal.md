Room:  https://tryhackme.com/room/internal

# RECONNAISSANCE

We start with a NMAP to the IP

```sudo nmap -v -sV -sC -Pn -T4 -p- -oG internal.nmap internal.thm```

Output of NMAP!

![image](https://user-images.githubusercontent.com/71237545/115320556-22e7bc00-a150-11eb-979c-14ecf14a9ddf.png)

We start doing manual enumeration on port 80.
The port 80 show as Apache2 default page.!

![image](https://user-images.githubusercontent.com/71237545/115320578-2b3ff700-a150-11eb-85c6-a7d6efdc75db.png)

So we go back to do some enumeration with gobuster on the site.
As soon as we launch the gobuster command we can see a directory under /blog. So we let it go while we enumerate manually this blog!

![image](https://user-images.githubusercontent.com/71237545/115320610-385ce600-a150-11eb-9e85-f747de698ad1.png)

We can see we're in front of a wordpress CMS .
We use WPScan for this.

```wpscan --url http://internal.thm/blog/ --api-token Uurpzypf7dGssJ3aYdm52yQaakwP4FJLtLkG3aAM76o -e vp,u --stealthy```

Output of WPScan

![image](https://user-images.githubusercontent.com/71237545/115320647-44e13e80-a150-11eb-8fee-14d413fbf826.png)
![image](https://user-images.githubusercontent.com/71237545/115320696-5d515900-a150-11eb-8f1d-82f8465913a8.png)

From this we can see we identified 2 vuln.
We also can find a phpmyadmin  site under that directory.

![image](https://user-images.githubusercontent.com/71237545/115320775-7ce88180-a150-11eb-99a6-bafc5cc661aa.png)

We go back to the login of wordpress and try to force the entrance with the user admin.

```wpscan --url http://internal.thm/blog/wp-login.php/ --passwords /usr/share/wordlists/rockyou.txt --usernames admin```  

Output of it.
After a while we can get the credentials

![image](https://user-images.githubusercontent.com/71237545/115320821-9093e800-a150-11eb-9fdb-02604da495ab.png)

Checking the post we can find some credentials.

![image](https://user-images.githubusercontent.com/71237545/115320859-a4d7e500-a150-11eb-8c17-7f4e0086d8a2.png)

Content of Private:

![image](https://user-images.githubusercontent.com/71237545/115320881-ad302000-a150-11eb-9576-da238ccc1d2b.png)

With this we can try to use those credentials for SSH.It didn't work.
After a bit of research I realize we can edit the theme for different payloads. I edited the template for 404.php with a reverse shell. 
I started to listen on the port I used for the reverse and after I went to the URL I got my reverse up.
http://internal.thm/blog/wp-admin/theme-editor.php?file=404.php&theme=twentyseventeen

![image](https://user-images.githubusercontent.com/71237545/115320920-c3d67700-a150-11eb-91e7-ea693b54f51e.png)

# ESCALATION

I look into the /etc/passwd file.

![image](https://user-images.githubusercontent.com/71237545/115320937-cf29a280-a150-11eb-9993-8475aa9280fb.png)

Once I got in I passed linpeas from my attacker machine and after I run it I got the credentials for phpmyadmin.

![image](https://user-images.githubusercontent.com/71237545/115320956-d650b080-a150-11eb-9a72-85d71f63e451.png)

This are the credentials for the phpmyadmin plugin we found before.
Looking though some files I found this under /opt

![image](https://user-images.githubusercontent.com/71237545/115321037-039d5e80-a151-11eb-9ff4-b91bfcc70f9a.png)

I used this credentials and try to do SSH which work.
In the local directory of aubreanna we can see user.txt (this is the first flag)

![image](https://user-images.githubusercontent.com/71237545/115321051-0bf59980-a151-11eb-8c6e-b19284bfc3ce.png)

Then we check the other file called jenkins.txt

![image](https://user-images.githubusercontent.com/71237545/115321093-1f086980-a151-11eb-937f-3cf266905438.png)

From here we can get that there's another service running on the port 8080.
We can forward the jenkings cms though the SSH Tunnel. 
For syntax: https://linuxize.com/post/how-to-setup-ssh-tunneling/

![image](https://user-images.githubusercontent.com/71237545/115321108-2af42b80-a151-11eb-9c62-da301d3b7c08.png)

After running this, we can get the login of jenkins on our localhost.

![image](https://user-images.githubusercontent.com/71237545/115321130-38111a80-a151-11eb-9e83-49496782bec1.png)

After using Hydra to brute force the default username (admin) we get the password

![image](https://user-images.githubusercontent.com/71237545/115321149-3e9f9200-a151-11eb-9a91-02b5b260246e.png)

With the password we check the jenkins.
We look into the jenkins site and we go to the console (this one runs on Groovy(java))

![image](https://user-images.githubusercontent.com/71237545/115321168-46f7cd00-a151-11eb-8f33-26777772746a.png)

So we look for a reverse shell on that language and we run it in the console
This will give us the remote session!

# SECOND ESCALATION

![image](https://user-images.githubusercontent.com/71237545/115321177-4eb77180-a151-11eb-97b8-192c2d11ff16.png)

After we look into the server files, we can find a interesting note under /opt

![image](https://user-images.githubusercontent.com/71237545/115321213-5bd46080-a151-11eb-8d76-562186ab3f9f.png)

Using cat we can get the root credentials.

![image](https://user-images.githubusercontent.com/71237545/115321233-64c53200-a151-11eb-9e3d-b9b775836f44.png)

With this we go back to our ssh or just create a new one with the root credentials.

![image](https://user-images.githubusercontent.com/71237545/115321243-6abb1300-a151-11eb-8ea3-ad583f522886.png)

And with this we get the second flag and we clear the room.
