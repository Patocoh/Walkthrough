Room: https://tryhackme.com/room/rrootme

#ENUMERATION

After we deployed the VM, we start with an NMAP and a Dirb to the machine

```sudo nmap -v -sV -sC -Pn -T4 -p- -oG rootme.nmap 10.10.131.189```

NMAP output:

![image](https://user-images.githubusercontent.com/71237545/115132045-bb543400-9fca-11eb-8079-8251003de083.png)

Dirb output:

```dirb http://10.10.131.189/ /usr/share/wordlists/dirb/common.txt```
![image](https://user-images.githubusercontent.com/71237545/115132052-c313d880-9fca-11eb-8a38-ba1bfa469d3b.png)

From this we start enumerating the site manually.
Under the panel directory we can see a place to upload files.

![image](https://user-images.githubusercontent.com/71237545/115132055-c7d88c80-9fca-11eb-88b1-cd7165f2fa78.png)

We try to upload a picture to see what type of files we can upload.
After we upload the file index.png the site confirms it was successful and we can see it under uploads/file.

![image](https://user-images.githubusercontent.com/71237545/115132061-d030c780-9fca-11eb-8115-7458ef03879e.png)

If we try to upload a php we get the not successful upload.

![image](https://user-images.githubusercontent.com/71237545/115132063-d4f57b80-9fca-11eb-9bbb-be9e8a1c79e6.png)

#Getting the reverse Shell

Here is when we jump to BurpSuite to check any filter.
Under the BurpSuite we can check the filters for it.
Checking the filter we change the content and the type of it to set it as an image.
![image](https://user-images.githubusercontent.com/71237545/115132064-d9ba2f80-9fca-11eb-895e-dbf4c1980b82.png)
![image](https://user-images.githubusercontent.com/71237545/115132077-f3f40d80-9fca-11eb-8475-d8aa803c5ae6.png)

Success ! Now we open our listener on the port we set on our payload. And we open it via the /uploads directory.
![image](https://user-images.githubusercontent.com/71237545/115132080-f6566780-9fca-11eb-8708-5ab9fc111a7c.png)
![image](https://user-images.githubusercontent.com/71237545/115132082-fc4c4880-9fca-11eb-8e0c-6d10d6ca2e21.png)

#Looking for interesting files

Now that we're in, we set our shell stable.
```python -c 'import pty;pty.spawn("/bin/bash")'```
Now we have our basic shell. So we start looking for interesting files
Under /var/www/ we can find the first flag, User.txt

![image](https://user-images.githubusercontent.com/71237545/115132086-0110fc80-9fcb-11eb-8b5f-cdb1a77853ea.png)

Now we keep going to check for what we can do.
```Cat /etc/passwd```

![image](https://user-images.githubusercontent.com/71237545/115132090-040bed00-9fcb-11eb-9c8f-6cb207394088.png)

We can see 3 users with bash.
Under /tmp we can write, so we set linpeas.sh to check what we can find.

![image](https://user-images.githubusercontent.com/71237545/115132092-066e4700-9fcb-11eb-9a8a-1c8bcd1f1b66.png)

From linpeas.sh we can get the following interesting SUID

![image](https://user-images.githubusercontent.com/71237545/115132094-09693780-9fcb-11eb-9656-41b33055f0d2.png)

#Going for the Root!

With this we can answer the first question of the last task.
From this I lookup on gtfobins and found the following.
And after we go to that path we can execute the command and…

![image](https://user-images.githubusercontent.com/71237545/115132099-138b3600-9fcb-11eb-8c97-9e06dc93b6a2.png)

![image](https://user-images.githubusercontent.com/71237545/115132100-14bc6300-9fcb-11eb-8ef3-12140716a403.png)

And we get the last flag.

![image](https://user-images.githubusercontent.com/71237545/115132101-16862680-9fcb-11eb-93aa-5b514401026e.png)

And with this we cleared the room.


