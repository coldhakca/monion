# monion

monion is a set of instructions on how to monitor onions w/ Nagios.

## Requirements

Working Nagios setup and the following packages.

*	tor / torsocks
	*	For Debian, follow [Tor Project Debian Instructions](https://www.torproject.org/docs/debian.html.en) summarized here:
		*	`echo deb http://deb.torproject.org/torproject.org jessie main > /etc/apt/sources.list.d/tor.list`
		*	`echo deb-src http://deb.torproject.org/torproject.org jessie main >> /etc/apt/sources.list.d/tor.list`
		*	`gpg --keyserver keys.gnupg.net --recv 886DDD89`
		*	`gpg --export A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89 | apt-key add -`
		*	`apt-get update`
		*	`apt-get install tor torsocks deb.torproject.org-keyring`

## Nagios setup

*	Add the following new command to your Nagios configuration to monitor various types of onions:

```
define command {
	command_name		check_http_tor
	command_line		/usr/bin/torsocks /usr/lib/nagios/plugins/check_http -H '$HOSTADDRESS$' -I 	'$HOSTADDRESS$' '$ARG1$'
	}
define command {
	command_name		check_ssh_tor
	command_line		/usr/bin/torsocks /usr/lib/nagios/plugins/check_ssh -H '$HOSTADDRESS$' '$ARG1$'
	}

define command {
	command_name		check_tcp_tor
	command_line		/usr/bin/torsocks /usr/lib/nagios/plugins/check_tcp -H '$HOSTADDRESS$' '$ARG1$'
	}
```

*	Apply the service check to a host:

```
define host {
	use					generic-host            ; Name of host template to use
    host_name			samplea8s7df23jh7.onion
    alias				Sample Fake Onion
    address				samplea8s7df23jh7.onion
	check_command		check_http_tor!-p 8080
}


define service {
	use					generic-service		; name of template to use
	host_name			samplea8s7df23jh7.onion
	service_description	SSH
	check_command		check_ssh_tor!-p 8492
}

define service {
	use					generic-service		; name of template to use
	host_name			samplea8s7df23jh7.onion
	service_description	XMPP
	check_command		check_tcp_tor!-p 5222
}

```

