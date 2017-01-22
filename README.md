# Automated iBFT testing

This git repository contains configuration files for all components needed to
automatically test iBFT installation and boot in Beaker environment.

## Test scenario

Our network topology is very simple, it only consists of storage server and
client connected to the same network. In our testing we use virtual machines
connected to isolated virtual network. All virtual resources are provisioned
using libvirt APIs.

```
+----------+             +----------+             +----------+
|  client  |-------------|  switch  |-------------|  server  | 
+----------+             +----------+             +----------+
```

```
Network IP: 192.168.1.0/24
client IP: 192.168.1.1/24
server IP: 192.168.1.254/24
```

After we provision virtual networks via libvirt we continue with server
setup. We aim to test iBFT installations on client side and thus there are no
special requirements for server operating system. We decided to use Red Hat
Enterprise Linux 7 as our storage server.

In order to prevent unrelated test failures we don't do server
installation. Instead we start server virtual machine via libvirt from latest
stable image that was already pre-provisioned. Only additional step we take is
iSCSI software stack installation and setup. We install and setup iSCSI using
Ansible playbook ```iscsi.yml```.

We install client using Anaconda installer. Installation configuration is
captured in ```client.ks``` file. In order to preconfigure client with iSCSI
configuration we use UEFI firmware NVRAM template that was gathered manually
[1].

In order to verify successfull instalation we wait for virt-install script to
finish. Secondly, we attempt ssh connection to the client machine (sshd
configuration is done via kickstart). As a last check we query installer logs
and we attempt to search for possible network configuration related errors.

## References
[1] https://www.mail-archive.com/edk2-devel@lists.01.org/msg04186.html


