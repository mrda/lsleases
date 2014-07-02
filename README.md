# lsleases - dhcp leases sniffer
**lsleases** captures broadcast 'DHCP Request' datagrams and displays the ip, mac and host name from computers in the local network with dynamic ip address.


  
##### ...and for what? 
Have you ever boot an embedded system (rasperry-pi, cubie, ...), an android device, an virtual machine or anything else with dynamic ip address (dhcp) and you need that address? Then this tool is for your toolbox - check the [Usage](#usage).



## Usage

1. install `lsleases` - see [Installation](#installation)
2. replug / startup any device with dynamic ip address
3. display captured ip, mac and host names. 

        j@main:~> lsleases
        Ip               Mac                Name
        192.168.1.189    10:bf:48:xx:xx:xx  android-f6c6dca2130b287
        192.168.1.122    b8:27:eb:xx:xx:xx  raspberrypi
        192.168.1.178    00:22:fb:xx:xx:xx  laptop


*for more info check the [MANUAL](https://github.com/j-keck/lsleases/blob/master/MANUAL.md)*
  

## Installation

### Binary packages

#### per package (from github.com)
    
Download the corresponding package for your platfrom from http://github.com/j-keck/lsleases/releases/latest.

Install per:
  
  * Debian based: `sudo dpkg -i <PKG_NAME>.deb`
  * RedHat based: `sudo rpm -i <PKG_NAME>.rpm`
  * Windows: use the installer: `lsleases_installer_<VERSION>_<ARCH>.exe`

  
#### per package manager repository (from bintray.com)

Debian based:

  * add the bintray repository:
  
    `echo "deb http://dl.bintray.com/j-keck/deb /" | sudo tee /etc/apt/sources.list`
  
  * update your index: `sudo apt-get update`
  * install: `sudo apt-get install lsleases`

RedHat based:

  * add the bintray repository:
  
   `wget https://bintray.com/j-keck/rpm/rpm -O - | sudo tee /etc/yum.repos.d/bintray-j-keck-rpm.repo`
  
  * install: `sudo yum install lsleases`

  
### From source

  1. install Go toolset from http://golang.org if not already
  2. ensure [`$GOPATH`](http://golang.org/doc/code.html#GOPATH) is properly set and `$GOPATH/bin` is in your `$PATH` 
  3. download and build: `go get github.com/j-keck/lsleases`. This will build the binary in `$GOPATH/bin`

  **Linux (if not running as root)**
  
  4. create the runtime application data dir (for unix domain socket, persistent leases)

     `sudo mkdir -p /var/lib/lsleases && sudo chown <USER WITH STARTS THE SERVER> /var/lib/lsleases`
  
  5. to allow non-root users to open port less than 1024 (dhcp sniffer) and use raw sockets (active availability host check (per arping)) set the corresponding capabilities
  
     `sudo setcap 'cap_net_raw,cap_net_bind_service+ep' $GOPATH/bin/lsleases`
  
  **FreeBSD (if not running as root)**
  
  4. create the runtime application data dir (for unix domain socket, persistent leases)

     `mkdir -p /var/lib/lsleases && chown <USER WITH STARTS THE SERVER> /var/lib/lsleases`
  
  5. allow non-root users to open port less than 1024 (dhcp sniffer)
  
        echo net.inet.ip.portrange.reservedhigh=0 >> /etc/sysctl.conf
        service sysctl restart

  *active availability host check (per arping) as non-root under FreeBSD not supported*  

  
  **Windows**

    
  *no additional steps necessary*


  
  
  
## Notes

- CentOS / RHEL distros doesn't send the hostname in the 'DHCP Request' datagram by default.
  To include the hostname in the datagram:

        echo 'DHCP_HOSTNAME=$(hostname -s)' >> /etc/sysconfig/network-scripts/ifcfg-eth0

  
- server logs location
    - init / SysVinit based: `/var/log/lsleases.log`
    - systemd based: `journalctl -u lsleases` and `/var/log/lsleases.log`

  
- if you get '... listen udp :67: bind: address already in use' error at server startup - check which program already listen on port 67

    - Linux:
  
          sudo netstat -taupen | grep ":67 " | awk '{print $NF}'

    - FreeBSD:

          sockstat -l -P udp -p 67

  
- if you get '... listen udp :67: bind: permission denied' error at server startup

    - installed from source: reread [installation guide](#from-source)

    - binary installation: [open and issue](http://github.com/j-keck/lsleases/issues)


     

  
## Changelog

see [Changelog](https://github.com/j-keck/lsleases/blob/master/CHANGELOG.md)

