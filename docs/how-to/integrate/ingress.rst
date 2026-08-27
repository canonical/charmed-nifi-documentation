.. _how-to-ingress:

Expose NiFi with ingress
========================

By default the NiFi web UI is only reachable inside the Kubernetes cluster. This
guide gives it an external URL by integrating Charmed Apache NiFi with an ingress
provider, `Traefik <https://charmhub.io/traefik-k8s>`_.

When the integration is in place, the charm automatically configures NiFi's proxy
settings (``nifi.web.proxy.host`` and ``nifi.web.proxy.context.path``) from the
ingress URL, so there is no NiFi configuration to do by hand.

Prerequisites
-------------

* A running Charmed Apache NiFi deployment (see :doc:`/how-to/deploy`).
* A Kubernetes cluster that can give Traefik an external address (a
  LoadBalancer). On MicroK8s, enable MetalLB with a free range on your network,
  for example:

  .. code-block:: bash

     sudo microk8s enable metallb:10.64.140.43-10.64.140.49

Deploy Traefik
--------------

.. code-block:: bash

   juju deploy traefik-k8s --channel latest/stable --trust

Wait until Traefik is ``active``; it needs an external address before it can
route traffic:

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

   Until Traefik publishes a URL, the NiFi charm shows ``Waiting for ingress
   URL``. This clears once Traefik is ready.

Open NiFi through the ingress
-----------------------------

Ask Traefik for NiFi's external URL:

.. code-block:: bash

   juju run traefik-k8s/0 show-proxied-endpoints

The result contains a URL for ``nifi-k8s``. Open it in your browser with the
``/nifi/`` path appended, for example
``http://<traefik-address>/<model>-nifi-k8s/nifi/``.

.. note::

   This edge release still serves NiFi over plain HTTP with no login. Exposing it
   through ingress makes it reachable to anyone who can reach that URL, so only do
   this on a trusted network.

Remove the integration
----------------------

.. code-block:: bash

   juju remove-relation nifi-k8s traefik-k8s

The charm clears NiFi's proxy settings, and the UI is no longer reachable through
Traefik.
