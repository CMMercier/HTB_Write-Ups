# Information

Name: Driver

OS: Windows

Difficulty: Easy

Points: 20

## Recon

![image](https://user-images.githubusercontent.com/43668197/150546941-08b372d9-ed72-456f-bf9d-6ea2ed1f820d.png)
![image](https://user-images.githubusercontent.com/43668197/150547055-248a37da-1658-457d-b8a5-6c85327ecb69.png)

Initial scanning reveals four open ports. Two `http` ports at 80 and 5985 and `SMB` at ports 135 and 445.

Heading to the website on port 80 asks for login credentials. Quickly trying out a couple of the basic defaults gets us logged in with `admin:admin`.

The only link we can access gives us the ability to upload a file. 

![image](https://user-images.githubusercontent.com/43668197/150548024-0689e550-211b-4e2e-a93c-6cc591a065f5.png)

But I wasn't able to find a way to access the shell after upload. After beating my head against the wall for some hours I gave in and took a look at a guide where I 
found out about https://pentestlab.blog/2017/12/13/smb-share-scf-file-attacks/.

![image](https://user-images.githubusercontent.com/43668197/150560990-19b8579b-969d-496a-bbed-1720b2fc47de.png)
![image](https://user-images.githubusercontent.com/43668197/150561070-c7a071dc-865c-4ece-9cbf-3e2f66381671.png)

This normally requires a user to browse the file still so if I hadn't seen the guide I would have probably written off the idea of it being the direction we were supposed to go.
But still to my surprise it worked instantly and we now have the hash of user `tony`!

![image](https://user-images.githubusercontent.com/43668197/150562245-5f735814-ac30-48fc-8dcb-4119b95e7c42.png)

Using `hashcat -m 5600 hash.txt lists/rockyou.txt --force` I was able to crack the password as `liltony`. 

## Initial Foothold

Next I try to connect via `Evil-WinRM`. 

![image](https://user-images.githubusercontent.com/43668197/150563306-e6d1c0e1-895c-49b6-bd86-39b5933533dc.png)

And just like that we are in as the user `tony`. The `user.txt` file is on tony's desktop and can be read by using `type user.txt`

Now on to attempting to get root access. 

## Privilege escalation

The vulnerability we must exploit to gain root privileges on this system is PrintNightmare. There is an easy Python script for doing this located at https://github.com/cube0x0/CVE-2021-1675.

Create the dll file.

![image](https://user-images.githubusercontent.com/43668197/150573285-d2687322-6309-4044-8971-0c71c0414299.png)

Install their version of impacket and run it.

![image](https://user-images.githubusercontent.com/43668197/150573357-0483fcbe-09f6-40e6-b965-2a9c36af4057.png)
![image](https://user-images.githubusercontent.com/43668197/150573433-060131d8-b7f0-4da2-b75b-356a79b6f310.png)

Run the exploit.

![image](https://user-images.githubusercontent.com/43668197/150573556-b92bf010-eae8-4efa-be55-f080518bb38d.png)

And go get the root.txt flag in the Administrator users desktop!

![image](https://user-images.githubusercontent.com/43668197/150573722-70aff538-9fca-47d8-bf19-31bad0ebc28a.png)

