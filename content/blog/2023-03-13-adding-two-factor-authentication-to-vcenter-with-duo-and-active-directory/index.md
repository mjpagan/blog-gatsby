---
layout: blog
title: Adding Two Factor Authentication to vCenter with Duo and Active Directory
date: 2023-03-13T04:42:08.016Z
img: duo-ad.jpg
tags:
  - vmware
  - security
  - infrastructure
---

## Summary

Adding multi-factor authentication you any service will make it instantly more secure. While it's not built into vCenter, you can add it easily by signing up for a free Duo account and setting up a Linux server.

## Layout

![duo-vcenter-mfa-01.png](/img/duo-vcenter-mfa-01.png)

Here are the components needed to make this work:

- vCenter Server
- Active Directory domain
- Duo Free account
- Linux server (or Windows)
- Domain account for lookups

## Plan

- [ ] Sign up for a free Duo account (10 users for free)
- [ ] Configure Duo to authenticate an application
- [ ] Create an Active Directory user to connect vCenter to the domain
- [ ] Configure Duo user(s)
- [ ] Deploy a Linux server
- [ ] Install Duo Proxy software on a Linux server
- [ ] Configure Duo Proxy software
- [ ] Configure vCenter to use Duo Proxy
- [ ] Test!
- [ ] Wrap-up

## Sign up for Duo Free account

Go to https://duo.com/editions-and-pricing/duo-free and sign up for a free account. 

![Duo Free Trial Signup](/img/duo-vcenter-mfa-12.png)

## Configure Duo to authenticate an application

In the Duo portal, in the left-hand menu bar click **Applications** > **Protect an Application**

![Protect an Application](/img/duo-vcenter-mfa-10.png)

On the **Protect and Application** page, type VMware in the search box, then **Protect**

![Protect an Application](/img/duo-vcenter-mfa-09.png)

Take note of your Integration key, Secret key, and API hostname, they will be used to configure your Duo Application Proxy software.

![Duo Application Settings](/img/duo-vcenter-mfa-08.png)

## Create an Active Directory user to connect vCenter to the domain

Create a domain user in your domain for vCenter to talk to the domain. It does not need any additional roles or permissions other than a standard user.

Collect the distinguished name of the account as you will need it later.

![Account Distinguished Name](/img/duo-vcenter-mfa-14.png)

## Configure Duo user

Add your vCenter admin user(s) and enroll them with the app or text messages.

> If your internal domain is different than your public domain (domain.local vs domain.com for example), then add the internal username as an alias on your account.

![Duo Alias](/img/duo-vcenter-mfa-11.png)

## Create a Linux server VM

I created a new virtual machine running Ubuntu server 20.04. If you need help with setting up a Linux server, check out these links:

- https://adamtheautomator.com/install-ubuntu/
- https://it-infrastructure.solutions/how-to-set-up-ubuntu-20-04-3-server-amd64-in-a-virtualbox/

## Install Duo Proxy software

On the Linux server, download the Duo Proxy software. 

> The version number at the end of the file name will number will change over time and the current version can be found here: https://duo.com/docs/checksums#duo-authentication-proxy

``` bash
wget https://dl.duosecurity.com/duoauthproxy-5.8.0-src.tgz
```

Extract the installer:

``` bash
tar xzf duoauthproxy-5.8.0-src.tgz
```

Now build the application:

``` bash
cd duoauthproxy-5.8.0-src/duoauthproxy-build
sudo ./install
```

## Configure Duo Proxy software

Edit the authproxy.cfg file:

``` bash
sudo vi /opt/duoauthproxy/conf/authproxy.cfg
```

Here is is my config (sensitive info masked):

``` bash
; Complete documentation about the Duo Auth Proxy can be found here:
; https://duo.com/docs/authproxy_reference

; NOTE: After any changes are made to this file the Duo Authentication Proxy
; must be restarted for those changes to take effect.

; MAIN: Include this section to specify global configuration options.
; Reference: https://duo.com/docs/authproxy_reference#main-section
;[main]


; CLIENTS: Include one or more of the following configuration sections.
; To configure more than one client configuration of the same type, append a
; number to the section name (e.g. [ad_client2])

[ad_client]
host=dc01.vcraftsman.local
service_account_username=vc_ldap
service_account_password=<PASSWORD HERE>
search_dn=DC=vcraftsman,DC=local
port=389

; SERVERS: Include one or more of the following configuration sections.
; To configure more than one server configuration of the same type, append a
; number to the section name (e.g. radius_server_auto1, radius_server_auto2)

[ldap_server_auto]
ikey=11111111111111111111
skey=2222222222222222222222222222222222222222
api_host=api-33333333.duosecurity.com
failmode=safe
client=ad_client
port=389
exempt_primary_bind=false
exempt_ou_1=CN=vc_ldap,OU=Service-Accounts,DC=vcraftsman,DC=local

"/opt/duoauthproxy/conf/authproxy.cfg" 35L, 1229B                                           
```

> I originally thought the exempt_primary_bind and exempt_ou_1 lines were not necessary, but it did not work without them. After researching, those lines allow the service account to bypass the need for multi-factor authentication.
>
> https://help.duo.com/s/article/4989?language=en_US

Once you are done editing, save your config and restart the service:

``` bash
sudo /opt/duoauthproxy/bin/authproxyctl restart
```

## Configure vCenter to use Duo Proxy

First, configure the vCenter for LDAP authentication. If you have already have your vCenter using Active Directory for authentication, 

Login to your vCenter with your SSO admin account (typically administrator@vsphere.local) and configure a new authentication domain.

Open the **Administration** menu

![vCenter Administration](/img/duo-vcenter-mfa-16.png)

Open the **Configuration** menu

![vCenter Administration Configuration](/img/duo-vcenter-mfa-15.png)

Add a new Identity Provider

![New Identity](/img/duo-vcenter-mfa-07.png)

Enter the information to match your environment, save your settings

![LDAP Settings](/img/duo-vcenter-mfa-04.png)

After the Identity Source is configured, we need to assign a role to a domain user or preferably a domain group. Open the Global Permissions menu.

![Global Permissions](/img/duo-vcenter-mfa-17.png)

Click Add

![Add Global Permissions](/img/duo-vcenter-mfa-03.png)

Use the drop-down to choose your Active Directory domain, search for the name of your user or group, select the role (typically Administrator), check the box for Propagate to children, then click OK.

![Add Permission](/img/duo-vcenter-mfa-02.png)

Now open a browser in "private" mode and try to log in with your Active Directory account. If that doesn't work, troubleshoot your identity settings.

When the Active Directory login works correctly, edit your Identity Source and change your **Connect to** setting to point at your Duo Proxy server.

![LDAP from Duo Proxy](/img/duo-vcenter-mfa-05.png)

## Testing authentication

Open a new window in private mode or another browser and login to your vCenter with domain creds.

You should get a push notification to your Duo enrolled device

If you don't or it times out, try restarting the duoauth service

``` bash
sudo /opt/duoauthproxy/bin/authproxyctl restart
```

To review the login process run the tail command on the auth logs

``` bash
sudo tail /opt/duoauthproxy/log/authproxy.log -f
```

## Wrap-up

Hopefully, you have been able to add mutli-factor authentication to your vCenter server for your domain logins. Remember that your default administrator account, administrator@vsphere.local is not secured with mutli-factor authentication, so make sure that password is strong (long and complex) and store it in a password vault if you ever need it.

References:

- https://www.virtualizationhowto.com/2021/12/easy-vcenter-server-two-factor-authentication-without-adfs/
- https://duo.com/docs/authproxy-overview










