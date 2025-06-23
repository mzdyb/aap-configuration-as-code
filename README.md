# Ansible Automation Platform Configuration as Code: Automating the Automation

## Overview

This repository provides an example of managing configurations for Ansible Automation Platform (AAP) using a Configuration as Code (CaC) approach which allows us to use version controling and streamlined deployment processes that align with DevOps best practices. In this approach AAP configuration is described using YAML-formatted variable files and configuration deployment is automated using Ansible. These variable files are the Source of Truth (SoT) for AAP configuration which means that they define the intended state of our configuration. 

## Implementation
This section details the methods used for managing AAP CaC.

### Configuration Approaches
There are two primary approaches to automate AAP 2.5 configuration with Ansible. The first approach is to use the following collections from the ansible namespace directly:
- ansible.platform
- ansible.hub
- ansible.controller
- ansible.eda

The second approach is to use [infra.aap_configuration](https://github.com/redhat-cop/infra.aap_configuration) collection from Red Hat's Validated Content and it is the approach used in this repository. A significant advantage of this method is [infra.aap_configuration.dispatch](https://github.com/redhat-cop/infra.aap_configuration/tree/devel/roles/dispatch) role. This role iterates through all AAP configuration objects and handles the specific ordering required for their implementation (AAP configuration has to be implemented in specific order, for example a Project must be configured before a Job Template that uses it).

### Defining the State of Configuration
The intended state of the AAP configuration is defined in the files within the aap_configuration/ directory. It is a good practice to structure this configuration using multiple hierarchical files and directories depending on our needs.

If we already have existing&configured AAP instance we can create these configuration files from scratch but the easier method is to use [controller_configuration.filetree_create](https://github.com/redhat-cop/aap_configuration_extended/tree/devel/roles/filetree_create) role from [aap_configuration_extended](https://github.com/redhat-cop/aap_configuration_extended) collection. This role connects to AAP instance and automatically generates AAP configuration files for us. 

**Note:** *At the time of writing this text there were some issues with this role's configuration discovery process which is why 'ignore_errors: true' is used in the gather_configuration playbook. Despite this it remains a helpful tool for creating the initial file structure.*

### Managing Configuration Changes
Once the initial configuration is defined and applied on AAP instance all further AAP configuration changes should be made within our SoT (this repository) not directly on the AAP:
- to add new configuration object use 'state: present' variable for the corresponding task
- to remove existing configuration object use 'state: absent' variable

Below is the example workflow for removing a configuration object managed with AAP-CaC:
  1. Add `state: absent` to configuration object which we want to remove. For example:
  ```
aap_organizations:
  - name: "my_organization"
    state: absent
  ```
  2. Run ansible playbook to implement configuration change
  3. Remove 'my_organization' entry from YAML file in a new commit

## Additional resources
- [ansible-automation-controller-cac-gitops](https://www.redhat.com/en/blog/ansible-automation-controller-cac-gitops)
- [automation-controller-active-passive-architecture-cac](https://www.redhat.com/en/blog/automation-controller-active-passive-architecture-cac)
- [filetree_create automate the automation](https://github.com/redhat-cop/aap_configuration_extended/blob/devel/roles/filetree_create/automatetheautomation.md)


## Feedback
Feedback is always welcome! If you have any comments, please reach me out

## Author

[@mzdyb](https://www.linkedin.com/in/michal-zdyb-9aa4046/)