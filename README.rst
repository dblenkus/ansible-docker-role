Docker
======

|galaxy|

Ansible role to install and configure Docker container engine on RHEL/CentOS 7.

**Main features:**

- Supports ``overlay2`` and ``devicemapper`` (with direct LVM) Docker storage
  drivers.
- Uses ``container-storage-setup`` (also known as ``docker-storage-setup``) to
  set up Docker's storage.
- Prevents users from running ``devicemapper`` driver `on a loopback device by
  default`_.
- Sets up convenient passwordless sudo access for running Docker for selected
  users `as recommended by Dan Walsh`_, lead of Red Hat's Docker team.
- Supports authenticating with private Docker registries.

**Limitations:**

- Automatically applying changes to the Docker storage configuration for an
  existing system is not supported. You will have to manually remove the
  contents of the ``/var/lib/docker`` directory and then run the role again.

.. |galaxy| image:: https://img.shields.io/ansible/role/25555.svg
    :target: https://galaxy.ansible.com/tjanez/docker/
    :alt: Ansible Role

.. _on a loopback device by default:
  https://www.projectatomic.io/blog/2015/06/notes-on-fedora-centos-and-docker-storage-drivers/
.. _as recommended by Dan Walsh:
  http://www.projectatomic.io/blog/2015/08/why-we-dont-let-non-root-users-run-docker-in-centos-fedora-or-rhel/


Requirements
------------

This role requires Ansible 2.4 or higher.

It also requires RHEL/CentOS 7.4 or higher since OverlayFS support for SELinux
has been added in that version.


Role Variables
--------------

Variables in ``defaults/main.yml``:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

+-----------------------------------------+---------+---------------------------------------------+
|                Name                     |  Type   |                Description                  |
+=========================================+=========+=============================================+
| ``docker_storage_driver``               | string  | Storage driver to be used with Docker. It   |
|                                         |         | can be one of the following:                |
|                                         |         |                                             |
|                                         |         | * ``overlay2``: Use OverlayFS.              |
|                                         |         | * ``devicemapper``: Use Device Mapper       |
|                                         |         |   kernel framework.                         |
|                                         |         | * ``""``: Don't do any storage setup with   |
|                                         |         |   docker-storage-setup.                     |
|                                         |         |                                             |
|                                         |         | *NOTE:* The ``overlay`` storage driver is   |
|                                         |         | omitted since ``overlay2`` is more          |
|                                         |         | performant.                                 |
+-----------------------------------------+---------+---------------------------------------------+
| ``docker_storage_volumegroup``          | string  | Name of the LVM volume group to use for     |
|                                         |         | Docker storage.                             |
|                                         |         |                                             |
|                                         |         | *NOTE:* If the value is ``""``, the role    |
|                                         |         | will not configure Docker storage's         |
|                                         |         | volume group.                               |
|                                         |         |                                             |
|                                         |         | *NOTE:* This variable must be changed       |
|                                         |         | from the default value if the               |
|                                         |         | ``docker_storage_driver`` variable is set   |
|                                         |         | to ``devicemapper``.                        |
+-----------------------------------------+---------+---------------------------------------------+
| ``docker_sudo_users``                   | list    | List of users for which to configure        |
|                                         |         | passwordless sudo access for running        |
|                                         |         | Docker of the form:                         |
|                                         |         |                                             |
|                                         |         | .. code-block:: yaml                        |
|                                         |         |                                             |
|                                         |         |     name: string                            |
|                                         |         |     test (optional): boolean                |
|                                         |         |                                             |
|                                         |         | where ``name`` represents user's name and   |
|                                         |         | ``test`` indicates whether to test that     |
|                                         |         | convenience alias for running Docker        |
|                                         |         | works (defaults to ``true``).               |
+-----------------------------------------+---------+---------------------------------------------+
| ``docker_private_registries``           | list    | List of private Docker registries with      |
|                                         |         | which to authenticate the current system    |
|                                         |         | user of the form:                           |
|                                         |         |                                             |
|                                         |         | .. code-block:: yaml                        |
|                                         |         |                                             |
|                                         |         |    registry_url: string                     |
|                                         |         |    username: string                         |
|                                         |         |    password: string                         |
|                                         |         |    email: string                            |
|                                         |         |                                             |
|                                         |         | where ``registry_url`` (optional)           |
|                                         |         | represents registry's URL, ``username``     |
|                                         |         | represents user name with which to          |
|                                         |         | authenticate with the registry,             |
|                                         |         | ``password`` represents the user's          |
|                                         |         | password and ``email`` (optional)           |
|                                         |         | represents the user's email address.        |
|                                         |         |                                             |
|                                         |         | *NOTE:* ``email`` is required when          |
|                                         |         | authenticating with `Docker Hub`_.          |
+-----------------------------------------+---------+---------------------------------------------+

.. _Docker Hub: https://hub.docker.com/


Dependencies
------------

None.


Example Playbook
----------------

.. code-block:: yaml

    - hosts: all

      vars:
        docker_storage_volumegroup: dockervg
        docker_sudo_users:
          - alice
          - bob

      roles:
        - docker


License
-------

GPLv3


Author Information
------------------

Tadej Janež


