
# Fixing Network Issues in WSL2

[Click here for the Portuguese version](README.pt.md)

## Problem

If your virtual machines in WSL2 cannot connect to the internet, there are two main possible causes:

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

If this works, the problem is with the DNS. If it doesn't work, the issue is with the network in general.

---

## Solution for DNS Issues

If the problem is DNS-related, you may need to adjust the `/etc/resolv.conf` file. The `resolv.conf` file that works for me was automatically created by WSL2 and has the following content:

```
nameserver 10.255.255.254
search neon.local
```

If it doesn't work, here is another configuration that was automatically generated for a colleague here at Neon, this one also works:

```
nameserver 10.9.81.238
nameserver 10.9.50.245
nameserver 172.26.21.112
```

**Tip:**
Make sure the `/etc/wsl.conf` file is configured to prevent the automatic replacement of `resolv.conf`. Add the following lines if necessary:

```
[network]
generateResolvConf = false
```

For more details, see the [official WSL documentation](https://docs.microsoft.com/en-us/windows/wsl/wsl-config#wslconf).

---

## Solution for Network Issues

If neither DNS nor direct IP access is working, the problem might be related to compatibility between WSL2 and certain VPNs. To fix this, you can use the [wsl-vpnkit](https://github.com/sakai135/wsl-vpnkit) project, which resolves this issue by creating a dedicated virtual network interface for WSL2.

### How to Set Up wsl-vpnkit

1. **Download the latest release:**
   Go to the [wsl-vpnkit releases page](https://github.com/sakai135/wsl-vpnkit/releases/latest) and download the `wsl-vpnkit.tar.gz` file.

2. **Import the wsl-vpnkit distribution:**
   Open PowerShell terminal, navigate to the directory where you downloaded the `wsl-vpnkit.tar.gz` file and run:
   ```
   wsl --import wsl-vpnkit --version 2 $env:USERPROFILE\wsl-vpnkit wsl-vpnkit.tar.gz
   ```
   This will create a new WSL2 distribution called `wsl-vpnkit`.

3. **Start wsl-vpnkit:**
   In PowerShell terminal, run:
   ```
   wsl.exe -d wsl-vpnkit --cd /app wsl-vpnkit
   ```
   The process will start and should remain running. You can minimize the window or run the process in the background.

4. **Test the connection:**
   Open a WSL2 machine and check if the internet is working.

---

## Notes

1. I am using Ubuntu 24.04 on WSL2.
2. My `wsl.conf` file is empty (no configuration was necessary).

## Suggestion

Create a script or similar to run automatically whenever a WSL2 machine is started.
