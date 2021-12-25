# Ansible Pihole


The object of this role is to manage multiple pihole servers,  on multiple sites.

## Requirements


none

## Role Variables


A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

- `dnsmasq_folder:` Where is  dnsmasq configs stored, default: `/etc/dnsmasq.d/`
- `pihole_folder:`  Where is  pihole configs stored, default: `/etc/pihole/`
- `pihole_user:`    What user is running pihole default: `pihole`
- `Pihole_folder_templates:` What files needs to be copy from templates, to pihole config
- `Pihole_dnsmasq_folder_templates:` What files needs to be copy from templates, to dnsmasq config

### Pihole setupVars.conf variables:
Some of the variables are only for web, some are for config.
https://github.com/pi-hole/pi-hole/blob/master/automated%20install/basic-install.sh#L106

- `pihole_WEBPASSWORD:` [web UI:password](https://github.com/pi-hole/pi-hole/blob/4736e03108763cc2d5659f48d8a1e8a64d9b2608/automated%20install/basic-install.sh#L2743)  

**Pihole password:**
`````bash
echo -n PASS@word | sha256sum | awk '{printf "%s",$1 }' | sha256sum
`````
- `pihole_CONDITIONAL_FORWARDING:` `true` or `false`, [AdminLTE/settings.php](https://github.com/pi-hole/AdminLTE/blob/master/settings.php#L992)   If not configured as your DHCP server, Pi-hole typically won't be able to determine the names of devices on your local network. As a result, tables such as Top Clients will only show IP addresses.
  - `pihole_REV_SERVER:` 
  - `pihole_REV_SERVER_CIDR:` Local network in [CIDR notation](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)
  - `pihole_REV_SERVER_TARGET:` IP address of your DHCP server (router)
  - `pihole_REV_SERVER_DOMAIN:` Local domain name (optional)

- `pihole_ADMIN_EMAIL:` Used for display
- `pihole_WEBUIBOXEDLAYOUT:` web interface  appearance `default-darker`, `dark` or `default-light`
- `pihole_WEBTHEME:` Default `boxed`
- `pihole_DHCP_ACTIVE:` Default `false`, can be  `true`, enables DHCP server
- `pihole_DHCP_START:` start of range
- `pihole_DHCP_END:` end of range
- `pihole_DHCP_ROUTER:` Default gateway
- `pihole_DHCP_LEASETIME:` time in hours for DHCP lease
- `pihole_PIHOLE_DOMAIN:` Search domain
- `pihole_DHCP_IPv6:`  Default `false`, can be  `true`, enable IPv6 support (SLAAC + RA)
- `pihole_DHCP_rapid_commit:` Default `false`, can be  `true`,Enable DHCPv4 rapid commit (fast address assignment)
- `pihole_PIHOLE_INTERFACE:` Web view
- `pihole_IPV4_ADDRESS:`Web view
- `pihole_IPV6_ADDRESS:`Web view
- `pihole_QUERY_LOGGING:`Default `true`, can be  `false`, Enable the Pi-hole log at `/var/log/pihole.log`
- `pihole_INSTALL_WEB_SERVER:` Default `true`, can be  `false`
- `pihole_INSTALL_WEB_INTERFACE:` Default `true`, can be  `false`
- `pihole_LIGHTTPD_ENABLED:`Default `true`, can be  `false`
- `pihole_CACHE_SIZE:` `10000`, 
- `pihole_DNSMASQ_LISTENING:` How many DNS results to keep in memory
- `pihole_PIHOLE_DNS:` list of DNS servers, limit is unknown:
  - 1.1.1.1
  - 1.0.0.1
  - 8.8.8.8
  - 8.8.4.4
- `pihole_DNS_FQDN_REQUIRED:` Default `true`, can be  `false`,**Never forward non-FQDNs** When there is a Pi-hole domain set and this box is ticked, this asks FTL that this domain is purely local and FTL may answer queries from `/etc/hosts` or DHCP leases but should never forward queries on that domain to any upstream servers.
- `pihole_DNS_BOGUS_PRIV:` Default `true`, can be  `false`, **Never forward reverse lookups for private IP ranges** All reverse lookups for private IP ranges (i.e., `192.168.0.x/24`, etc.) which are not found in `/etc/hosts` or the DHCP leases are answered with "no such domain" rather than being forwarded upstream. The set of prefixes affected is the list given in [RFC6303](https://datatracker.ietf.org/doc/html/rfc6303).
- `pihole_DNSSEC:` Default `true`, can be  `false`,[Secure DNS]([Domain Name System Security Extensions](https://en.wikipedia.org/wiki/Domain_Name_System_Security_Extensions))
- `pihole_BLOCKING_ENABLED:` Default `true`, can be  `false`, Enable ad blocking.

### Pihole `/etc/pihole/adlists.list`

- `pihole_adlist_list:` Not used, may be used in future.

### Pihole `/etc/pihole/dns-servers.conf`
For web view

- `pihole_remote_dns_servers_UI:` Default list
- `pihole_remote_dns_servers_UI_extra:` Your pretty list

### Ad lists, TODO


### DNSmarsq setting

To be able to have unique  settings for different sites, the DNSmarsq settings are split.

- `pihole_DNSmasq_Global:` 
- `pihole_DNSmasq_Site:`

- `pihole_DNSmasq_DHCP:` list of your DNSmarsq settings, for your DHCP server. since only one server will have DHCP enabled, this is a separate list.

#### TFTP server

- `pihole_DNSmasq_TFTP_State:` Default `False`, can be `True`. Enables the service
- `pihole_DNSmasq_TFTP_Path:` Path to directory that you like to share If not valid, wil fail.

### ToDo list:

- [ ] adlist
  - [ ] Whitelist RegEx filter
  - [ ] Whitelist Domain
  - [ ] Blacklist RegEx filter
  - [ ] Blacklist Domain
- [ ] check for update
- [ ] update
- [ ] 

White and black list need database or API setup. Pihole uses SQLite database, Ansible supports MYsql.

## SQLite3 commands:

SQLite version 3.34.1 support json output
source: 
- <https://docs.pi-hole.net/database/ftl/h=api#example-for-interaction-with-the-long-term-query-database>


List the tables in your database:

````sql
.tables
````

List how the table looks:

````sql
.schema tablename
````

Print the entire table:
````sql
SELECT * FROM tablename;
````

List all of the available SQLite prompt commands:
````sql
.help
````




## Dependencies


No dependencies.

## Example Playbook


Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

````yml
- hosts: pihole_servers
  roles:
   - ansible-pihole
  vars:
    pihole_CONDITIONAL_FORWARDING: false
    pihole_ADMIN_EMAIL: eideen@example.com
    pihole_WEBUIBOXEDLAYOUT: boxed
    pihole_WEBTHEME: default-dark
    pihole_PIHOLE_DOMAIN: example.com

    pihole_INSTALL_WEB_SERVER: false
    pihole_INSTALL_WEB_INTERFACE: false
    pihole_LIGHTTPD_ENABLED: false
    pihole_CACHE_SIZE: 40000
    pihole_DNSMASQ_LISTENING: all
    pihole_PIHOLE_DNS:
      - 1.0.0.1
    pihole_DNSmasq_Global:
      - address=/pihole.example.com/pihole-s1.example.com/192.168.2.6
    pihole_DNSmasq_Site:
      - srv-host=_apt_proxy._tcp.example.com,server2.example.com,3142
    pihole_DNSmasq_DHCP:
      - dhcp-option=option:ntp-server,192.168.2.1
````

## License


BSD

## Author Information


Torstein Eide
<https://github.com/Eideen>
