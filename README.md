# Linux Monitoring PoC

Simple Linux web server monitoring proof-of-concept setup using [Nginx ModSecurity+OWASP Core Rule Set](https://owasp.org/www-project-modsecurity-core-rule-set/) and [Falco](https://falco.org/).

## Requirements

- [Qemy](https://www.qemu.org/)
- [Libvirt](https://libvirt.org/)
- [Vagrant](https://www.vagrantup.com/)
- [Vagrant-libvirt plugin](https://vagrant-libvirt.github.io/vagrant-libvirt/configuration.html)
- [Ansible](https://docs.ansible.com/)

## Initial Steps

```bash
# start servers
export LIBVIRT_DEFAULT_URI=qemu:///system
export VAGRANT_DEFAULT_PROVIDER=libvirt
vagrant box update
vagrant up

# install required Ansbile roles
ansible-galaxy install -r requirements.yml
```

## Elastic Stack

```bash
# configure elastic stack
ansible-playbook playbooks/deploy-elk.yml

# connect to ELK node
vagrant ssh elk

# create test event
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"message":"Hello, world!"}' \
  http://192.168.122.10:8080/
```

## Deploy Web Application

```bash
# deploy Damn Vulnerable Web Application
ansible-playbook playbooks/deploy-dvwa.yml

# set up Falco
ansible-playbook playbooks/deploy-falco.yml
```

## Firewall

```bash
# set up firewall
ansible-playbook playbooks/deploy-fw.yml
```

```bash
# get all zones
firewall-cmd --get-zones
# block dmz docker drop external home internal public trusted work

# Check what services are associated with the Docker zone
firewall-cmd --list-all --zone=docker
# docker (active)
#   target: ACCEPT
#   icmp-block-inversion: no
#   interfaces: br-04fecbef2341 docker0
#   sources:
#   services:
#   ports:
#   protocols:
#   forward: yes
#   masquerade: no
#   forward-ports:
#   source-ports:
#   icmp-blocks:
#   rich rules:

# List chains
iptables -vnL
nft list ruleset

# Rule to log all incoming traffic setting log level to 4
firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p all -s 0.0.0.0/0 -m state --state NEW -j LOG --log-prefix "INPUT " --log-level 4

# Rule to log all outgoing traffic setting log level to 4
firewall-cmd --direct --add-rule ipv4 filter OUTPUT 0 -p all -s 0.0.0.0/0 -m state --state NEW -j LOG --log-prefix "OUTPUT " --log-level 4

# To check if the rules are applied
firewall-cmd --direct --get-all-rules

# To make the rules permanent
firewall-cmd --runtime-to-permanent

# To list all direct permanent rules
firewall-cmd --direct --get-all-rules --permanent

# Logs are being written in either /var/log/syslog or /var/log/messages
```

```bash
# alma
nc -l 0.0.0.0 8888

# ubuntu
nc -lvp 8888

# test open ports with NMAP
nmap -Pn -sSV -p 80,3306,8001,8002,8080,8888,8000 192.168.122.20
```

## References

- [geerlingguy.docker Ansible Role](https://galaxy.ansible.com/geerlingguy/docker)
- [juju4.falco Ansible Role](https://galaxy.ansible.com/juju4/falco)
- [Install Kibana with Docker](https://www.elastic.co/guide/en/kibana/current/docker.html)
- [Install Elasticsearch with Docker](https://www.elastic.co/guide/en/elasticsearch/reference/8.7/docker.html)
- [Running Falco](https://falco.org/docs/getting-started/running/)
- [DAMN VULNERABLE WEB APPLICATION](https://github.com/digininja/DVWA)
- [owasp/modsecurity-crs](https://hub.docker.com/r/owasp/modsecurity-crs/)
- [ipt-netflow](https://github.com/aabc/ipt-netflow)
- [fluentbit](https://fluentbit.io/)
- [Use Compose Files with Podman](https://docs.oracle.com/en/learn/podman-compose/index.html#introduction)
- [Podman 4.0's new network stack: What you need to know](https://www.redhat.com/sysadmin/podman-new-network-stack)
- [Rootless Docker-Compose with Podman](https://brandonrozek.com/blog/rootless-docker-compose-podman/)
- [Dockerhub Falco](https://hub.docker.com/r/falcosecurity/falco)
- [Error error opening device /host/dev/falco0](https://github.com/falcosecurity/falco/issues/2469)
- [Falco Docker](https://falco.org/docs/install-operate/running/#docker)
- [Logging all inbound and outbound traffic with firewalld direct rules](https://www.suse.com/support/kb/doc/?id=000020833)
- [Why is firewalld allowing public traffic to my non-public ports, bound to Docker containers?](https://serverfault.com/questions/1077849/why-is-firewalld-allowing-public-traffic-to-my-non-public-ports-bound-to-docker)
- [Packet filtering and firewalls](https://docs.docker.com/network/packet-filtering-firewalls/)
- [How to Secure a Docker Host Using Firewalld](https://dev.to/soerenmetje/how-to-secure-a-docker-host-using-firewalld-2joo)
- [Securing Docker ports to local access only with firewalld](https://talk.plesk.com/threads/securing-docker-ports-to-local-access-only-with-firewalld.368775/)
- [A beginner's guide to firewalld in Linux](https://www.redhat.com/sysadmin/beginners-guide-firewalld)
- [Docker - Hardening with firewalld](https://blog.nuvotex.de/docker-hardening-with-firewalld/)
