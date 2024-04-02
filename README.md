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
