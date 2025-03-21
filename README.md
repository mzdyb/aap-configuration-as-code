# ...draft...in progress...
# Ansible Automation Platform Configuration as Code: Automating the Automation

## Overview

This repository provides an example of managing configurations for Ansible Automation Platform (AAP) using a Configuration as Code (CaC) approach. It allows us to use version controling and streamlined deployment processes that align with DevOps best practices. In this case AAP configuration is described using YAML-formatted variable files and configuration deployment is automated using Ansible. These variable files are the Source of Truth (SoT) for AAP configuration which means they define the intended state of our configuration. 

## Implementation

To automate AAP configuration with Ansible the following collections from ansible namespace can be used:
- ansible.platform
- ansible.hub
- ansible.controller
- ansible.eda

The other approach is to use [infra.aap_configuration](https://github.com/redhat-cop/infra.aap_configuration) collection from Validated Content collections and it is the approach used in this repository. Tha big advantage of it is that we can use [infra.aap_configuration.dispatch](https://github.com/redhat-cop/infra.aap_configuration/tree/devel/roles/dispatch) role which runs throught the all AAP configuration objects and handles for us ordering required to implement them (configuration has to be implemented in sepecific order, for example to configure Job Template we have to configure Project first).


The intended state of AAP configuration is defined in the files in 'aap_configuration' folder. It is up to us if we want to keep AAP configuration in one or separate files. Depending on our needs the good practice is to keep configuration hierarchical as separate files in structured directories. We can create AAP configuration from scratch but the easier approach is to use [controller_configuration.filetree_create](https://github.com/redhat-cop/aap_configuration_extended/tree/devel/roles/filetree_create) role from [aap_configuration_extended](https://github.com/redhat-cop/aap_configuration_extended) collection. This role connects to AAP and generates configuration files for us. At the time of creating this repository there were some issues with this role during the configuration discovery (hence 'ignore_errors: true' parameter in 'gather_configuration' playbook) but still it was very helpful for creating initial configuration files, just some final touches had to be added to cover missing parts.


When our data structure is defined and implemented on AAP it is up to us how we will be managing our AAP configuration after initial implementation. When we are running Ansible playbook and there is no configuration drift between SoT and AAP configuration will never be overwritten thanks to the crucial Ansible feature which is **idempotency**. When we want to remove some configuration object we might use the following workflow:

Removing CaC-configured AAP configuration objects:
  1. Add `state: absent` to configuration object which we want to remove. For example:
  ```
aap_organizations:
  - name: "my_organization"
    state: absent
  ```
  2. Run ansible playbook to implement configuration
  2. Verify removal in AAP UI/API
  3. Remove 'my_organization' entry from YAML file in new commit


## Additional resources
- [ansible-automation-controller-cac-gitops](https://www.redhat.com/en/blog/ansible-automation-controller-cac-gitops)
- [automation-controller-active-passive-architecture-cac](https://www.redhat.com/en/blog/automation-controller-active-passive-architecture-cac)
- [filetree_create automate the automation](https://github.com/redhat-cop/aap_configuration_extended/blob/devel/roles/filetree_create/automatetheautomation.md)


## Feedback
Feedback is always welcome! If you have any comments, please reach me out

## Author

[@mzdyb](https://www.linkedin.com/in/michal-zdyb-9aa4046/)