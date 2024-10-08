

we can use Firewalld to filter packets,

and put `each interface` to a `zone` (which is a `set of rules`)

normally the `default` zone is `public`

in this zone every `INCOMMING` Connection is `Blocked`, except what we choose to allow

### in PUBLIC ZONE ---> default deny all

________________________________________________________________________________________________


## `--get-zones`

```bash
[bob@centos-host ~]$ firewall-cmd --get-zones

block dmz drop external home internal nm-shared public trusted work
```

________________________________________________________________________________________________


## `--get-default-zone`

```bash
[bob@centos-host ~]$ firewall-cmd --get-default-zone

public
```

________________________________________________________________________________________________




## `--set-default-zone=`

```bash
[bob@centos-host ~]$ sudo firewall-cmd --set-default-zone=public
Warning: ZONE_ALREADY_SET: public
success
```

________________________________________________________________________________________________



## `--get-active-zones`

```bash
[bob@centos-host ~]$ firewall-cmd --get-active-zones

public
  interfaces: eth0 eth1
```



________________________________________________________________________________________________


## `--list-all --zone=public`

```bash
[bob@centos-host ~]$ sudo firewall-cmd --list-all --zone=public

public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0 eth1
  sources: 
  services: cockpit dhcpv6-client ssh
  ports: 8080/tcp 22/tcp
  protocols: 
  forward: no
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
```






________________________________________________________________________________________________


## `--list-all`

to see current firewall rules

```bash
[bob@centos-host ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0 eth1
  sources: 
  services: cockpit dhcpv6-client ssh
  ports: 8080/tcp 22/tcp
  protocols: 
  forward: no
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules:
```

in the "services" you can see that `incomming connection` for these `services` are `allow`

in the "ports" you can see that `incomming connection` for these `ports` are `allow`

________________________________________________________________________________________________

## `--info-service=`

### to see what `port` is using each `service` (ex: cockpit)

```bash
[bob@centos-host ~]$ sudo firewall-cmd --info-service=cockpit

cockpit
  ports: 9090/tcp
```

________________________________________________________________________________________________


ex: if we want to allow incomming traffic to NGINX we have 2 ways:


## `--add-service=`


```bash
[bob@centos-host ~]$ sudo firewall-cmd --add-service=http
success
```

OR


## `--add-port=`

```bash
[bob@centos-host ~]$ sudo firewall-cmd --add-port=80/tcp
success
```

________________________________________________________________________________________________



## `--remove-service=`

```bash
[bob@centos-host ~]$ sudo firewall-cmd --remove-service=http
success
```

OR


## `--remove-port=`

```bash
[bob@centos-host ~]$ sudo firewall-cmd --remove-port=80/tcp
success
```

________________________________________________________________________________________________


# Allow incomming connections based on `SOURCE`

IF traffic that is comming from any ip of this network, then filter and process it according to the policy and rules of the `trusted` zone

## `--add-source= --zone=trusted`

```bash
[bob@centos-host ~]$ sudo firewall-cmd --add-source=1.2.3.4/24 --zone=trusted
success
```

By Default, the trusted zone allows every incomming connection, so we allow every connection from this source ip range

________________________________________________________________________________________________


## `--get-active-zones`


```bash
[bob@centos-host ~]$ sudo firewall-cmd --get-active-zones

public
  interfaces: eth0 eth1
trusted
  sources: 1.2.3.4/24
```

________________________________________________________________________________________________


# Block incomming connections based on `SOURCE`

IF traffic that is comming from any ip of this network, then filter and process it according to the policy and rules of the `block` zone

## `--add-source= --zone=block`

```bash
[bob@centos-host ~]$ sudo firewall-cmd --add-source=1.2.3.4/24 --zone=block
success
```

________________________________________________________________________________________________




## `--list-all --zone=`


### list all `rules` of a zone

```bash
[bob@centos-host ~]$ sudo firewall-cmd --list-all --zone=trusted 
trusted (active)
  target: ACCEPT
  icmp-block-inversion: no
  interfaces: 
  sources: 1.2.3.4/24
  services: 
  ports: 
  protocols: 
  forward: no
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
```

________________________________________________________________________________________________



## `--remove-source= --zone=`


```bash
[bob@centos-host ~]$ sudo firewall-cmd --remove-source=1.2.3.4/24 --zone=trusted 
success
```

________________________________________________________________________________________________



# all these rules are Temporary!

to make all rules permanent:

## `--runtime-to-permanent`



```bash
[bob@centos-host ~]$ sudo firewall-cmd --runtime-to-permanent
success
```

OR make them permanent at the creation


## `--permanent`

```bash
[bob@centos-host ~]$ sudo firewall-cmd --add-port=1234/tcp --permanent 
success
```

when you user --permanent , it will not be active for the current session! so we should run the command 2 times, without and with --permanent

________________________________________________________________________________________________







## `--add-interface= --zone=`

## `--change-interface= --zone=`

## `--remove-interface= --zone=`



### Assign/Change interface to/from a zone

```bash
[root@centos-host bob]$ firewall-cmd --add-interface=eth1 --zone=trusted 
Error: ZONE_CONFLICT: 'eth1' already bound to a zone

[root@centos-host bob]$ firewall-cmd --change-interface=eth1 --zone=trusted 
```

```bash
[root@centos-host bob]$ firewall-cmd --get-active-zones 

public
  interfaces: eth0
trusted
  interfaces: eth1
```
________________________________________________________________________________________________





## `--add-icmp-block=echo-request --zone=`



### Block incomming ICMP (ping)

```bash
[root@centos-host bob]$ firewall-cmd --add-icmp-block=echo-request --zone=public
```



________________________________________________________________________________________________
