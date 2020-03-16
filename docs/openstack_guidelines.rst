.. _OpenStack_module_development:

OpenStack Ansible Modules
=========================

These are a set of modules for interacting with OpenStack as either an admin
or an end user. If the module does not begin with ``os_``, it's either deprecated
or soon to be. This document serves as developer coding guidelines for
modules intended to be here.

.. contents::
   :local:

Naming
------

* All module names should start with ``os_``
* Name any module that a cloud consumer would expect to use after the logical resource it manages: 
  ``os_server`` not ``os_nova``. This naming convention acknowledges that the end user does not care 
  which service manages the resource - that is a deployment detail. For example cloud consumers may
  not know whether their floating IPs are managed by Nova or Neutron.
* Name any module that a cloud admin would expect to use with the service and the resource: 
  ``os_keystone_domain``.
* If the module is one that a cloud admin and a cloud consumer could both use,
  the cloud consumer rules apply.

Interface
---------

* If the resource being managed has an id, it should be returned.
* If the resource being managed has an associated object more complex than
  an id, it should also be returned.

Interoperability
----------------

* It should be assumed that the cloud consumer does not know
  details about the deployment choices their cloud provider made. A best
  effort should be made to present one sane interface to the Ansible user
  regardless of deployer choices.
* It should be assumed that a user may have more than one cloud account that
  they wish to combine as part of a single Ansible-managed infrastructure.
* All modules should work appropriately against all existing versions of
  OpenStack regardless of upstream EOL status. The reason for this is that
  the Ansible modules are for consumers of cloud APIs who are not in a
  position to impact what version of OpenStack their cloud provider is
  running. It is known that there are OpenStack Public Clouds running rather
  old versions of OpenStack, but from a user point of view the Ansible
  modules can still support these users without impacting use of more
  modern versions.

Libraries
---------

* All modules should use ``OpenStackModule`` from
  ``ansible_collections.openstack.cloud.plugins.module_utils.openstack``
  as their base class.
* All modules should include ``extends_documentation_fragment: openstack``.
* All complex cloud interaction or interoperability code should be housed in
  the `openstacksdk <https://opendev.org/openstack/openstacksdk>`_
  library.
* All OpenStack API interactions should happen via the openstacksdk and not via
  OpenStack Client libraries. The OpenStack Client libraries do no have end
  users as a primary audience, they are for intra-server communication.
* All modules should be registered in ``meta/action_groups.yml`` for enabling the
  variables to be set in `group level
  <https://docs.ansible.com/ansible/latest/user_guide/playbooks_module_defaults.html>`.

Testing
-------

* Integration testing is currently done in `OpenStack's CI system
  <https://opendev.org/openstack/ansible-collections-openstack/src/branch/master/zuul.yaml>`