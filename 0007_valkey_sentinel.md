# Testing Valkey Sentinel with Orbstack on MacOS

## Requirements
- orbstack installed
- git command-line utility

## Download the valkey-sentinel script

Download the script from the github repository and set it as executable.
```
curl -LO https://raw.githubusercontent.com/riveraja/valkey-how-tos/refs/heads/main/tools/valkey-sentinel
chmod +x valkey-sentinel
```

## Create the Valkey sentinel cluster

To create a three node sentinel cluster, edit the script on the following lines:
```
HOSTS=3
ARCH=amd64
DISTRO=rocky
VERSION=9
PREFIX=valkey
```

Initialize the cluster, this creates three virtual machines running Rocky Linux 9.
```
./valkey-sentinel initialize
Start at Tue Nov 26 15:26:28 PST 2024
Initializing nodes...
Last metadata expiration check: 0:00:07 ago on Tue 26 Nov 2024 03:27:15 PM PST.
Dependencies resolved.
================================================================================
 Package                     Arch      Version               Repository    Size
================================================================================
Installing:
 epel-release                noarch    9-7.el9               extras        19 k
 less                        x86_64    590-5.el9             baseos       161 k
 vim-enhanced                x86_64    2:8.2.2637-21.el9     appstream    1.7 M
Installing dependencies:
 dbus-libs                   x86_64    1:1.12.20-8.el9       baseos       151 k
 gpm-libs                    x86_64    1.20.7-29.el9         appstream     20 k
 python3-dateutil            noarch    1:2.8.1-7.el9         baseos       287 k
 python3-dbus                x86_64    1.2.18-2.el9.0.1      baseos       131 k
 python3-dnf-plugins-core    noarch    4.3.0-16.el9          baseos       246 k
 python3-six                 noarch    1.15.0-9.el9          baseos        36 k
 python3-systemd             x86_64    234-19.el9            baseos        82 k
 vim-common                  x86_64    2:8.2.2637-21.el9     appstream    6.6 M
 vim-filesystem              noarch    2:8.2.2637-21.el9     baseos       9.3 k
 which                       x86_64    2.21-29.el9           baseos        40 k
Installing weak dependencies:
 dnf-plugins-core            noarch    4.3.0-16.el9          baseos        36 k

Transaction Summary
================================================================================
Install  14 Packages

Total download size: 9.5 M
Installed size: 38 M
Downloading Packages:
(1/14): python3-six-1.15.0-9.el9.noarch.rpm      40 kB/s |  36 kB     00:00    
(2/14): dnf-plugins-core-4.3.0-16.el9.noarch.rp  40 kB/s |  36 kB     00:00    
(3/14): python3-dnf-plugins-core-4.3.0-16.el9.n 171 kB/s | 246 kB     00:01    
(4/14): python3-dateutil-2.8.1-7.el9.noarch.rpm 118 kB/s | 287 kB     00:02    
(5/14): less-590-5.el9.x86_64.rpm                60 kB/s | 161 kB     00:02    
(6/14): python3-systemd-234-19.el9.x86_64.rpm    38 kB/s |  82 kB     00:02    
(7/14): which-2.21-29.el9.x86_64.rpm             77 kB/s |  40 kB     00:00    
(8/14): dbus-libs-1.12.20-8.el9.x86_64.rpm      351 kB/s | 151 kB     00:00    
(9/14): python3-dbus-1.2.18-2.el9.0.1.x86_64.rp 244 kB/s | 131 kB     00:00    
(10/14): vim-filesystem-8.2.2637-21.el9.noarch.  31 kB/s | 9.3 kB     00:00    
(11/14): gpm-libs-1.20.7-29.el9.x86_64.rpm       34 kB/s |  20 kB     00:00    
(12/14): epel-release-9-7.el9.noarch.rpm         16 kB/s |  19 kB     00:01    
(13/14): vim-enhanced-8.2.2637-21.el9.x86_64.rp 243 kB/s | 1.7 MB     00:07    
(14/14): vim-common-8.2.2637-21.el9.x86_64.rpm  864 kB/s | 6.6 MB     00:07    
--------------------------------------------------------------------------------
Total                                           653 kB/s | 9.5 MB     00:14     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Installing       : gpm-libs-1.20.7-29.el9.x86_64                         1/14 
  Installing       : vim-filesystem-2:8.2.2637-21.el9.noarch               2/14 
  Installing       : vim-common-2:8.2.2637-21.el9.x86_64                   3/14 
  Installing       : dbus-libs-1:1.12.20-8.el9.x86_64                      4/14 
  Installing       : python3-dbus-1.2.18-2.el9.0.1.x86_64                  5/14 
  Installing       : which-2.21-29.el9.x86_64                              6/14 
  Installing       : python3-systemd-234-19.el9.x86_64                     7/14 
  Installing       : python3-six-1.15.0-9.el9.noarch                       8/14 
  Installing       : python3-dateutil-1:2.8.1-7.el9.noarch                 9/14 
  Installing       : python3-dnf-plugins-core-4.3.0-16.el9.noarch         10/14 
  Installing       : dnf-plugins-core-4.3.0-16.el9.noarch                 11/14 
  Installing       : epel-release-9-7.el9.noarch                          12/14 
  Running scriptlet: epel-release-9-7.el9.noarch                          12/14 
Many EPEL packages require the CodeReady Builder (CRB) repository.
It is recommended that you run /usr/bin/crb enable to enable the CRB repository.

  Installing       : vim-enhanced-2:8.2.2637-21.el9.x86_64                13/14 
  Installing       : less-590-5.el9.x86_64                                14/14 
  Running scriptlet: less-590-5.el9.x86_64                                14/14 
  Verifying        : python3-six-1.15.0-9.el9.noarch                       1/14 
  Verifying        : python3-dnf-plugins-core-4.3.0-16.el9.noarch          2/14 
  Verifying        : dnf-plugins-core-4.3.0-16.el9.noarch                  3/14 
  Verifying        : python3-dateutil-1:2.8.1-7.el9.noarch                 4/14 
  Verifying        : less-590-5.el9.x86_64                                 5/14 
  Verifying        : python3-systemd-234-19.el9.x86_64                     6/14 
  Verifying        : which-2.21-29.el9.x86_64                              7/14 
  Verifying        : dbus-libs-1:1.12.20-8.el9.x86_64                      8/14 
  Verifying        : python3-dbus-1.2.18-2.el9.0.1.x86_64                  9/14 
  Verifying        : vim-filesystem-2:8.2.2637-21.el9.noarch              10/14 
  Verifying        : vim-enhanced-2:8.2.2637-21.el9.x86_64                11/14 
  Verifying        : vim-common-2:8.2.2637-21.el9.x86_64                  12/14 
  Verifying        : gpm-libs-1.20.7-29.el9.x86_64                        13/14 
  Verifying        : epel-release-9-7.el9.noarch                          14/14 

Installed:
  dbus-libs-1:1.12.20-8.el9.x86_64                                              
  dnf-plugins-core-4.3.0-16.el9.noarch                                          
  epel-release-9-7.el9.noarch                                                   
  gpm-libs-1.20.7-29.el9.x86_64                                                 
  less-590-5.el9.x86_64                                                         
  python3-dateutil-1:2.8.1-7.el9.noarch                                         
  python3-dbus-1.2.18-2.el9.0.1.x86_64                                          
  python3-dnf-plugins-core-4.3.0-16.el9.noarch                                  
  python3-six-1.15.0-9.el9.noarch                                               
  python3-systemd-234-19.el9.x86_64                                             
  vim-common-2:8.2.2637-21.el9.x86_64                                           
  vim-enhanced-2:8.2.2637-21.el9.x86_64                                         
  vim-filesystem-2:8.2.2637-21.el9.noarch                                       
  which-2.21-29.el9.x86_64                                                      

Complete!
Extra Packages for Enterprise Linux 9 - x86_64  5.3 MB/s |  23 MB     00:04    
Extra Packages for Enterprise Linux 9 openh264  2.3 kB/s | 2.5 kB     00:01    
Dependencies resolved.
================================================================================
 Package          Architecture     Version                 Repository      Size
================================================================================
Installing:
 valkey           x86_64           8.0.1-3.el9             epel           1.6 M

Transaction Summary
================================================================================
Install  1 Package

Total download size: 1.6 M
Installed size: 5.7 M
Downloading Packages:
valkey-8.0.1-3.el9.x86_64.rpm                   2.2 MB/s | 1.6 MB     00:00    
--------------------------------------------------------------------------------
Total                                           1.6 MB/s | 1.6 MB     00:01     
Extra Packages for Enterprise Linux 9 - x86_64  1.6 MB/s | 1.6 kB     00:00    
Importing GPG key 0x3228467C:
 Userid     : "Fedora (epel9) <epel@fedoraproject.org>"
 Fingerprint: FF8A D134 4597 106E CE81 3B91 8A38 72BF 3228 467C
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-9
Key imported successfully
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Running scriptlet: valkey-8.0.1-3.el9.x86_64                              1/1 
  Installing       : valkey-8.0.1-3.el9.x86_64                              1/1 
  Running scriptlet: valkey-8.0.1-3.el9.x86_64                              1/1 
  Verifying        : valkey-8.0.1-3.el9.x86_64                              1/1 

Installed:
  valkey-8.0.1-3.el9.x86_64                                                     

Complete!
Last metadata expiration check: 0:00:06 ago on Tue 26 Nov 2024 03:27:55 PM PST.
Dependencies resolved.
================================================================================
 Package             Architecture  Version                  Repository     Size
================================================================================
Upgrading:
 epel-release        noarch        9-8.el9                  epel           18 k
 pam                 x86_64        1.5.1-22.el9_5           baseos        548 k

Transaction Summary
================================================================================
Upgrade  2 Packages

Total download size: 567 k
Downloading Packages:
(1/2): epel-release-9-8.el9.noarch.rpm           35 kB/s |  18 kB     00:00    
(2/2): pam-1.5.1-22.el9_5.x86_64.rpm            575 kB/s | 548 kB     00:00    
--------------------------------------------------------------------------------
Total                                           269 kB/s | 567 kB     00:02     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Upgrading        : pam-1.5.1-22.el9_5.x86_64                              1/4 
  Running scriptlet: pam-1.5.1-22.el9_5.x86_64                              1/4 
  Upgrading        : epel-release-9-8.el9.noarch                            2/4 
  Running scriptlet: epel-release-9-8.el9.noarch                            2/4 
  Cleanup          : epel-release-9-7.el9.noarch                            3/4 
  Cleanup          : pam-1.5.1-20.el9.x86_64                                4/4 
  Running scriptlet: pam-1.5.1-20.el9.x86_64                                4/4 
  Verifying        : epel-release-9-8.el9.noarch                            1/4 
  Verifying        : epel-release-9-7.el9.noarch                            2/4 
  Verifying        : pam-1.5.1-22.el9_5.x86_64                              3/4 
  Verifying        : pam-1.5.1-20.el9.x86_64                                4/4 

Upgraded:
  epel-release-9-8.el9.noarch             pam-1.5.1-22.el9_5.x86_64            

Complete!
Done valkey1
Last metadata expiration check: 0:00:07 ago on Tue 26 Nov 2024 03:28:35 PM PST.
Dependencies resolved.
================================================================================
 Package                     Arch      Version               Repository    Size
================================================================================
Installing:
 epel-release                noarch    9-7.el9               extras        19 k
 less                        x86_64    590-5.el9             baseos       161 k
 vim-enhanced                x86_64    2:8.2.2637-21.el9     appstream    1.7 M
Installing dependencies:
 dbus-libs                   x86_64    1:1.12.20-8.el9       baseos       151 k
 gpm-libs                    x86_64    1.20.7-29.el9         appstream     20 k
 python3-dateutil            noarch    1:2.8.1-7.el9         baseos       287 k
 python3-dbus                x86_64    1.2.18-2.el9.0.1      baseos       131 k
 python3-dnf-plugins-core    noarch    4.3.0-16.el9          baseos       246 k
 python3-six                 noarch    1.15.0-9.el9          baseos        36 k
 python3-systemd             x86_64    234-19.el9            baseos        82 k
 vim-common                  x86_64    2:8.2.2637-21.el9     appstream    6.6 M
 vim-filesystem              noarch    2:8.2.2637-21.el9     baseos       9.3 k
 which                       x86_64    2.21-29.el9           baseos        40 k
Installing weak dependencies:
 dnf-plugins-core            noarch    4.3.0-16.el9          baseos        36 k

Transaction Summary
================================================================================
Install  14 Packages

Total download size: 9.5 M
Installed size: 38 M
Downloading Packages:
(1/14): dnf-plugins-core-4.3.0-16.el9.noarch.rp  73 kB/s |  36 kB     00:00    
(2/14): python3-six-1.15.0-9.el9.noarch.rpm      71 kB/s |  36 kB     00:00    
(3/14): python3-dnf-plugins-core-4.3.0-16.el9.n 361 kB/s | 246 kB     00:00    
(4/14): less-590-5.el9.x86_64.rpm               761 kB/s | 161 kB     00:00    
(5/14): python3-dateutil-2.8.1-7.el9.noarch.rpm 1.1 MB/s | 287 kB     00:00    
(6/14): python3-systemd-234-19.el9.x86_64.rpm   644 kB/s |  82 kB     00:00    
(7/14): which-2.21-29.el9.x86_64.rpm            341 kB/s |  40 kB     00:00    
(8/14): dbus-libs-1.12.20-8.el9.x86_64.rpm      1.0 MB/s | 151 kB     00:00    
(9/14): python3-dbus-1.2.18-2.el9.0.1.x86_64.rp 911 kB/s | 131 kB     00:00    
(10/14): vim-filesystem-8.2.2637-21.el9.noarch.  75 kB/s | 9.3 kB     00:00    
(11/14): gpm-libs-1.20.7-29.el9.x86_64.rpm       48 kB/s |  20 kB     00:00    
(12/14): vim-enhanced-8.2.2637-21.el9.x86_64.rp 1.1 MB/s | 1.7 MB     00:01    
(13/14): epel-release-9-7.el9.noarch.rpm         13 kB/s |  19 kB     00:01    
(14/14): vim-common-8.2.2637-21.el9.x86_64.rpm  1.7 MB/s | 6.6 MB     00:03    
--------------------------------------------------------------------------------
Total                                           1.2 MB/s | 9.5 MB     00:07     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Installing       : gpm-libs-1.20.7-29.el9.x86_64                         1/14 
  Installing       : vim-filesystem-2:8.2.2637-21.el9.noarch               2/14 
  Installing       : vim-common-2:8.2.2637-21.el9.x86_64                   3/14 
  Installing       : dbus-libs-1:1.12.20-8.el9.x86_64                      4/14 
  Installing       : python3-dbus-1.2.18-2.el9.0.1.x86_64                  5/14 
  Installing       : which-2.21-29.el9.x86_64                              6/14 
  Installing       : python3-systemd-234-19.el9.x86_64                     7/14 
  Installing       : python3-six-1.15.0-9.el9.noarch                       8/14 
  Installing       : python3-dateutil-1:2.8.1-7.el9.noarch                 9/14 
  Installing       : python3-dnf-plugins-core-4.3.0-16.el9.noarch         10/14 
  Installing       : dnf-plugins-core-4.3.0-16.el9.noarch                 11/14 
  Installing       : epel-release-9-7.el9.noarch                          12/14 
  Running scriptlet: epel-release-9-7.el9.noarch                          12/14 
Many EPEL packages require the CodeReady Builder (CRB) repository.
It is recommended that you run /usr/bin/crb enable to enable the CRB repository.

  Installing       : vim-enhanced-2:8.2.2637-21.el9.x86_64                13/14 
  Installing       : less-590-5.el9.x86_64                                14/14 
  Running scriptlet: less-590-5.el9.x86_64                                14/14 
  Verifying        : python3-six-1.15.0-9.el9.noarch                       1/14 
  Verifying        : python3-dnf-plugins-core-4.3.0-16.el9.noarch          2/14 
  Verifying        : dnf-plugins-core-4.3.0-16.el9.noarch                  3/14 
  Verifying        : python3-dateutil-1:2.8.1-7.el9.noarch                 4/14 
  Verifying        : less-590-5.el9.x86_64                                 5/14 
  Verifying        : python3-systemd-234-19.el9.x86_64                     6/14 
  Verifying        : which-2.21-29.el9.x86_64                              7/14 
  Verifying        : dbus-libs-1:1.12.20-8.el9.x86_64                      8/14 
  Verifying        : python3-dbus-1.2.18-2.el9.0.1.x86_64                  9/14 
  Verifying        : vim-filesystem-2:8.2.2637-21.el9.noarch              10/14 
  Verifying        : vim-enhanced-2:8.2.2637-21.el9.x86_64                11/14 
  Verifying        : vim-common-2:8.2.2637-21.el9.x86_64                  12/14 
  Verifying        : gpm-libs-1.20.7-29.el9.x86_64                        13/14 
  Verifying        : epel-release-9-7.el9.noarch                          14/14 

Installed:
  dbus-libs-1:1.12.20-8.el9.x86_64                                              
  dnf-plugins-core-4.3.0-16.el9.noarch                                          
  epel-release-9-7.el9.noarch                                                   
  gpm-libs-1.20.7-29.el9.x86_64                                                 
  less-590-5.el9.x86_64                                                         
  python3-dateutil-1:2.8.1-7.el9.noarch                                         
  python3-dbus-1.2.18-2.el9.0.1.x86_64                                          
  python3-dnf-plugins-core-4.3.0-16.el9.noarch                                  
  python3-six-1.15.0-9.el9.noarch                                               
  python3-systemd-234-19.el9.x86_64                                             
  vim-common-2:8.2.2637-21.el9.x86_64                                           
  vim-enhanced-2:8.2.2637-21.el9.x86_64                                         
  vim-filesystem-2:8.2.2637-21.el9.noarch                                       
  which-2.21-29.el9.x86_64                                                      

Complete!
Extra Packages for Enterprise Linux 9 - x86_64  4.6 MB/s |  23 MB     00:04    
Extra Packages for Enterprise Linux 9 openh264  3.0 kB/s | 2.5 kB     00:00    
Last metadata expiration check: 0:00:01 ago on Tue 26 Nov 2024 03:29:08 PM PST.
Dependencies resolved.
================================================================================
 Package          Architecture     Version                 Repository      Size
================================================================================
Installing:
 valkey           x86_64           8.0.1-3.el9             epel           1.6 M

Transaction Summary
================================================================================
Install  1 Package

Total download size: 1.6 M
Installed size: 5.7 M
Downloading Packages:
valkey-8.0.1-3.el9.x86_64.rpm                   899 kB/s | 1.6 MB     00:01    
--------------------------------------------------------------------------------
Total                                           796 kB/s | 1.6 MB     00:02     
Extra Packages for Enterprise Linux 9 - x86_64  1.6 MB/s | 1.6 kB     00:00    
Importing GPG key 0x3228467C:
 Userid     : "Fedora (epel9) <epel@fedoraproject.org>"
 Fingerprint: FF8A D134 4597 106E CE81 3B91 8A38 72BF 3228 467C
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-9
Key imported successfully
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Running scriptlet: valkey-8.0.1-3.el9.x86_64                              1/1 
  Installing       : valkey-8.0.1-3.el9.x86_64                              1/1 
  Running scriptlet: valkey-8.0.1-3.el9.x86_64                              1/1 
  Verifying        : valkey-8.0.1-3.el9.x86_64                              1/1 

Installed:
  valkey-8.0.1-3.el9.x86_64                                                     

Complete!
Last metadata expiration check: 0:00:07 ago on Tue 26 Nov 2024 03:29:08 PM PST.
Dependencies resolved.
================================================================================
 Package                Architecture     Version           Repository      Size
================================================================================
Upgrading:
 epel-release           noarch           9-8.el9           epel            18 k

Transaction Summary
================================================================================
Upgrade  1 Package

Total download size: 18 k
Downloading Packages:
epel-release-9-8.el9.noarch.rpm                  34 kB/s |  18 kB     00:00    
--------------------------------------------------------------------------------
Total                                            21 kB/s |  18 kB     00:00     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Upgrading        : epel-release-9-8.el9.noarch                            1/2 
  Running scriptlet: epel-release-9-8.el9.noarch                            1/2 
  Cleanup          : epel-release-9-7.el9.noarch                            2/2 
  Running scriptlet: epel-release-9-7.el9.noarch                            2/2 
  Verifying        : epel-release-9-8.el9.noarch                            1/2 
  Verifying        : epel-release-9-7.el9.noarch                            2/2 

Upgraded:
  epel-release-9-8.el9.noarch                                                   

Complete!
Done valkey2
Last metadata expiration check: 0:00:08 ago on Tue 26 Nov 2024 03:29:45 PM PST.
Dependencies resolved.
================================================================================
 Package                     Arch      Version               Repository    Size
================================================================================
Installing:
 epel-release                noarch    9-7.el9               extras        19 k
 less                        x86_64    590-5.el9             baseos       161 k
 vim-enhanced                x86_64    2:8.2.2637-21.el9     appstream    1.7 M
Installing dependencies:
 dbus-libs                   x86_64    1:1.12.20-8.el9       baseos       151 k
 gpm-libs                    x86_64    1.20.7-29.el9         appstream     20 k
 python3-dateutil            noarch    1:2.8.1-7.el9         baseos       287 k
 python3-dbus                x86_64    1.2.18-2.el9.0.1      baseos       131 k
 python3-dnf-plugins-core    noarch    4.3.0-16.el9          baseos       246 k
 python3-six                 noarch    1.15.0-9.el9          baseos        36 k
 python3-systemd             x86_64    234-19.el9            baseos        82 k
 vim-common                  x86_64    2:8.2.2637-21.el9     appstream    6.6 M
 vim-filesystem              noarch    2:8.2.2637-21.el9     baseos       9.3 k
 which                       x86_64    2.21-29.el9           baseos        40 k
Installing weak dependencies:
 dnf-plugins-core            noarch    4.3.0-16.el9          baseos        36 k

Transaction Summary
================================================================================
Install  14 Packages

Total download size: 9.5 M
Installed size: 38 M
Downloading Packages:
(1/14): dnf-plugins-core-4.3.0-16.el9.noarch.rp 106 kB/s |  36 kB     00:00    
(2/14): python3-six-1.15.0-9.el9.noarch.rpm     102 kB/s |  36 kB     00:00    
(3/14): python3-dnf-plugins-core-4.3.0-16.el9.n 331 kB/s | 246 kB     00:00    
(4/14): python3-dateutil-2.8.1-7.el9.noarch.rpm 567 kB/s | 287 kB     00:00    
(5/14): python3-systemd-234-19.el9.x86_64.rpm   452 kB/s |  82 kB     00:00    
(6/14): which-2.21-29.el9.x86_64.rpm            206 kB/s |  40 kB     00:00    
(7/14): dbus-libs-1.12.20-8.el9.x86_64.rpm      889 kB/s | 151 kB     00:00    
(8/14): python3-dbus-1.2.18-2.el9.0.1.x86_64.rp 997 kB/s | 131 kB     00:00    
(9/14): vim-filesystem-8.2.2637-21.el9.noarch.r  65 kB/s | 9.3 kB     00:00    
(10/14): less-590-5.el9.x86_64.rpm              110 kB/s | 161 kB     00:01    
(11/14): gpm-libs-1.20.7-29.el9.x86_64.rpm       13 kB/s |  20 kB     00:01    
(12/14): epel-release-9-7.el9.noarch.rpm         28 kB/s |  19 kB     00:00    
(13/14): vim-common-8.2.2637-21.el9.x86_64.rpm  706 kB/s | 6.6 MB     00:09    
(14/14): vim-enhanced-8.2.2637-21.el9.x86_64.rp 151 kB/s | 1.7 MB     00:11    
--------------------------------------------------------------------------------
Total                                           610 kB/s | 9.5 MB     00:15     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Installing       : gpm-libs-1.20.7-29.el9.x86_64                         1/14 
  Installing       : vim-filesystem-2:8.2.2637-21.el9.noarch               2/14 
  Installing       : vim-common-2:8.2.2637-21.el9.x86_64                   3/14 
  Installing       : dbus-libs-1:1.12.20-8.el9.x86_64                      4/14 
  Installing       : python3-dbus-1.2.18-2.el9.0.1.x86_64                  5/14 
  Installing       : which-2.21-29.el9.x86_64                              6/14 
  Installing       : python3-systemd-234-19.el9.x86_64                     7/14 
  Installing       : python3-six-1.15.0-9.el9.noarch                       8/14 
  Installing       : python3-dateutil-1:2.8.1-7.el9.noarch                 9/14 
  Installing       : python3-dnf-plugins-core-4.3.0-16.el9.noarch         10/14 
  Installing       : dnf-plugins-core-4.3.0-16.el9.noarch                 11/14 
  Installing       : epel-release-9-7.el9.noarch                          12/14 
  Running scriptlet: epel-release-9-7.el9.noarch                          12/14 
Many EPEL packages require the CodeReady Builder (CRB) repository.
It is recommended that you run /usr/bin/crb enable to enable the CRB repository.

  Installing       : vim-enhanced-2:8.2.2637-21.el9.x86_64                13/14 
  Installing       : less-590-5.el9.x86_64                                14/14 
  Running scriptlet: less-590-5.el9.x86_64                                14/14 
  Verifying        : python3-six-1.15.0-9.el9.noarch                       1/14 
  Verifying        : python3-dnf-plugins-core-4.3.0-16.el9.noarch          2/14 
  Verifying        : dnf-plugins-core-4.3.0-16.el9.noarch                  3/14 
  Verifying        : python3-dateutil-1:2.8.1-7.el9.noarch                 4/14 
  Verifying        : less-590-5.el9.x86_64                                 5/14 
  Verifying        : python3-systemd-234-19.el9.x86_64                     6/14 
  Verifying        : which-2.21-29.el9.x86_64                              7/14 
  Verifying        : dbus-libs-1:1.12.20-8.el9.x86_64                      8/14 
  Verifying        : python3-dbus-1.2.18-2.el9.0.1.x86_64                  9/14 
  Verifying        : vim-filesystem-2:8.2.2637-21.el9.noarch              10/14 
  Verifying        : vim-enhanced-2:8.2.2637-21.el9.x86_64                11/14 
  Verifying        : vim-common-2:8.2.2637-21.el9.x86_64                  12/14 
  Verifying        : gpm-libs-1.20.7-29.el9.x86_64                        13/14 
  Verifying        : epel-release-9-7.el9.noarch                          14/14 

Installed:
  dbus-libs-1:1.12.20-8.el9.x86_64                                              
  dnf-plugins-core-4.3.0-16.el9.noarch                                          
  epel-release-9-7.el9.noarch                                                   
  gpm-libs-1.20.7-29.el9.x86_64                                                 
  less-590-5.el9.x86_64                                                         
  python3-dateutil-1:2.8.1-7.el9.noarch                                         
  python3-dbus-1.2.18-2.el9.0.1.x86_64                                          
  python3-dnf-plugins-core-4.3.0-16.el9.noarch                                  
  python3-six-1.15.0-9.el9.noarch                                               
  python3-systemd-234-19.el9.x86_64                                             
  vim-common-2:8.2.2637-21.el9.x86_64                                           
  vim-enhanced-2:8.2.2637-21.el9.x86_64                                         
  vim-filesystem-2:8.2.2637-21.el9.noarch                                       
  which-2.21-29.el9.x86_64                                                      

Complete!
Extra Packages for Enterprise Linux 9 - x86_64  5.5 MB/s |  23 MB     00:04    
Extra Packages for Enterprise Linux 9 openh264  3.0 kB/s | 2.5 kB     00:00    
Dependencies resolved.
================================================================================
 Package          Architecture     Version                 Repository      Size
================================================================================
Installing:
 valkey           x86_64           8.0.1-3.el9             epel           1.6 M

Transaction Summary
================================================================================
Install  1 Package

Total download size: 1.6 M
Installed size: 5.7 M
Downloading Packages:
valkey-8.0.1-3.el9.x86_64.rpm                   1.2 MB/s | 1.6 MB     00:01    
--------------------------------------------------------------------------------
Total                                           983 kB/s | 1.6 MB     00:01     
Extra Packages for Enterprise Linux 9 - x86_64  1.6 MB/s | 1.6 kB     00:00    
Importing GPG key 0x3228467C:
 Userid     : "Fedora (epel9) <epel@fedoraproject.org>"
 Fingerprint: FF8A D134 4597 106E CE81 3B91 8A38 72BF 3228 467C
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-9
Key imported successfully
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Running scriptlet: valkey-8.0.1-3.el9.x86_64                              1/1 
  Installing       : valkey-8.0.1-3.el9.x86_64                              1/1 
  Running scriptlet: valkey-8.0.1-3.el9.x86_64                              1/1 
  Verifying        : valkey-8.0.1-3.el9.x86_64                              1/1 

Installed:
  valkey-8.0.1-3.el9.x86_64                                                     

Complete!
Last metadata expiration check: 0:00:07 ago on Tue 26 Nov 2024 03:30:27 PM PST.
Dependencies resolved.
================================================================================
 Package             Architecture  Version                  Repository     Size
================================================================================
Upgrading:
 epel-release        noarch        9-8.el9                  epel           18 k
 pam                 x86_64        1.5.1-22.el9_5           baseos        548 k

Transaction Summary
================================================================================
Upgrade  2 Packages

Total download size: 567 k
Downloading Packages:
(1/2): epel-release-9-8.el9.noarch.rpm           27 kB/s |  18 kB     00:00    
(2/2): pam-1.5.1-22.el9_5.x86_64.rpm            661 kB/s | 548 kB     00:00    
--------------------------------------------------------------------------------
Total                                           291 kB/s | 567 kB     00:01     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Upgrading        : pam-1.5.1-22.el9_5.x86_64                              1/4 
  Running scriptlet: pam-1.5.1-22.el9_5.x86_64                              1/4 
  Upgrading        : epel-release-9-8.el9.noarch                            2/4 
  Running scriptlet: epel-release-9-8.el9.noarch                            2/4 
  Cleanup          : epel-release-9-7.el9.noarch                            3/4 
  Cleanup          : pam-1.5.1-20.el9.x86_64                                4/4 
  Running scriptlet: pam-1.5.1-20.el9.x86_64                                4/4 
  Verifying        : epel-release-9-8.el9.noarch                            1/4 
  Verifying        : epel-release-9-7.el9.noarch                            2/4 
  Verifying        : pam-1.5.1-22.el9_5.x86_64                              3/4 
  Verifying        : pam-1.5.1-20.el9.x86_64                                4/4 

Upgraded:
  epel-release-9-8.el9.noarch             pam-1.5.1-22.el9_5.x86_64            

Complete!
Done valkey3
```

# Starting Valkey server and sentinel instances

Since the Valkey package is already installed in the previous step. We now create the configuration files and start the server and sentinel processes.

```
./valkey-sentinel start
Starting server and sentinel...
Master IP address: 198.19.249.200
root           9  0.0  0.1 1244244 13900 ?       Ssl  18:05   0:00 orbstack-helper: valkey1
jericho+     757  0.0  0.0 1198740 7100 ?        S    18:54   0:00 [rosetta] /bin/bash -bash -c valkey-server ./server.conf --protected-mode no --daemonize yes && valkey-sentinel ./sentinel.conf --daemonize yes && ps aux|grep valkey
jericho+     771  0.0  0.1 1257032 14756 ?       Ssl  18:54   0:00 [rosetta] /usr/bin/valkey-server valkey-server ./server.conf --protected-mode no --daemonize yes
jericho+     777  0.0  0.1 1255044 14924 ?       Rsl  18:54   0:00 [rosetta] /usr/bin/valkey-sentinel valkey-sentinel ./sentinel.conf --daemonize yes
jericho+     779  0.0  0.0 1197384 4364 ?        S    18:54   0:00 [rosetta] /usr/bin/grep grep valkey
Done valkey1
root           9  0.0  0.1 1244244 12924 ?       Ssl  18:06   0:00 orbstack-helper: valkey2
jericho+     717  0.0  0.0 1198740 6964 ?        S    18:54   0:00 [rosetta] /bin/bash -bash -c valkey-server ./server.conf --protected-mode no --daemonize yes && valkey-sentinel ./sentinel.conf --daemonize yes && ps aux|grep valkey
jericho+     731  0.0  0.1 1257040 14756 ?       Ssl  18:54   0:00 [rosetta] /usr/bin/valkey-server valkey-server ./server.conf --protected-mode no --daemonize yes
jericho+     737  0.0  0.1 1257096 15044 ?       Rsl  18:54   0:00 [rosetta] /usr/bin/valkey-sentinel valkey-sentinel ./sentinel.conf --daemonize yes
jericho+     739  0.0  0.0 1197384 4348 ?        S    18:54   0:00 [rosetta] /usr/bin/grep grep valkey
Done valkey2
root           9  0.0  0.1 1244244 13312 ?       Ssl  18:08   0:00 orbstack-helper: valkey3
jericho+     738  0.0  0.0 1198740 7040 ?        S    18:54   0:00 [rosetta] /bin/bash -bash -c valkey-server ./server.conf --protected-mode no --daemonize yes && valkey-sentinel ./sentinel.conf --daemonize yes && ps aux|grep valkey
jericho+     752  0.0  0.1 1257040 14740 ?       Ssl  18:54   0:00 [rosetta] /usr/bin/valkey-server valkey-server ./server.conf --protected-mode no --daemonize yes
jericho+     758  0.0  0.1 1257096 14784 ?       Rsl  18:54   0:00 [rosetta] /usr/bin/valkey-sentinel valkey-sentinel ./sentinel.conf --daemonize yes
jericho+     760  0.0  0.0 1197384 4360 ?        S    18:54   0:00 [rosetta] /usr/bin/grep grep valkey
Done valkey3
```

## SSH to the node

We can now connect to the node via SSH.
```
./valkey-sentinel ssh valkey1
Connecting to valkey1
[jerichorivera@valkey1 ~]$ 
```

## Test failover

From here we can test failover.

```
[jerichorivera@valkey1 ~]$ valkey-cli -p 6379 debug sleep 30
OK
```

