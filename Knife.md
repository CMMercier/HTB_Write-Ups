# Information

Name: Knife

Difficulty: Easy

Description: PHP, GTFOBin, Backdoor

## Recon

**Search for open ports using nmap.**

![image](https://user-images.githubusercontent.com/43668197/133293023-6e852170-54a1-4bfb-a673-c088e45d5729.png)

There is two ports open but the one we are interested in because its most likely to be our way in is port 80. The SSH port 22 is generally used 
after we discover a username and password.

I set a few scans to run while I have a look around the website page and source code: a more agressive nmap scan and a gobuster scan.

![image](https://user-images.githubusercontent.com/43668197/133294504-ed7fcc57-b59e-4baa-89b6-5948b6d5ac8e.png)

![image](https://user-images.githubusercontent.com/43668197/133296174-f6f7dd55-678c-41fc-bfdc-3886c0087cec.png)

The gobuster scan doesnt turn up anything interesting but the aggressive nmap scan shows some supporting methods: GET HEAD POST and OPTIONS.

![image](https://user-images.githubusercontent.com/43668197/133296579-9e31ebff-3eca-4109-b58f-ace29c81ac70.png)

# Exploitation

A search on PHP 8.1.0-dev returns a possible RCE exploit.

https://www.exploit-db.com/exploits/49933

I read through the script to see if theres any fields I need to edit and to try to get an understanding of what the script does. Theres nothing to edit here
so its time to run the script.

![image](https://user-images.githubusercontent.com/43668197/133299859-48da183b-e2e4-4861-95b8-925d2cde7039.png)

Success! We are in and have access to the user james and can grab the user flag from cat /home/james/user.txt.

# Priv Esc

First thing we want to check is what if any sudo options this user has.

![image](https://user-images.githubusercontent.com/43668197/133300538-28c334db-3e1d-4bef-ae2b-a528b52d1221.png)

Go figure its knife (as machine names usually have something to do with the exploits)

My first stop is https://gtfobins.github.io/gtfobins/knife/#sudo to learn that all we need to execute is sudo knife exec -E 'exec "/bin/sh"'. However
the backdoor exploit is rather unstable and gives us an error. So we need a reverse shell. Luckly one is provided for us at 
https://github.com/flast101/php-8.1.0-dev-backdoor-rce.

![image](https://user-images.githubusercontent.com/43668197/133301628-9a3efda8-a4e2-4dc0-a122-226ba4684af1.png)

![image](https://user-images.githubusercontent.com/43668197/133301651-5f679674-1963-40fd-b217-7563ef79a4e1.png)

Now we can just execute the command and it will work.

![image](https://user-images.githubusercontent.com/43668197/133301765-82871a13-5bf9-4800-8ac9-439d41499de4.png)

and we can cat root/root.txt


