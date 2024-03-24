# Linux Monitoring PoC

Simple Linux web server monitoring proof-of-concept setup using [Nginx ModSecurity+OWASP Core Rule Set](https://owasp.org/www-project-modsecurity-core-rule-set/) and [Falco]().

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
ansible-playbook playbooks/setup-monitoring.yml
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
