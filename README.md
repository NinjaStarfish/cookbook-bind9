Description
===========

This cookbook takes care of the installation and configuration of BIND9.
Currently it is possible to define some global variables and to manage zonefiles via data bags (JSON example below).

**DISCLAIMER**:  
Please keep in mind that this cookbook is far from finished and not recommended for production use.

Requirements
============

Platform:

* Debian
* Ubuntu

Attributes
==========

* **node[:bind9][:enable_ipv6]**       - Enables BIND to listen on an IPv6 address. Default is: false
* **node[:bind9][:allow_query]**       - Allow clients to query the nameserver. Default is: "any"
* **node[:bind9][:allow_recursion]**   - Allow recursive name resolution. Default is: "none" (to prevent DNS cache poisoning)
* **node[:bind9][:allow_update]**      - Allow dynamic DNS updates. Default is: "none"
* **node[:bind9][:allow_transfer]**    - Allow zone transfers globally. Default is: "none"
* **node[:bind9][:enable_forwarding]** - Enables forwarding of requests. Default is: false
* **node[:bind9][:forwarders]**        - Array for forwarding DNS. Default is: "8.8.8.8" and "8.8.4.4" (Google DNS)

Usage
=====

Add "recipe[bind9]" to a node or a role. Per-environment domain configuration is stored in data bags as shown below.
Please note that the data bag structure is mandatory except:
* TTL for DNS records - if left blank, defaults to global TTL

```
developer@workstation:~/chef-repo$ knife data bag create zones
developer@workstation:~/chef-repo$ knife data bag create zones example_com
```
```json
{
  "id": "example_com",
  "production": {
    "domain": "example.com",
    "type": "master",
    "allow_transfer": [ "4.4.4.4",
                        "8.8.8.8" ],
    "zone_info": {
      "global_ttl": 300,
      "soa": "ns.example.com.",
      "contact": "webmaster.example.com.",
      "serial": 2011091402,
      "nameserver": [ "ns.example.net.",
                      "ns.friendlyweb.co.uk.",
                      "ns.ninjastarfish.co.uk." ],
      "mail_exchange": [{
        "host": "ASPMX.L.GOOGLE.COM.",
        "priority": 10
      },{
        "host": "ALT1.ASPMX.L.GOOGLE.COM.",
        "priority": 20
      },{
        "host": "ALT2.ASPMX.L.GOOGLE.COM.",
        "priority": 20
      },{
        "host": "ASPMX2.GOOGLEMAIL.COM.",
        "priority": 30
      },{
        "host": "ASPMX3.GOOGLEMAIL.COM.",
        "priority": 30
      },{
        "host": "ASPMX4.GOOGLEMAIL.COM.",
        "priority": 30
      },{
        "host": "ASPMX5.GOOGLEMAIL.COM.",
        "priority": 30
      }],
      "records": [{
        "name": "example.com.",
        "type": "A",
        "ip": "192.0.43.10"
      },{
        "name": "www",
        "type": "CNAME",
        "ip": "example.com"
      },{
        "name": "me",
        "type": "A",
        "ip": "127.0.0.1"
      },{
        "name": "mail",
        "type": "CNAME",
        "ip": "ghs.google.com."
      }]
    }
  },
  "development": {
    "domain": "example.com",
    "type": "master",
    "allow_transfer": [ "" ],
    "zone_info": {
      "global_ttl": 30,
      "soa": "localhost.",
      "contact": "webmaster.example.com.",
      "serial": 2011091402,
      "nameserver": [ "localhost." ],
      "mail_exchange": [{
        "host": "localhost.",
        "priority": 0
      }],
      "records": [{
        "name": "example.com.",
        "type": "A",
        "ip": "127.0.0.1"
      },{
        "name": "www",
        "type": "CNAME",
        "ip": "example.com"
      }]
    }
  }
}
```
Using Librarian?
================

Add this line to your Cheffile:

    cookbook 'bind9',
      :git => 'https://github.com/NinjaStarfishUK/cookbook-bind9'