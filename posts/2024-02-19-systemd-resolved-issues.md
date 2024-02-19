---
title: It was DNS (Well, DHCP)
created: 2024-02-19T01:33:09
modified: 2024-02-19T01:34:52
tags:
  - admins
  - dns
author: distro
---

## The Problem

Systemd creates a symlink from `/run/systemd/resolve/stub-resolv.conf` to the "current" `/etc/resolv.conf`. This is fine, and works as expected, but when retrieving a DHCP lease from a DHCP server, `dhcpcd` by default will overwrite `/etc/resolv.conf`, and by proxy, overwriting the symlinked location in `/run`.

## The Initial Resolution (`dig +short`)

```
mojito@glados:~ sudo crontab -l
* */1 * * * systemctl restart systemd-resolved.service`
```

If you don't know what's this `cron` does, it restarts the service that configures DNS on `aperture` every hour. It's like replacing your sink because you haven't properly turned off your tap.

Not exactly a long term solution.

## The Real Resolution (`dig @ns1.rb.dcu.ie`)

We can tell `dhcpcd` to not update `resolv.conf` during it's lease renew by adding a hook to the workflow.

On `glados`, `wheatley` and `chell`:

```bash
$ cat /etc/dhcp/dhclient-enter-hooks.d/nodnsupdate
#!/bin/sh
make_resolv_conf(){
	:
}
$ chmod +x /etc/dhcp/dhclient-enter-hooks.d/nodnsupdate
```

All's well that ends well.

Cheers for joining this short blog!

`~distro`
