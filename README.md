# NetFoundry Edge Router OEM Image Tooling #
This documentation is for customers of the NetFoundry NPaaS (https://nfconsole.io) that wish to create their own branded and modified renditon of the available NetFoundry Edge Router image located in various different cloud marketplaces as well for bare-metal hypervisors.  Please see the available images and links to cloud marketplaces at (https://netfoundry.io/downloads). 

This utility continues the workflow which has established the image containing a complete OS (Ubuntu 22.04 LTS), kernel configurations, CIS Hardening, required application installation and updates, and the OpenZiti (https://github.com/openziti) Edge Router and supporting applications.  This utility uses two tools: Packer and Ansible.  It is only available for Linux/*nix due to the requirement for running Ansible.

**Packer:**
This tool, created and maintained by Hashicorp, handles launching a virtual machine in various platforms and producing an "image" that usable in each.  Find out more information at (https://www.packer.io/).  NetFoundry provides some example configurations based on the following plugins:
1. AWS - https://www.packer.io/plugins/builders/amazon/ebs
2. Azure - https://www.packer.io/plugins/builders/azure
3. GCP - https://www.packer.io/plugins/builders/googlecompute
4. VirtualBox - https://www.packer.io/plugins/builders/virtualbox/ovf

To download and install Packer see: https://www.packer.io/downloads

**Ansible:**
This tool, created and maintained by RedHat, provides a single source configuration mechanism in the form of "playbooks".  Find out more information at (https://www.ansible.com/).  NetFoundry provides a base playbook to allow an OEM to add custom branding and configuration to any platform originally available as an image.
To download and install Ansible see: https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html

**Overview:**
The process of creating images starts with Packer, and getting it configured to launch a virtual machine in a cloud of choice.  Once launched, an Ansible playbook is run against the Packer managed virtual machine.  After configuration is complete, Packer will export a usable virtual machine image in the corresponding platform.

**PreRequisites:**
To use this utility, you will need to have an environment (SHELL such as SH/ZSH/BASH) with Packer and Ansible in the run path.  Test your ability to launch Packer and Ansible by opening a terminal session and typing "packer"/ENTER and "ansible"/ENTER.  If either return in error, then you may not have the corresponding application installed and/or in your run path.

**Authentication:**
Each Packer configuration requires authentication to launch a virtual machine in the various platforms.  Please check the Plugins link above to find out more information for each platform.  For example, in AWS you can export the AWS (API) access-key, secret-key, and region to grant command line permisson to launch virtual machines.

**Example:**
Target: You wish to change only the ASCII art logo that is displayed upon login to the Edge Router such that it displays your company logo.  You want to make the virtual machine image that results from this available in AWS.
1. Clone this project and change into the project folder.
``` 
git clone https://github.com/netfoundry/edge-router-oem
cd edge-router-oem
```
3. Update and save new ASCII art located in the file "ansible/roles/common/files/custom.logo" with the editor of your choice (EG VIM).
```
vi ansible/roles/common/files/custom.logo
```
4. Review, modify, and save the "assume_role" section to match credentials permitted to perform launch and save functions in AWS in the file "aws.json".  Review the Packer "AWS" link above for options and methods of credential presentation as needed.
```
vi aws.json
```
6. Export key variables required for the building process in the SHELL.
```
export BUILD_NAME="CustomBuild"
export VERSION_NUMBER="1.0.0"
```
5. You may optionally validate your Packer configuration with:
```
packer validate aws.json
```
6. Build the new image with:
```
packer build aws.json
```

**Result:** Packer will find, download, and launch the NetFoundry Edge Router image in your VPC.  It will log into the SHELL of the running instance and launch Ansible with the modifications specified in the "ansible" directory.  This includes the new ASCII logo being placed into the appropriate place on the file system to be read upon user login.  After modifications are completed, Packer will stop the image, create a snapshot of it, form an Amazon Machine Image (AMI) from it, store it on the cloud, and remove the staging files used to create it.  You will be left with a new image (AMI) that contains all the modifications made and is ready to launch into an instance.
