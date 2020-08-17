# Ansible guide

Determine microk8s config and keep it handy
```microk8s config```

To start make sure a ptyhon virtualenv is selected.

```bash
pip install ansible openshift
ansible --version
openshift --version
```

To use Ansible configuration manager for kubernetes

```ansible-galaxy collection install community.kubernetes```

To run a playbook

```bash
ansible-playbook -i ansible/inventory ansible/microk8snginxplaybook.yml
```
