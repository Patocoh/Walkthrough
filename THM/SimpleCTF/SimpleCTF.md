Room: https://tryhackme.com/room/easyctf  

# Reconnaissance!  

``` sudo nmap -v -sV -sC -Pn -T4 -oG simple.nmap 10.10.166.171```  
Output of NMAP:  
![image](https://user-images.githubusercontent.com/71237545/117581643-f7f7e480-b0cb-11eb-997d-9ca558958d89.png)  
Doing a dirb to the IP you can find a CMS under /Simple/ and robots.txt  
Dirb output:  
![image](https://user-images.githubusercontent.com/71237545/117580553-6cc82000-b0c6-11eb-9190-1083bbdfdc8d.png)  
![image](https://user-images.githubusercontent.com/71237545/117580555-6f2a7a00-b0c6-11eb-90b2-4930cf34d2bd.png)  
![image](https://user-images.githubusercontent.com/71237545/117580557-718cd400-b0c6-11eb-9e0f-4e1a26351494.png)  
![image](https://user-images.githubusercontent.com/71237545/117580652-fc6dce80-b0c6-11eb-8313-171d51b4cc65.png)  
Robots.txt content:  
![image](https://user-images.githubusercontent.com/71237545/117580566-7cdfff80-b0c6-11eb-913b-9b4431fd9961.png)  
Looking at the /simple/index we can find a CMS and then from the dirb command we also can find the login site:  
http://simple.thm/simple/admin/login.php  
While Dirb was happening I checked the FTP with an anonymous account.  
![image](https://user-images.githubusercontent.com/71237545/117580574-88332b00-b0c6-11eb-8dfd-ab7bf5d7d195.png)  
Content of Mitch.txt:  
![image](https://user-images.githubusercontent.com/71237545/117580580-8e290c00-b0c6-11eb-89c8-265f3ff6a9b7.png)  
We start looking through the site and I found the login of the CMS at simple.thm/simple/admin/login.php  
![image](https://user-images.githubusercontent.com/71237545/117580582-91bc9300-b0c6-11eb-8305-72105d936a54.png)  
Looking on the site for any vuln for the version (2.2.8)(we can get it from the index, scrolling down).  
I found the following cve: https://www.exploit-db.com/exploits/46635  
Trying to re make it available for python3 took a time but luckily we can execute it and get the hash  
``` Python3 [file] -u http://simple.thm/simple --crack -w /usr/share/seclists/Passwords/Common-Credentials/best110.txt ```  
``` 
 [+] Salt for password found: 1dac0d92e9f-----   
 [+] Username found: -----   
 [+] Email found: -----@-----.com   
 [+] Password found: 0c01f4468bd75d7a84c7eb73846-----   
 [+] Password cracked: ----! 
```  
With this credentials we can login on the SSH  
``` ssh -----@simple.thm -p 2222 ```  
![image](https://user-images.githubusercontent.com/71237545/117580983-68046b80-b0c8-11eb-9236-421c87206ae9.png)  
With this we can get the user flag.  
# Priv Esc !  
Checking for interesting information.  
![image](https://user-images.githubusercontent.com/71237545/117581027-b1ed5180-b0c8-11eb-825b-de4934653b66.png)  
With this we know we can use vim to priv esc from GTFOBin !  
https://gtfobins.github.io/gtfobins/vim/  
![image](https://user-images.githubusercontent.com/71237545/117581115-350ea780-b0c9-11eb-82b8-74ea43bc9f81.png)  
![image](https://user-images.githubusercontent.com/71237545/117581117-37710180-b0c9-11eb-90c4-626e7b57640d.png)  
With this we can look for the final flag under /root  
``` ls /root/ ```  
![image](https://user-images.githubusercontent.com/71237545/117581141-540d3980-b0c9-11eb-9f63-324aaee77679.png)  
