# Symfonos2-walkthough

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

![Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled.png](Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled.png)

Do a detail scan on those ports

```bash
nmap -nvv -sSV -p 21,22,80,139,445 --version-intensity 9 -A [ip]
```

![Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%201.png](Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%201.png)

![Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%202.png](Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%202.png)

### Nikto scan

Use nikto to scan the target

```bash
nikto -h [ip]
```

![Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%203.png](Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%203.png)

Not much information get from nikto, try dirscan.

### Dirscan(dirb,dirbuster,dirsearch)

![Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%204.png](Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%204.png)

One path discovered.

### Try ftp anonymous login

faild

Smb connect 

![Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%205.png](Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%205.png)

Get in the path shared.

![Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%206.png](Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%206.png)

get a file named log.txt, found a username in that aeolus.

So use hydra to brute force ssh and ftp, use the rokyou.txt as usual.

![Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%207.png](Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%207.png)

Login to the ftp, nothing important found except a path unaccessble

So login to ssh, and get shell.

## privilege escape

check the version of linux on target. try a vuln found by google but failed.

![Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%208.png](Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%208.png)

![Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%209.png](Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%209.png)

check suid command.

![Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%2010.png](Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%2010.png)

"sudo -l" is banned

after a long journey, found a bak file in the /var/backup path

![Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%2011.png](Symfonos2-walkthough%200f9d7db6e73c49748c46a4d1257f3a1b/Untitled%2011.png)

3 interesting userinfo found.

not end...