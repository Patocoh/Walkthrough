Room: https://tryhackme.com/room/bruteit

We start with a NMAP to the IP and in case it has any http protocol, we go for the web enumeration

#Reconnaissance

NMAP:

```sudo nmap -v -sV -sC -Pn -T4 -p- -oG Bruteit.nmap 10.10.79.84```

Output of NMAP:

![image](https://user-images.githubusercontent.com/71237545/115155477-1aa75800-a04e-11eb-9af3-4a0d365f4845.png)

Now we saw a web port we continue with Dirb, DirSearch or gobuster.

```dirb http://10.10.79.84/ /usr/share/wordlists/dirb/common.txt```

Output of Dirb:

![image](https://user-images.githubusercontent.com/71237545/115155474-1844fe00-a04e-11eb-8085-76fc2f18238c.png)

With this two outputs we can answer all the questions from task 2

Under the secret directory. We check the source code to find something interesting.

![image](https://user-images.githubusercontent.com/71237545/115155464-111df000-a04e-11eb-9e2b-167e9cd05a4e.png)

With this we can try to brute force it, based on the lack of information from the login site.
We use hydra to brute force it. Based on how the method is made we can see how to make our command.

Then we launch out brute force attack.

![image](https://user-images.githubusercontent.com/71237545/115155459-0c593c00-a04e-11eb-9811-9a613af62439.png)

```hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.79.84 http-form-post "/admin/:user=^USER^&pass=^PASS^&form=%2F&Submit=Sign+in:invalid"```
Note: you have to be aware the "invalid" text at the end is based on the response you get from the site in case of a bad login.

Output of Hydra:

![image](https://user-images.githubusercontent.com/71237545/115155452-06fbf180-a04e-11eb-89dd-832aa31da473.png)

Now we are in! and we can get 2 answers for task 3.

Web flag: THM{brut3_f0rce_is_e4sy}

Now we take the RSA from the site and we proceed to crack it.

![image](https://user-images.githubusercontent.com/71237545/115155447-04010100-a04e-11eb-9e6e-5a9dcc14be35.png)

Our friend John the ripper can helps us here.

We have to make that rsa key a hash to  crack and for this we use John

```john /usr/share/john/ssh2john.py rsa > john.hash ```

This will give us the hash to decrypt.

```john john.hash --wordlist=/usr/share/wordlists/rockyou.txt ```

Output of John:

![image](https://user-images.githubusercontent.com/71237545/115155444-006d7a00-a04e-11eb-99c7-954300e336e1.png)

Now we got the hash we can go in with SSH!

To do this we add the permissions to the rsa.
```chmod 400 rsa```
```ssh -I rsa john@10.10.79.84```
This will ask for the passphrase for the key… and then ! 

![image](https://user-images.githubusercontent.com/71237545/115155442-fe0b2000-a04d-11eb-8550-cc85c4ad21c2.png)

#Escalation

As soon as we enter we can check the files and get the last answer for task 3.
User.txt

Now we check for interesting files.

Just from the comand ``` sudo -l``` we know we can run cat with privileges

![image](https://user-images.githubusercontent.com/71237545/115155438-fb102f80-a04d-11eb-8fbc-2a4c2f7797cd.png)

With this we can get the last flag using GFTOBins. With this we execute the following.

![image](https://user-images.githubusercontent.com/71237545/115155435-f9466c00-a04d-11eb-9fc6-9bd87b87cdd5.png)

And after we get the root hash we can crack it like we did before.

![image](https://user-images.githubusercontent.com/71237545/115155434-f6e41200-a04d-11eb-91f0-932896cf6528.png)

Then we get the password of root. And the answer for the first question of task4.

![image](https://user-images.githubusercontent.com/71237545/115155426-f3e92180-a04d-11eb-9ad9-63e3fd98370e.png)

With this we can login as root

```su```

![image](https://user-images.githubusercontent.com/71237545/115155418-ef246d80-a04d-11eb-90e1-2fa5596dc5e4.png)

![image](https://user-images.githubusercontent.com/71237545/115155415-ed5aaa00-a04d-11eb-92eb-e0f68d9d01c4.png)

With this we get the last flag for task 4.
