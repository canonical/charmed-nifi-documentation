.. _how-to-troubleshoot:

Troubleshoot charm statuses
===========================

The charm reports its state in the ``Message`` column of ``juju status``. Start
there:

.. code-block:: bash

   juju status --relations

The messages fall into three kinds. In each entry below, the first line is the
message you see in ``juju status`` and the text under it is what to do.

Blocked: the charm needs you to act
-----------------------------------

No sensitive-properties key set
   ``Missing required config 'sensitive-props-key' (Juju user secret)``

   No key is set. Follow the :doc:`deploy guide <deploy>` to create and
   configure it.

Sensitive-properties secret cannot be read
   ``Cannot read 'sensitive-props-key' secret; ensure it exists, is granted to the application, and exposes field 'sensitive-props-key'``

   The secret isn't granted to the application, or its field is misnamed.
   Re-grant it with ``juju grant-secret`` and confirm the field is named
   ``sensitive-props-key``.

Sensitive-properties key too short
   ``'sensitive-props-key' must be at least 12 characters long``

   Use a longer key (32 characters recommended).

Git provider is using SSH
   ``NiFi flow registry does not support SSH auth; reconfigure git-integrator with credentials (personal access token)``

   Switch the Git provider to HTTPS with a personal access token; see the
   :doc:`Git integration guide <integrate/git-integrator>`.

Apache NiFi rejected the registry settings
   ``Failed to configure NiFi flow registry client``

   Check the Git provider's repository URL and token, then check the logs
   (below).

Apache NiFi did not accept a change
   ``Failed to write NiFi configuration``, ``Failed to (re)start NiFi service``,
   or ``Failed to rotate nifi.sensitive.props.key``

   Check the logs (below).

Waiting: paused for something to be ready
-----------------------------------------

These usually clear on their own once the related application is ``active``.

Waiting for the Git provider
   ``Waiting for git-registry relation data``

   The Git provider hasn't supplied its details yet. Confirm it is ``active``
   and correctly configured.

Waiting for ingress
   ``Waiting for ingress URL``

   An ingress relation exists but the ingress charm hasn't provided a URL yet.
   Confirm the ingress provider (for example Traefik) is ``active``.

In progress: transient
----------------------

These clear on their own; no action is needed unless they persist.

Apache NiFi is starting or the charm is retrying
   ``NiFi is starting``, ``Cannot connect to workload container``, or
   ``Failed to read nifi.properties from workload; will retry``

   Give it a moment.

Check the logs
--------------

Charm (operator) logs:

.. code-block:: bash

   juju debug-log --replay --include nifi-k8s/0

Apache NiFi's own application log, inside the workload container:

.. code-block:: bash

   juju ssh --container nifi nifi-k8s/0 "tail -n 50 /var/log/nifi/nifi-app.log"

If you're still stuck, open an issue on the `charm's GitHub repository
<Charmed Apache NiFi on GitHub_>`_ with the output of ``juju status`` and the relevant
log lines.
