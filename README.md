# Welcome to the NetFoundry Edge Router OEM Image tooling

This documentation if for those customers that wish to create their own images based on the NetFoundry Edge Router for various platforms.

The OEM process uses two tools, Packer & Ansible & is available Linux Only(due to Ansible)

**Packer:**

This tool, from Hashicorp is to handle launching a VM in various platforms & producing an "image" that usable in each.  Find out more information at https://www.packer.io/.  NetFoundry provides some example configurations base on the following plugins:

1. AWS - https://www.packer.io/plugins/builders/amazon/ebs
1. Azure - https://www.packer.io/plugins/builders/azure
1. GCP - https://www.packer.io/plugins/builders/googlecompute
1. VirtualBox - https://www.packer.io/plugins/builders/virtualbox/ovf

To download packer see: https://www.packer.io/downloads


**Ansible:**

This tool, from RedHat is used to provide a single source configuration.  Find out more information at https://www.ansible.com/. NetFoundry provides a base playbook to allow customer to add configuration to any platform.

To install ansible please see: https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html



**Overview:**

The process of creating images starts with packer & getting it configured to launch a VM in which ever cloud is desired. Once launched, an Ansible playbook is run against the packer maanged VM.  After configuration is complete, packer will export a usable VM image in corresponding platform.

**PreRequisites:**

To use this CLI tool, you will need to have an environment with ansible in the path.

**Authentication:**

Each packer configuration requires authentication to launch a VM in the various platforms.  Please check the Plugins link above to find out more information for each platform.  An example of this is AWS, you can export the AWS access-key, secret-key & region to grant access to lauch VMs.

**Example command:**

To create an image in AWS:

    export BUILD_NAME="CustomBuild"
    export VERSION_NUMBER="1.0.0"
    ./packer build aws.json

