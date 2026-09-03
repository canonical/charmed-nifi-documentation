.. _how-to-ingress:

Expose NiFi with ingress
========================

By default the Apache NiFi web UI is only reachable inside the Kubernetes
cluster. This guide gives it an external URL by integrating Charmed Apache NiFi
with an ingress provider.

When the integration is in place, the charm automatically configures NiFi's proxy
settings (``nifi.web.proxy.host`` and ``nifi.web.proxy.context.path``) from the
ingress URL, so there is no NiFi configuration to do by hand.

.. tip::

   If you deploy with the Terraform module, set ``traefik = { enabled = true }``
   in your ``terraform.tfvars`` rather than following the steps below. The module
   deploys the ingress provider and establishes the integration for you. See
   :doc:`/tutorials/deploy-using-terraform`.

Prerequisites
-------------

* A running Charmed Apache NiFi deployment (see :doc:`/how-to/deploy`).
* A Kubernetes cluster with a load balancer, so the ingress provider can obtain
  an external address. `Canonical Kubernetes
  <https://documentation.ubuntu.com/canonical-kubernetes/>`_ is recommended; see
  its `load-balancer guide
  <https://documentation.ubuntu.com/canonical-kubernetes/latest/snap/howto/networking/default-loadbalancer/>`_
  for setup.

Choose an ingress provider
--------------------------

The charm's ``ingress`` relation works with any charm that provides the
``ingress`` interface. There are two options:

* ``traefik-k8s``: supported today, but the charm is in maintenance mode.
* ``gateway-api-integrator``: the preferred direction, but not yet supported by
  Charmed Apache NiFi.

This guide uses ``traefik-k8s``, the option that works today.

Deploy the ingress provider
---------------------------

.. code-block:: bash

   juju deploy traefik-k8s --channel latest/stable --trust

Wait until it is ``active``; it needs an external address before it can route
traffic:

.. code-block:: bash

   juju status traefik-k8s

Integrate with Charmed Apache NiFi
----------------------------------

.. code-block:: bash

   juju integrate nifi-k8s:ingress traefik-k8s:ingress

Wait for both applications to return to ``active/idle``:

.. code-block:: bash

   juju status --watch 5s

.. note::

   Until the ingress provider publishes a URL, the NiFi charm shows ``Waiting for
   ingress URL``. This clears once the provider is ready.

Open NiFi through the ingress
-----------------------------

Ask the ingress provider for NiFi's external URL. With ``traefik-k8s``:

.. code-block:: bash

   juju run traefik-k8s/0 show-proxied-endpoints

The result contains a URL for ``nifi-k8s``. Open it in your browser with the
``/nifi/`` path appended, for example
``http://<external-address>/<model>-nifi-k8s/nifi/``.

.. note::

   This edge release still serves NiFi over plain HTTP with no login. Exposing it
   through ingress makes it reachable to anyone who can reach that URL, so only do
   this on a trusted network.

Remove the integration
----------------------

.. code-block:: bash

   juju remove-relation nifi-k8s traefik-k8s

The charm clears NiFi's proxy settings, and the UI is no longer reachable through
the ingress provider.
