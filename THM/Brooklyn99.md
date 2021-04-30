Room: https://tryhackme.com/room/brooklynninenine  
# Reconnaissance  
NMAP  
``` sudo nmap -v -sV -sC -Pn -T4 -p- -oG brooklyn.nmap 10.10.152.156```  

![image](https://user-images.githubusercontent.com/71237545/116751882-a74d0100-a9d2-11eb-98c6-48c2eccc9efb.png)  
Downloading the image from the site, we can try to  stegcracker after we realize it has some info  

``` stegcracker brooklyn99.jpg /usr/share/wordlists/rockyou.txt ```  

![image](https://user-images.githubusercontent.com/71237545/116751852-a025f300-a9d2-11eb-94e0-072de3f61608.png)  
After extracting the image we get the first credentials.  
![image](https://user-images.githubusercontent.com/71237545/116751825-98fee500-a9d2-11eb-8219-8623cc5225d2.png)  
We use holt credentials to login into ssh.  
![image](https://user-images.githubusercontent.com/71237545/116751807-943a3100-a9d2-11eb-9936-b0e6f7680f74.png)  
# Priv Escalation  
Now we look for interesting files.  
![image](https://user-images.githubusercontent.com/71237545/116751772-8684ab80-a9d2-11eb-90c1-eac67a388c67.png)  
Checking sudo -l we see we can execute nano with sudo.  
And we check on GTFOBins  
![image](https://user-images.githubusercontent.com/71237545/116751787-8b495f80-a9d2-11eb-83a5-e134e50a50f4.png)  
And after doing this we got root.  
![image](https://user-images.githubusercontent.com/71237545/116751739-7c62ad00-a9d2-11eb-8a57-66d0aa596494.png)  
