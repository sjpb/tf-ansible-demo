# tf-ansible-demo

# Aim
Currently the slurm appliance uses Terraform vars to define the infrastructure inputs such as cluster name, node names, flavors etc. Terraform then templates out Ansible's inventory. Whilst the separation this provides between provisioning (with Terraform) and configuration (with Ansible) was considered desirable it is now getting somewhat painful:
- It is not clear where "inputs" should be set; some are Terraform vars, some are Ansible inventory vars.
- Terraform's functionality for logic is less expressive/harder to use than Ansible+Jinja templates.
- Incorporating cloud-init userdata, which itself depends on inventory, into the TF configuration requires a horrible terraform-ansible-terraform process.

This repo demonstrates an alternative process:
- Use a very simple, handwritten inventory file `cluster.yml` to define the infrastructure inputs (cluster name, node names, flavors, etc).
- Use this to template Terraform configuration.

As hosts and groups are defined in the initial inventory, group- and host-vars are available during templating making it easy to have default and overriden values.

It also demonstrates:
- How Terraform can be driven by Ansible while still retaining the nice property that user confirmation of changes is required.
- How Terraform blocks can be provided directly by inventory vars, which makes adding any possible network configuration trivial.

# Install

    sudo yum install python39
    python3.9 -m venv venv
    . venv/bin/activate
    pip install -U pip
    pip install -r requirements.txt

# Use

    . venv/bin/activate
    ansible-playbook -i cluster.yml create.yml

You can then run e.g.:

    ansible all -i cluster.yml -i hosts.yaml  -a hostname

using the generated `hosts.yml` file contains the `ansible_hostnames` to use for connection.
