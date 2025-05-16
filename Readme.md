# Resources for using VARNISH as a Proxy for CVMFS

This repository contains configurations and information for using VARNISH as a http proxy in CVMFS deployments, as an alternative for the commonly used SQUID proxy.

## FRONTIER

For historical reasons, SQUID proxies at many sites are used both for CVMFS and FRONTIER. Thus, a replacement for SQUID needs to support both FRONTIER and CVMFS.

The two main features that FRONTIER needs are collapsed forwarding and the If-Modified-Since header (details to be added).

## Forward vs Reverse Proxy

For CVMFS, a forward proxy is preferrable as some logic around selecting the URLs for the server is done on the client side, and it's hard to get a reverse proxy config that is universally applicable and not only useful for one site.

VARNISH is a reverse proxy by design, but can be run as a forward proxy using the dynamic vmod. See cvmfs.vcl for an example.

## Deployment Instructions

The following instructions have been tested on a fresh almalinux 9 box:


```sh
yum update
yum install varnish
# on alma 9, currently varnish 6.6
varnishd -V
yum -y install git
git clone https://github.com/nigoroll/libvmod-dynamic
cd libvmod-dynamic/
# branch compatible with varnish 6.6
git checkout 6.6
yum -y groupinstall 'Development Tools'
yum -y install python-docutils
yum -y install getdns-devel
yum -y install varnish-devel
./autogen.sh
./configure
make install
cd ..
git clone https://github.com/cvmfs-contrib/varnish-cvmfs
cd varnish-cvmfs/
cp cvmfs.vcl /etc/varnish/default.vcl
systemctl start varnish
systemctl status varnish
# open firewall if needed 
firewall-cmd --zone=public --permanent --add-port 6081/tcp

# sanity test
# you may want to run varnishlog in another terminal to check the activity 
varnishlog
http_proxy=http://127.0.0.1:6081/  curl http://cvmfs-stratum-one.cern.ch/cvmfs/atlas.cern.ch/.cvmfspublished

# config to use in cvmfs-clients:
# CVMFS_HTTP_PROXY=<url of proxy machine>:6081


```
## Resources

* [Varnish presentation at the CernVM workshop '24](https://indico.cern.ch/event/1347727/contributions/5673368/attachments/2927909/5140554/CVMFS%20Workshop%202024%20-vanish%20and%20jump%20combined.pdf)

## Contact

Join or contact the  mailinglist cvmfs-varnish-wg _ cern _ ch for discussions. 
