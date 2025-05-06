# Resources for using VARNISH as a Proxy for CVMFS

This repository contains configurations and information for using VARNISH as a http proxy in CVMFS deployments, as an alternative for the commonly used SQUID proxy.

## FRONTIER

For historical reasons, SQUID proxies at many sites are used both for CVMFS and FRONTIER. Thus, a replacement for SQUID needs to support both FRONTIER and CVMFS.

The two main features that FRONTIER needs are collapsed forwarding and the If-Modified-Since header (details to be added).

## Forward vs Reverse Proxy

For CVMFS, a forward proxy is preferrable as some logic around selecting the URLs for the server is done on the client side, and it's hard to get a reverse proxy config that is universally applicable and not only useful for one site.

VARNISH is a reverse proxy by design, but can be run as a forward proxy using the dynamic vmod. See cvmfs.vcl for an example.

## Deployment Instructions

Under construction.
