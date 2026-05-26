*********************************************
Amazon Web Services driver installation guide
*********************************************

Requirements
============

* An AWS account with EC2 permissions
* A VPC subnet with auto-assign public IP enabled (or set ``assign_public_ip: true``)

Install
=======

Please refer to the `Virtual environment`_ documentation for installation best
practices. If not using a virtual environment, please consider passing the
widely recommended `'--user' flag`_ when invoking ``pip``.

.. _Virtual environment: https://virtualenv.pypa.io/en/latest/
.. _'--user' flag: https://packaging.python.org/tutorials/installing-packages/#installing-to-the-user-site

.. code-block:: bash

    $ pip install 'molecule-plugins[ec2]'
    $ ansible-galaxy collection install amazon.aws community.crypto

Environment variables
=====================

.. code-block:: bash

    export AWS_ACCESS_KEY_ID=<your-key-id>
    export AWS_SECRET_ACCESS_KEY=<your-secret>

    # Optional: pin a specific subnet. When unset, the default VPC subnet with the
    # most available IPs in eu-west-1 is discovered automatically.
    export MOLECULE_VPC_SUBNET_ID=<subnet-id>

Notes
=====

* Region is set to ``eu-west-1`` by default. Override by editing ``molecule.yml``.
* Instance type defaults to ``t3.small``.
* AMI is auto-discovered (latest Debian 13 Trixie from the official Debian account). Set
  ``image`` in ``molecule.yml`` to pin a specific AMI ID.
* SSH keys and security groups are created and destroyed automatically per run.
