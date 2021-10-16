# Konductor

Run the following command on the desire host to bootstrap the host for Kubernetes

```
ansible-pull -U https://github.com/millerjem/konductor.git ansible/provision.yaml -c local -e  work/ubuntu-18-1633827839-ZtTugexit^Cnsible_vars.yaml -i inventory.yaml
```
