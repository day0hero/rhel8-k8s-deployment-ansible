# rhel8-kubernetes-deployment
This role deploys kubernetes onto rhel8 machines in a single controlplane/multi-worker topology.<br>
It also deploys the Calico SDN by default using these [manifests](https://docs.projectcalico.org/manifests/calico.yaml)

### My environment Setup:
I'm using the rhel qcow2 (cloud) images for the deployment, so there are a few things that you may have to do that I didn't: <br>
- disable swap in '/etc/fstab'
- install firewalld package and start/enable the service

I manually copied the ssh-keys from my control-machine to each of the nodes.

**This is not a production ready deployment**<br>
I set selinux to `permissive` mode to get through the installation. I did enable the necessary ports in firewalld.

### Variable Usage:

| variable name | default value | usage |
|:-------------:|:-------------:|:-----:|
| rhsm_username | `empty` | This is the CDN username for RH customer portal |
| rhsm_passowrd | `empty` | Password for rhsm_username |
| k8s_version | 1.23 | Kubernetes Version, this is needed for k8s packages and CRIO |
| k8s_pod_network_cidr | 10.222.0.0/16 | Whatever CIDR you want as long as it doesn't collide with existing networks |
| deploy_nginx_ingress | true | Deploy nginx ingress controller - set to false to turn it off |
| deploy_metallb | true | Deploy metallb l2 loadbalancer |
| metallb.addressPool.name | default | Whatever you want this to be |
| metallb.addressPool.start | 192.168.1.235 | Start of your metallb address pool |
| metallb.addressPool.end | 192.168.1.238 | End of your metallb address pool |

### Sample Inventory File 
```yaml
controlplane:
  hosts:
    ctlplane.example.com:
      ansible_host: 192.168.122.220

workers:
  hosts:
    worker0.example.com:
      ansible_host: 192.168.122.149
    worker1.example.com:
      ansible_host: 192.168.122.244
```

### Sample `site.yml` playbook to call the role
```yaml
---
- name: Kubernetes Installer
  hosts: all
  remote_user: root
  roles:
    - rhel8-kubernetes-deployment
```
