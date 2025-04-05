# fail2ban

## Install

Package `fail2ban`

## Logs

    tail -F /var/log/fail2ban.log
    journalctl -f -u fail2ban.service

## Commands

### Version

    fail2ban-client version

### Show status for all jails

    for jail in `fail2ban-client status | grep "Jail list" | sed -E 's/^[^:]+:[ \t]+//' | sed 's/,//g'` ; do echo ">>>> $jail" ; fail2ban-client get ${jail} banip --with-time ; echo ; done

### Get all banned IP address for jail

    fail2ban-client get jail-name banip
    # fail2ban-client get jail-name banip | tr ' ' '\n'

### Unban

    fail2ban-client set JAIL_NAME unbanip IP_ADDRESS

## Setup

**fail2ban/jail.d/custom.conf**

    [DEFAULT]
    bantime  = 300
    findtime = 300
    banaction = iptables-allports

**fail2ban/jail.d/apache-dos.conf**

    [apache-dos]
    enabled  = true
    port     = http,https
    filter   = apache-dos
    logpath  = /var/log/apache2/access.log
    datepattern = %%d/%%b/%%Y:%%H:%%M:%%S %%z
    maxretry = 20
    findtime = 60
    bantime  = 24h

**fail2ban/filter.d/apache-dos.conf**

    # Fail2Ban filter to scan Apache access.log for DoS attacks

    [INCLUDES]
    before = common.conf

    [Definition]
    # Option:  failregex
    # Notes.:  regex to match GET requests in the logfile resulting in one of the
    #          following status codes: 401, 403, 404, 503.
    #          The host must be matched by a group named "host". The tag "<HOST>" 
    #          can be used for standard IP/hostname matching and is only an alias for
    #          (?:::f{4,6}:)?(?P<host>[\w\-.^_]+)
    # Values:  TEXT
    #failregex = ^<HOST> .*" (?!\/robots\.txt).*" (400|401|403|404|503)\s
    failregex = ^<HOST> .* (400|401|403|404|405|503)\s

    # Option:  ignoreregex
    # Notes.:  regex to ignore. If this regex matches, the line is ignored.
    # Values:  TEXT
    #
    ignoreregex =

**fail2ban/action.d/iptables-common.conf**

Replace both of

* `blocktype = REJECT --reject-with icmp-port-unreachable`
* `blocktype = REJECT --reject-with icmp6-port-unreachable`

with

* `blocktype = DROP`

**fail2ban/filter.d/openvpn.conf**

    # Fail2Ban filter for openvpn
    # For custom logs ...

    [INCLUDES]

    # Read common prefixes. If any customizations available -- read them from
    # common.local
    before = common.conf

    [Definition]

    _daemon = openvpn

    failregex = ^%(__prefix_line)s<HOST>:[0-9][0-9]*.*possible active attack on the TCP.*$
                ^%(__prefix_line)s<HOST>:[0-9][0-9]*.*VERIFY ERROR:.*$
                ^%(__prefix_line)s<HOST>:[0-9][0-9]*.*TLS Auth Error:.*$
                ^%(__prefix_line)s<HOST>:[0-9][0-9]*.*TLS Error: TLS handshake failed.*$

    ignoreregex = 

**fail2ban/filter.d/sshd-tcpdos.conf**

    # Fail2Ban ssh filter for TCP packet not related to SSH protocol
    #
    # Protect SSH port from TCP DOS, e.g. echo "Some string" | nc server 22, telnet ...

    [INCLUDES]

    # Read common prefixes. If any customizations available -- read them from
    # common.local
    before = common.conf

    [Definition]

    _daemon = sshd

    failregex = ^%(__prefix_line)s.*Bad protocol version identification.*from <HOST> .*$

    ignoreregex = 

## Disable WHOIS details in email notification

**fail2ban/action.d/mail-whois-common.conf**

Replace

* `_whois = whois <ip> ...`

with

* `_whois = whois_disabledByAnsible <ip> || echo "missing whois program"`
