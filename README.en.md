# Fixing Network Issues in WSL2

[Clique aqui para a versão em português](README.md)

## Problem

If your virtual machines in WSL2 cannot connect to the internet, there might be two main reasons for this:

1. The DNS is not working.
2. WSL2 cannot connect to the internet in general.

To test this, run the command `curl 142.250.189.206` (this is a Google server IP) inside WSL2. The output should look like this:

```
joao@NL-BD4V0N3:~$ curl 142.250.189.206
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
```

If this works, the problem is with the DNS. If it doesn't work, the issue is with network connectivity in general.

---

## Solution for General Network Issues

If neither DNS nor direct IP access is working, the problem might be related to compatibility between WSL2 and certain VPNs. To fix this, you can use the [wsl-vpnkit](https://github.com/sakai135/wsl-vpnkit) project, which resolves this issue by creating a dedicated virtual network interface for WSL2.

### How to Set Up wsl-vpnkit

1. **Download the latest release:**
   Visit the [wsl-vpnkit releases page](https://github.com/sakai135/wsl-vpnkit/releases/latest) and download the `wsl-vpnkit.tar.gz` file.

2. **Import the wsl-vpnkit distribution:**
   Open the PowerShell terminal, navigate to the directory where you downloaded the `wsl-vpnkit.tar.gz` file, and execute:
   ```
   wsl --import wsl-vpnkit --version 2 $env:USERPROFILE\wsl-vpnkit wsl-vpnkit.tar.gz
   ```
   This will create a new WSL2 distribution named `wsl-vpnkit`.

3. **Start wsl-vpnkit:**
   In the PowerShell terminal, run:
   ```
   wsl.exe -d wsl-vpnkit --cd /app wsl-vpnkit
   ```
   The process will start and should remain running. You can minimize the window or run the process in the background.

4. **Test the connection:**
   Open a WSL2 machine and check if the internet is working.

---

### Notes
1. I am using Ubuntu 24.04 on WSL2.
2. My `wsl.conf` file is empty (no configuration was necessary).

### Suggestion
Create a script or similar tool to run automatically whenever a WSL2 machine is started.

## Solution for DNS Issues
If the issue is DNS-related, you may need to adjust the `/etc/resolv.conf` file. The `resolv.conf` file that works for me was automatically created by WSL2 and contains the following content:

```
nameserver 10.9.81.238
nameserver 10.9.50.245
nameserver 172.26.21.112
```

**Tip:**
If you modify the `resolv.conf`, ensure that the `/etc/wsl.conf` file is configured to prevent the automatic replacement of `resolv.conf`. Add the following lines, if needed:

```
[network]
generateResolvConf = false
```

For more information, see the [official WSL documentation](https://docs.microsoft.com/en-us/windows/wsl/wsl-config#wslconf).
