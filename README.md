# Konductor

Run the following command on the desire host to bootstrap the host for Kubernetes

```ansible-pull -U https://github.com/mesosphere/konductor.git ansible/provision.yaml -c local -e work/ubuntu-18-1633827839-ZtTug/ansible_vars.yaml -e packer_builder_type=local -e sysprep=false -i inventory.yaml```
