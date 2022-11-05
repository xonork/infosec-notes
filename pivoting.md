# Port Forwarding
## [rinetd](https://github.com/samhocevar/rinetd)
It is used for redirect any traffic received to an specific port
- In its config file we have to specify the bindaddress, bindport, connectaddress and connectport:
  ```bash
  kali@kali:~$ cat /etc/rinetd.conf
  ...
  # bindadress bindport connectaddress connectport
  0.0.0.0 80 216.58.207.142 80
  ...
  
  kali@kali:~$ sudo service rinetd restart
  ```
## SSH Local Port Forwarding
### Linux
```bash
kali@kali:~$ sudo ssh -N -L 0.0.0.0:1337:10.0.0.2:80 root@10.0.0.1
```
![image](https://user-images.githubusercontent.com/43812413/199309514-9603a925-3358-4fee-b289-955c892f04ed.png)

## SSH Remote Port Forwarding
### Linux
```bash
user1@debian:~$ ssh -N -R 10.11.0.4:2221:127.0.0.1:3306 kali@10.11.0.4
```
### Windows
#### plink
```bash
 cmd.exe /c echo y | plink.exe -ssh -l kali -pw ilak -R 10.11.0.4:1234:127.0.0.1:3306 10.11.0.4
 ```


## SSH Dynamic Port Forwarding
- With the **-D** flag we can set a SOCKS4/5 application proxy
```
kali@kali:~$ sudo ssh -N -D 127.0.0.1:8080 user1@10.11.0.128

kali@kali:~$ cat /etc/proxychains.conf
...
[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
socks5 127.0.0.1 8080
```

## NETSH Port Forwarding
- Installed by default on every modern version of Windows
- Windows system must have the *IP Helper* running and *IPv6* supports must be enabled.
```bash
C:\Windows\system32> netsh interface portproxy add v4tov4 listenport=4455 listenaddress=10.11.0.22 connectport=445 connectaddress=192.168.1.110
```
- If theres is some rule in the Firewall that blocks the incoming connections to the specified port
```bash
netsh advfirewall firewall add rule name="forward_port_rule" protocol=TCP dir=in localip=10.11.0.22 localport=4455 action=allow
``````

# Tunneling
## httptunnel
- It is used when only HTTP traffic is allowed. If we have a compromised machines we can:
  1. Set-up a SSH Port Forwarding in the target machine, that forwards the traffic to another machine in its private network
    ```bash
    www-data@debian:/$ ssh -L 0.0.0.0:8888:192.168.1.110:3389 user1@127.0.0.1
    ```
  2. Set-up a HTTP Tunnel between our machine and the compromised one (the compromised one will re-forward the traffic to the port configured in the port forwarding)
    ```bash
    # at the compromised machine
    user1@debian:~$ hts --forward-port localhost:8888 1234
    
    # at our Kali
    htc --forward-port 8080 10.11.0.128:1234
    ```
    
