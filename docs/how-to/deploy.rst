.. _how-to-deploy:

Deploy Charmed NiFi
===================

This guide walks you through deploying Charmed NiFi on Kubernetes using Juju.

Prerequisites
-------------

* A K8s cluster (v1.32+) with a Juju controller (v3.6+) bootstrapped on it.

  See `Set up your Juju deployment`_
  for a step-by-step guide.

* ``jq`` installed (used to retrieve the unit IP):

  .. code-block:: bash

     sudo apt-get install -y jq

Deploy the NiFi charm
---------------------

.. code-block:: bash

   juju deploy nifi-k8s --channel 2.10/edge

Configure the sensitive properties key
---------------------------------------

NiFi uses a *sensitive properties key* to encrypt passwords stored in flow
definitions. The charm remains in ``BlockedStatus`` until this key is
configured.

Generate a random key and store it as a Juju secret:

.. code-block:: bash

   juju add-secret nifi-sensitive-key \
     sensitive-props-key="$(openssl rand -base64 32)"

Grant the secret to the NiFi charm and configure it:

.. code-block:: bash

   juju grant-secret nifi-sensitive-key nifi-k8s
   juju config nifi-k8s sensitive-props-key=secret:<secret-id>

.. note::

   Replace ``<secret-id>`` with the secret ID returned by ``juju add-secret``.
   You can find it with ``juju list-secrets``.

Wait for the deployment
-----------------------

Monitor the deployment with ``juju status`` and wait until the NiFi application
shows ``active/idle``.

Access the NiFi UI
------------------

The NiFi web UI (the *canvas*) is served on port **8080**:

.. code-block:: bash

   juju status --format=json | jq -r '.applications["nifi-k8s"].units[]."address"'

Open ``http://<unit-ip>:8080/nifi/`` in your browser.

.. note::

   This works directly on MicroK8s. On other clusters, use
   ``kubectl port-forward`` to reach the unit.

.. image:: ../images/nifi-ui.png
   :alt: NiFi Canvas
   :align: center

.. note::

   The current edge release serves the UI over plain HTTP with no login.

Next steps
----------

* :doc:`/how-to/index` for more task-oriented guides.


