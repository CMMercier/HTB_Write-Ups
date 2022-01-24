# Information

Name: Forge

OS: Linux

Difficulty: Medium

Points: 0 (Retired)

## Recon

![image](https://user-images.githubusercontent.com/43668197/150809394-c7fd83c5-e3b4-4212-839a-3625e1eb4beb.png)

Using Rustscan and Nmap came back with different results. We can't really do anything with the filtered port but it's nice to know that it is there. I wasn't able to find a way 
to make Rustscan discover this filtered port that Nmap had so I may default back to using nmap. Or just keep trying them both out like I have been.

So here we have 3 open ports. The 2 standard ports on 22:ssh and 80:http and a filtered ftp port on 21. Again we cannot do anything on the filtered port so it is time to check 
out the only option, the website.

![image](https://user-images.githubusercontent.com/43668197/150812506-ff05cd3c-2d53-4535-a21f-a29272249914.png)
![image](https://user-images.githubusercontent.com/43668197/150812599-c7f4001d-b579-48f5-8954-79676db244bb.png)

Immediately we notice the option to `Upload an image`. Trying to upload a php reverse shell comes back with:

![image](https://user-images.githubusercontent.com/43668197/150814615-cef39dac-121d-4cf1-bf4e-b38378117b04.png)

Trying again with curl shows our script is there but it wasn't run. Maybe this server isn't running php. Running a netcat listener and submitting my own ip on the site shows that 
this is infact running Python.

Running ffuf immediately comes back with an interesting subdomain:

![image](https://user-images.githubusercontent.com/43668197/150817737-30142a0f-f2c5-4099-b573-8e8716981263.png)

Adding this new discovery to /etc/hosts and attempting to visit it gives us this message:

![image](https://user-images.githubusercontent.com/43668197/150818137-6bf39a6c-b989-46f4-b305-3824033fa7ca.png)

Attempting to upload a file from url with the url `http://admin.forge.htb` tells us this is blacklisted.

![image](https://user-images.githubusercontent.com/43668197/150818622-734b1385-bbfe-4635-8876-cb046bf63ca8.png)

I was able to bypass this silly blacklist by simply using `http://ADMIN.FORGE.HTB` instead. Using curl on the link it provides reveals another directory `/announcements`.

![image](https://user-images.githubusercontent.com/43668197/150819024-29e067c9-c7a0-4833-9a26-82d08f988b3c.png)

Now to get a new link by uploading from the url `http://ADMIN.FORGE.HTB/announcements` and using curl on that. 

![image](https://user-images.githubusercontent.com/43668197/150819344-2a19e652-a23c-4e98-8ca6-02da6e5a200c.png)

We get the internal ftp server credentials of `user:heightofsecurity123!`. Seems filtered ports aren't always useless to know about but we would have found out about it this 
way even if I hadn't used nmap.

Time to try `http://ADMIN.FORGE.HTB/upload?u=ftp://user:heightofsecurity123!@ADMIN.FORGE.HTB` and curl that url responce. And we get the directory with user.txt so all we have 
to do to get the flag from user.txt is to add it to the end of the url. `http://ADMIN.FORGE.HTB/upload?u=ftp://user:heightofsecurity123!@ADMIN.FORGE.HTB/user.txt`

![image](https://user-images.githubusercontent.com/43668197/150821182-330a26b7-f9e1-43e1-b480-aa7c6c027d5e.png)

![image](https://user-images.githubusercontent.com/43668197/150821295-c4bf2cd0-82d7-45be-b2aa-09637f107d00.png)

## Initial Foothold

To get the initial foothold onto the system all we have to do now is upload from the url again but this time to
`http://ADMIN.FORGE.HTB/upload?u=ftp://user:heightofsecurity123!@ADMIN.FORGE.HTB/.ssh/id_rsa`

![image](https://user-images.githubusercontent.com/43668197/150821824-1f7a5c85-07ba-4858-92d6-76e1b535b27f.png)

`nano id_rsa` and paste the private key info in there and don't forget to `chmod 600 id_rsa`. And we are in!

![image](https://user-images.githubusercontent.com/43668197/150822241-ae05cc1f-53dd-42eb-adc6-835a51facf44.png)

## Privilege escalation

First thing we always do once we gain user access is to check what sudo permissions that user has.

![image](https://user-images.githubusercontent.com/43668197/150822438-60151cb2-a6b0-47e7-a97b-289e62b47281.png)

The user has sudo access to running a specific python script so time to see what this script does and how we can use it to gain root access.

![image](https://user-images.githubusercontent.com/43668197/150822867-f31e3f8c-cdf2-4b5c-bb23-f6c9d3f19aa0.png)

We get the password `secretadminpassword`. I ran the script and found that it is listening on port `48888`.

![image](https://user-images.githubusercontent.com/43668197/150823261-7513b501-da5e-4f70-b988-9fb68d303bd3.png)

![image](https://user-images.githubusercontent.com/43668197/150823655-1201fa23-6c34-458a-a2ea-aebcf647aa36.png)

This brings up a menu. If you enter invalid information into the menu it brings up a `Pdb` debugger.

![image](https://user-images.githubusercontent.com/43668197/150823907-730c8c41-56a2-4fac-830b-0a3accb740b3.png)

Now we can use Pdb debugger to get root access with the following commands:

```
(Pdb) import os
(Pdb) os.system ('chmod u+s /bin/bash')
(Pdb) exit
user@forge:~$ /bin/bash -p
```

![image](https://user-images.githubusercontent.com/43668197/150824552-09caeef4-1ffc-416a-8379-379d27f13b4d.png)

Just cat out /root/root.txt and machine complete! This felt easier than some of the "easy" machines and I enjoyed the Web and SSRF learning involved.



