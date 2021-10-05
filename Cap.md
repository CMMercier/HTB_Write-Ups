# Information

Name: Cap

Difficulty: Easy

Description: IDOR, Python, Linux, Web, Network, SUID

## Recon

**Search for open ports using nmap.**

![image](https://user-images.githubusercontent.com/43668197/136067823-8a60d99c-a009-4bca-868a-737e92848cfa.png)

The open FTP looks interesting so I check it out first to see if it allows anonymous connection. No luck there so it's time to check out the website.

![image](https://user-images.githubusercontent.com/43668197/136068484-70bc41dc-87ab-4250-8665-715a92a8936d.png)

![image](https://user-images.githubusercontent.com/43668197/136069514-b351c2f8-5e6b-42a7-a37e-476d9b2d77e9.png)

Looking at the menu options we find a PCAP analysis section. Making this most likely a Wireshark related challenge.

![image](https://user-images.githubusercontent.com/43668197/136069831-c860cee9-a623-4b07-acf6-2b6e45ca0927.png)

Initially it sends us to page 8 where there is no data but checking 0-7 has some interesting finds. 0 has the most captures by far so I downloaded this one first and checked it
out in Wireshark.

![image](https://user-images.githubusercontent.com/43668197/136070284-3e2277e5-b53e-422b-8b5d-5c6e04df3ab8.png)

Filtering the request to FTP quickly reveals login information for `nathan` with the password `Buck3tH4TF0RM3!`.

# Exploitation

![image](https://user-images.githubusercontent.com/43668197/136070623-ed9045a6-3dfd-4e84-aa67-07642b94977d.png)

Logging into FTP as nathan reveals just one file, the user.txt file which is our first flag.

![image](https://user-images.githubusercontent.com/43668197/136070769-3b6a0b6b-456c-47c4-8b82-18e0ca87c688.png)

Next was to try using nathans credentials on the ssh and with success we are in!

![image](https://user-images.githubusercontent.com/43668197/136071676-dcefae2c-2e69-488b-92b1-3cea8359cd66.png)

# Priv Esc

Now we need to get root and get that flag. The machine names are usually a hint toward this and in this case it is referencing `getcap`.

https://man7.org/linux/man-pages/man8/getcap.8.html

![image](https://user-images.githubusercontent.com/43668197/136072301-d7af3855-60b5-409f-93ed-a5173083bd87.png)

We've got Python here so its time to head to GTFObins and see if there is a `capabilities` option for Python and yes there is!

![image](https://user-images.githubusercontent.com/43668197/136072506-1d4782dc-0408-4c7a-aa22-dd954aaafd35.png)

We already know the Python version so just copy the code and add 3.8.

```
python3.8 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

![image](https://user-images.githubusercontent.com/43668197/136072917-459eafd7-79bd-49fa-95ec-e3fb9dba610a.png)

...and go claim that root flag!
