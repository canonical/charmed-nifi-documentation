.. _reference:

Reference
=========

Technical details you can look up: what the charm supports, how to configure it,
and what its integrations and statuses mean.

Configuration options
---------------------

Charmed Apache NiFi has a single configuration option.

``sensitive-props-key``
~~~~~~~~~~~~~~~~~~~~~~~~~

.. list-table::
   :widths: 25 75

   * - Type
     - ``secret`` (a Juju user secret)
   * - Required
     - Yes; the charm is **blocked** until it is set
   * - Secret field
     - ``sensitive-props-key``
   * - Constraint
     - At least 12 characters (32 recommended)

The Juju secret holding the NiFi sensitive-properties key
(``nifi.sensitive.props.key``), which NiFi uses to encrypt passwords stored in
flow definitions. See :doc:`/how-to/deploy` to create and configure it.

Integrations
------------

Charmed Apache NiFi has two optional integrations. Neither is required for the
charm to run.

``git-registry``
~~~~~~~~~~~~~~~~~

.. list-table::
   :widths: 25 75

   * - Interface
     - ``git``
   * - Role
     - requires
   * - Optional
     - Yes
   * - Limit
     - 1 (one Git provider)

Connects NiFi to a Git repository so that flows can be version-controlled.
Authentication is HTTPS with a personal access token; SSH is not supported. See
:doc:`/how-to/integrate/git-integrator`.

``ingress``
~~~~~~~~~~~

.. list-table::
   :widths: 25 75

   * - Interface
     - ``ingress``
   * - Role
     - requires
   * - Optional
     - Yes
   * - Limit
     - 1 (one ingress provider)

Exposes the NiFi web UI through an ingress provider such as Traefik, giving it an
external URL. Relate it with:

.. code-block:: bash

   juju integrate nifi-k8s <ingress-provider>

Charm statuses
--------------

The charm reports its state in the ``Message`` column of ``juju status``. The
tables below list the messages you may see and what each one means. For
resolution steps, see the :doc:`how-to guides </how-to/index>`.

Blocked
~~~~~~~

The charm needs you to act before it can continue.

.. list-table::
   :header-rows: 1
   :widths: 65 35

   * - Message
     - Meaning
   * - ``Missing required config 'sensitive-props-key' (Juju user secret)``
     - The sensitive-properties key is not set.
   * - ``Cannot read 'sensitive-props-key' secret; ensure it exists, is granted to the application, and exposes field 'sensitive-props-key'``
     - The secret is missing, not granted to the application, or its field is
       misnamed.
   * - ``'sensitive-props-key' must be at least 12 characters long``
     - The configured key is too short.
   * - ``NiFi flow registry does not support SSH auth; reconfigure git-integrator with credentials (personal access token)``
     - The Git provider is configured for SSH, which NiFi's registry does not
       support.
   * - ``Failed to configure NiFi flow registry client``
     - NiFi rejected the Git registry configuration.
   * - ``Failed to write NiFi configuration``
     - The charm could not write NiFi's configuration files.
   * - ``Failed to (re)start NiFi service``
     - NiFi did not start.
   * - ``Failed to rotate nifi.sensitive.props.key``
     - Re-encrypting the flow with a new key failed.

Waiting
~~~~~~~

The charm is paused until a related application is ready.

.. list-table::
   :header-rows: 1
   :widths: 65 35

   * - Message
     - Meaning
   * - ``Waiting for git-registry relation data``
     - A Git provider is related but has not supplied its details yet.
   * - ``Waiting for ingress URL``
     - An ingress provider is related but has not supplied a URL yet.

Maintenance
~~~~~~~~~~~

Transient states that clear on their own.

.. list-table::
   :header-rows: 1
   :widths: 65 35

   * - Message
     - Meaning
   * - ``NiFi is starting``
     - NiFi is booting.
   * - ``Cannot connect to workload container``
     - The workload container is not ready yet.
   * - ``Failed to read nifi.properties from workload; will retry``
     - A transient read error; the charm retries automatically.

Active
~~~~~~

An empty message with ``active/idle`` means NiFi is running normally.
