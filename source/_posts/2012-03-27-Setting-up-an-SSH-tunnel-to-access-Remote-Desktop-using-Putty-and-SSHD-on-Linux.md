---
title: "Setting up an SSH tunnel to access Remote Desktop using Putty and SSHD on Linux"
extid: posts/9345
author: johan
date: 2012-03-27 11:23:59
updated: 2012-08-05 17:28:46
slug: Setting-up-an-SSH-tunnel-to-access-Remote-Desktop-using-Putty-and-SSHD-on-Linux
tags: [ssh, ubuntu, windows 7, networking]
---

 In [Approach](http://approach.se), we have servers (ya, rly!). From time to time I need to access them. Usually, I do this by connecting to our <abbr title="Virtual Private Network">VPN</abbr>. At times, however, that is not possible, like for instance when some overzealous firewall administrator has blocked outgoing [PPTP](http://en.wikipedia.org/wiki/PPTP "Point-to-Point Tunneling Protocol"). Then you might have the need for an alternate approach, like using an [SSH](http://en.wikipedia.org/wiki/Secure_Shell "Secure Shell") tunnel (provided that the administrator didn’t block outgoing SSH as well…)

 And yes, I am aware that there are plenty of guides how to setup an SSH tunnel with Putty, but I found that they are either 1) overly verbose, 2) not exactly describing my problem or 3) wrong.

## 
	The situation

*   I want to access a Windows Server using Remote Desktop
*   There is a Linux server running in the same network section. Mine is running Ubuntu, so all examples will be in <abbr title="A word I made up">Ubuntuish</abbr>.
*   I have a Windows 7 client machine, with the Putty ssh client

## 
	On the Linux server

1.  Make sure that SSH is installed (it probably is, otherwise, how do you access the server?). If not, `$ sudo apt-get install ssh`.
2.  Edit /etc/ssh/sshd_config.  

		Check that it says AllowTcpForwarding yes somewhere. If not, add it.
3.  Reload sshd.  

		`$ sudo /etc/init.d/sshd reload` or  

		`$ sudo service ssh reload` depending on your Ubuntu version.

## 
	On the Windows client

1.  If you don’t have Putty yet, get it.
2.  Configure putty to connect to your Linux server using SSH 2.
3.  Under Connection – SSH – Tunnels, map a local port to remote desktop on your Windows server. Usually, the remote port is 3389. The local port can be anything, except 3389 or 3390, which won’t work*.  

		[![Configuration of SSH Tunnels in putty](/images/Windows-Live-Writer/Setting-up-an-SSH-tunnel-to-access-Remot_953E/putty-ssh-tunnel-setup_thumb.png "You only need to enter the source port and the destination. Leave everything else as you found it")](/images/Windows-Live-Writer/Setting-up-an-SSH-tunnel-to-access-Remot_953E/putty-ssh-tunnel-setup.png)
4.  Save the session and connect to your linux server. You need to log on, and keep the connection open.
5.  Open a remote desktop session to the source port on localhost.  

		[![Opening a new Remote Desktop connection](/images/Windows-Live-Writer/Setting-up-an-SSH-tunnel-to-access-Remot_953E/connecting-with-remote-desktop_thumb.png "Our domain isn't really example, and my username is not username.")](/images/Windows-Live-Writer/Setting-up-an-SSH-tunnel-to-access-Remot_953E/connecting-with-remote-desktop.png)
6.  Profit.

 * Because 3389 is probably already running a RDP server, and 3390 is blocked in Windows 7 for some reason.

<div class="wlWriterEditableSmartContent" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:b2349a68-494a-4695-be16-b58b07783cf8" style="margin: 0px; padding: 0px; float: none; display: inline;">
	Tags: [ssh](/tags/ssh), [ubuntu](/tags/ubuntu), [windows 7](/tags/windows+7), [networking](/tags/networking)</div>
