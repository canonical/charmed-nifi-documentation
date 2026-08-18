.. _how-to-integrate-git-integrator:

Integrate with Git Integrator
==============================

`Git Integrator`_ is a Juju charm designed to manage and share Git repository
details and credentials with consumer charms. By integrating it with Charmed
NiFi, you provide the connection details necessary to register a Git Flow
Registry Client in NiFi, enabling NiFi to load and version flow definitions
directly from your remote repository.

.. note::

   NiFi flow registry clients support token-based authentication only.
   SSH authentication is **not** supported. Configuring git-integrator with
   ``authentication_method = "ssh"`` will put the NiFi charm in
   ``BlockedStatus``.

Prerequisites
-------------

A running Charmed NiFi deployment. If you haven't deployed it yet, follow the
:doc:`deploy guide </how-to/Deploy Nifi>` first.

Deploy the Git Integrator charm
--------------------------------

.. code-block:: bash

   juju deploy git-integrator --channel 1.0/edge

Configure the charm
-------------------

At minimum, set the repository URL:

.. code-block:: bash

   juju config git-integrator repository_url="https://github.com/<org>/<repo>.git"

To track a specific branch (default is ``main``):

.. code-block:: bash

   juju config git-integrator tracking_ref="<branch>"

**For a private repository**, add a personal access token:

.. code-block:: bash

   juju add-secret git-pat credentials_personal_access_token="<your-token>"
   juju grant-secret git-pat git-integrator
   juju config git-integrator \
     authentication_method="credentials" \
     credentials_username="<your-username>" \
     credentials_personal_access_token_secret=secret:<secret-id>

.. note::

   Replace ``<secret-id>`` with the ID returned by ``juju add-secret``.
   You can find it with ``juju list-secrets``.

For full configuration options, see the `Git Integrator`_ documentation on
Charmhub.

Integrate with Charmed NiFi
----------------------------

.. code-block:: bash

   juju integrate git-integrator:git nifi-k8s:git-registry

Wait for all units to return to ``active/idle``:

.. code-block:: bash

   juju status --watch 5s

Once active, NiFi automatically creates a Flow Registry Client pointing to the
configured repository. You can find it in the NiFi UI under
**Controller Settings → Registry Clients**.

Remove the integration
-----------------------

.. code-block:: bash

   juju remove-relation git-integrator:git nifi-k8s:git-registry

.. _Git Integrator: https://charmhub.io/git-integrator
