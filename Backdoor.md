# Information

Name: Backdoor

OS: Linux

Difficulty: Easy

Points: 20

## Recon

![image](https://user-images.githubusercontent.com/43668197/150363483-e083a063-e0d6-4de0-b2fa-52e309390401.png)
![image](https://user-images.githubusercontent.com/43668197/150363577-2e11a250-5148-4849-a42b-91b1ab2e96b0.png)

The scan reveals three ports open. The usual ports 22:ssh, 80:http and an odd one at 1337:waste?.

Unusual ports are often rabbit holes on CTF machines so lets follow the normal enumeration methodology for this and check out the WordPress 5.8.1 on port 80.

We have a simple WordPress blog page. Time to do some basic enumeration on WordPress. First we check if we have access to the files at `wp-includes/`

![image](https://user-images.githubusercontent.com/43668197/150365983-6c4a93ad-f237-4612-8f06-c3432b37bc2a.png)

Looking good. Time to check for plugins at `wp-content/plugins/`

![image](https://user-images.githubusercontent.com/43668197/150366103-11b76122-481f-4598-9b4d-3712c45226da.png)

There is just one plugin here which makes checking this simple. Select the ebook-download plugin and let's check the readme.txt inside that directory to check the current 
version of that plugin.

![image](https://user-images.githubusercontent.com/43668197/150366439-3c70200c-6821-485a-b7f5-a2f633f2952e.png)

We got the version `1.1` so time to check if this specific plugin and version has an exploit. 

![image](https://user-images.githubusercontent.com/43668197/150366592-32376b35-8b69-4c7a-aadc-ce73a07cfa2e.png)
![image](https://user-images.githubusercontent.com/43668197/150366836-dc123d20-bba6-4ae4-b7fb-12c4313a06d0.png)

Indeed it does! 

Next we check what users are on the system.

![image](https://user-images.githubusercontent.com/43668197/150367247-9a7a5b66-e538-4de1-a06b-86c1ca6a31f8.png)

We have a user named `user`. Tried to get the `id_rsa` of the user but no luck. Searching `1337 waste` lead to an actual rabbit hole so I used `wfuzz` to enumerate `pid` to
figure out what is really running on that port.

![image](https://user-images.githubusercontent.com/43668197/150369383-a4925483-e0e7-4209-890e-79953b1f363f.png)
![image](https://user-images.githubusercontent.com/43668197/150369964-c216a6b9-bf27-45c6-8ee6-f2a70f6eee3b.png)

We get `gdbserver`.

## Initial Foothold

Time to load up `msfconsole` and see if we can exploit the `gdbserver` at port `1337`.

![image](https://user-images.githubusercontent.com/43668197/150370876-6fbcad28-9296-47cd-a86a-1d12aceb249d.png)

And were in as `user`. 

## Privilege escalation

Running `linPEAS` shows that it's running `GNU Screen` as root so we can switch our screen to root simply by executing:

`screen -x root/root`

![image](https://user-images.githubusercontent.com/43668197/150371498-f51c04eb-20a1-4c2d-a1a8-5e3ef1fb16b7.png)

Easy root and machine pwnd!

