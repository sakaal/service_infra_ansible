
# Service Infrastructure

This is a configuration management database (CMDB)
for tracking configuration items (CIs) related to
deployment of hardware virtualization hypervisors
and a secure domain name system (DNS) for
administrative access during service transition.

A hypervisor is a physical infrastructure node for
hosting the virtual machines comprising the service.

The service management domain name system provides
identification, authentication, and key management
support for the service infrastructure resources
and their secure administration.

![Deployment diagram](service_infra_deployment.png "Deployment diagram")

## Current implementation

This Git repository contains Ansible playbooks for
the provision of new hardware virtualization hypervisors
configured for secure, unattended access, based on the
following external services, software applications and components:

Infrastructure service                    | Supported alternatives                                             | Budget estimate, without obligation, 2014
----------------------------------------- | ------------------------------------------------------------------ | -----------------------------------------
Hosting provider                          | [Hetzner Online](https://www.hetzner.de/en/)                       | Monthly 54 € per root server
Managed DNS provider with DNSSEC support  | [DynECT Managed DNS Express](https://dyn.com/managed-dns-express/) | Monthly 12 € for Express 5
Domain name registrar with DNSSEC support | [Dyn](https://dyn.com)                                             | Annual 19 € per domain

Infrastructure component  | Supported alternatives
------------------------- | --------------------------------------------------------
Server hardware           | Server computer with CPU hardware virtualization support
Operating System          | CentOS
Management access         | SSH
Revision Control          | Git
Configuration Management  | Ansible
Hypervisor                | Linux Kernel-based Virtual Machine (KVM)
Virtualization management | libvirt

Information source                | Reference
--------------------------------- | ----------------------
Secure Domain Name System (DNS)   | http://www.dnssec.net/

## Prerequisites

1. Competencies
    * Basic know-how of the Domain Name System (DNS)
    * Basic know-how of DNS Security Extensions (DNSSEC)
    * Intermediate know-how of Linux virtualization
1. Service Infrastructure configuration repository (this CMDB)
1. Administrative Client System (ACS) ready for use
1. Account with the hosting provider (requires credit card)
    * Hetzner Webservice API access
1. Account with the managed DNS provider (requires credit card)
    * DynECT API access
1. A domain name with DNSSEC support for management purposes
1. Main configuration repository for the service operation stage
1. Root access credentials and the IP addresses of one or more dedicated servers

### Registering a management domain name

When registering a management domain, make sure that your registrar
offers DNSSEC support for the specific TLD that you are planning to use.

Using a randomly generated name for the management domain may improve
privacy by making it more difficult to associate with the public
service instance name.

## Deployment procedure

### Obtaining the servers

* If you are deploying new servers, obtain one or more dedicated servers
  from the hosting provider to your account.
    * This may involve some of your account details
      being passed to RIPE for IP address registration.
    * Preparing the servers may take some time depending on the hosting provider.
      Some hosting providers process orders only during their office hours.
* If you are re-installing existing servers, bring them to their initial state
  according to your hosting provider's method.

Once you have the servers:

1. Perform a minimal operating system installation using whichever approach
   is most convenient in the hosting provider's environment.
    * [Hetzner installimage CentOS](Hetzner_installimage_CentOS.md)
1. Set a strong root account password as appropriate.
   It will only be used when preparing the host for automated configuration.
   Keep the credentials according to the administrative access plan, just in case.

### Configuring the servers

The playbooks are run on an Administrative Client System (ACS)
that has access to the service infrastructure nodes and external services
via a secure private channel over the Internet.

    git clone git@github.com:sakaal/service_infra_ansible.git

1. Examine until you understand the configuration files.
   Make working copies of the sample files as appropriate for your environment.
   Observe instructions found in the configuration files.
    * Add the management DNS zone default A record IP address
      to the `zones` inventory.
    * Add the target host addresses to the `bootstrap` inventory.

1. Open the sensitive data volume:

        sudo open_sensitive_data

1. If you haven't already created a security enabled DNS zone,
   you can deploy one using:

        sudo ansible-playbook -v dns.yml -i zones

1. If you are redeploying previously existed hosts,
   remove the old server keys from your known hosts file:

        ansible-playbook forget_servers.yml -i bootstrap

1. Prepare the hosts for automated configuration:

        ansible-playbook managed_servers.yml -ki bootstrap
    * You may need to provide the root password manually on the initial run.
    * You may need to accept the target host key fingerprint at first connect.

1. Configure the hosts as virtualization hypervisors:

        ansible-playbook hypervisors.yml -i bootstrap

1. After the new managed nodes are ready, they will be transferred over
   from the bootstrap inventory to the main configuration management inventory.

        ansible-playbook handovers.yml -i bootstrap
    * Ensure that the bootstrap inventory is again empty.
    * Manually push the target inventory changes to the main repository.

The hypervisors are now ready for service operation.

## Continual improvement

Please feel free to submit your proposals for specific improvements
to this process module, its solution alternatives, or configuration items,
as pull requests or issues to this repository.

Let the wiki serve as a part of the service knowledge management system (SKMS).

You may fork this module and adapt it for your needs (even for commercial use).
Please refer to the enclosed license documents for details.

Thank you for your interest and support to peer-to-peer service management.
