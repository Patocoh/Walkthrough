https://tryhackme.com/room/relevant
 
#Reconnaissance  
Nmap: 
``` sudo nmap -v -sV -sC -Pn -T4 -p- -oG relevant.nmap relevant.thm```  
![image](https://user-images.githubusercontent.com/71237545/116306484-1fb58700-a773-11eb-8dc4-9cd58620db31.png)  
SMBmap:  
```smbmap -H relevant.thm -u guest ```  
![image](https://user-images.githubusercontent.com/71237545/116306511-26dc9500-a773-11eb-8f78-c17da308758d.png)  
SBMBclient:  
```smbclient //relevant.thm/nt4wrksv -u guest```  
![image](https://user-images.githubusercontent.com/71237545/116306527-2a701c00-a773-11eb-9e21-65a04127ed83.png)  
Passwords.txt  
![image](https://user-images.githubusercontent.com/71237545/116306566-3825a180-a773-11eb-8e2b-603246fdd783.png)  
Passwords decoded  
![image](https://user-images.githubusercontent.com/71237545/116306590-3f4caf80-a773-11eb-8f7d-d1b617f8e054.png)
![image](https://user-images.githubusercontent.com/71237545/116306595-41167300-a773-11eb-8928-c9b32d017ff7.png)  
Remmina for remote access didn't work.  
I used dirsearch.py to check the folder of the smb.  
```sudo python3 /opt/dirsearch/dirsearch.py -u http://relevant.thm:49663/ -e txt,php,sh,exe,jsp,js,aspx,html,war,axe -x 400,500 -r -t 50 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt```  
![image](https://user-images.githubusercontent.com/71237545/116306647-54c1d980-a773-11eb-8352-368267d440ed.png)  
This proves we can go to the url of the share.  
Once we realize we can upload files to the share I created a payload with msfvenom and uploaded it.  
![image](https://user-images.githubusercontent.com/71237545/116306678-60150500-a773-11eb-9343-15b364394ae5.png)  
Then In other terminal I use Curl.  
![image](https://user-images.githubusercontent.com/71237545/116306702-67d4a980-a773-11eb-9638-77eb56d273ba.png)  
![image](https://user-images.githubusercontent.com/71237545/116306713-6b683080-a773-11eb-9798-32beb734f291.png)  
Looking for user.txt  
Content user.txt:  
![image](https://user-images.githubusercontent.com/71237545/116307050-d6b20280-a773-11eb-9589-dc31c24435c3.png)  
After that I uploaded winpeas to check what is inside the machine.  
![image](https://user-images.githubusercontent.com/71237545/116306750-78851f80-a773-11eb-96d3-3598cbf4bad1.png)  
And run it.  
While winpeas run, I checked privileges  
![image](https://user-images.githubusercontent.com/71237545/116306780-8175f100-a773-11eb-8ecb-4d880d121bc7.png)  
Looking on internet for any vuln for this types of privileges. I found printspoofer.exe  
![image](https://user-images.githubusercontent.com/71237545/116306800-88046880-a773-11eb-982b-8de71099be6f.png)  
![image](https://user-images.githubusercontent.com/71237545/116306806-89ce2c00-a773-11eb-92d8-ea691f8f427f.png)  
So I uploaded again and run it with the commands.  
![image](https://user-images.githubusercontent.com/71237545/116306862-9bafcf00-a773-11eb-90ea-b774cf0c5cb3.png)  
Looking for the second flag on desktop, we find it.  
![image](https://user-images.githubusercontent.com/71237545/116306874-9e122900-a773-11eb-988c-e70701000802.png)  
Content of root.txt.  
![image](https://user-images.githubusercontent.com/71237545/116306930-af5b3580-a773-11eb-8ca9-d466028d6348.png)  
