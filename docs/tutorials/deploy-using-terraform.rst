.. _how-to-deploy-terraform:

Deploy with Terraform
=====================

This guide shows how to deploy Charmed Apache Nifi using `Terraform`_ and the
`Juju Terraform Provider`_. Terraform automates the deployment of the Apache Nifi charm
and its configuration in a single, reproducible plan.

Prerequisites
-------------

* A Juju controller (v3.6+) bootstrapped on a Kubernetes cluster.
  See the :doc:`deploy guide </how-to/deploy>` for setup instructions.
* `Terraform CLI`_ (v1.12+).

Clone the Charmed Apache Nifi Solutions repository
--------------------------------------------------

Clone the ``charmed-nifi-solutions`` repository that contains the Terraform
module:

.. code-block:: bash

   git clone https://github.com/canonical/charmed-nifi-solutions.git
   cd charmed-nifi-solutions/modules/charmed-nifi

Deploy
------

Create a Juju model:

.. code-block:: bash

   juju add-model nifi

Apache Nifi uses a *sensitive properties key* to encrypt passwords stored in flow
definitions. The Terraform module creates a Juju secret from this value,
grants it to the Apache Nifi charm, and configures the charm automatically.

Generate a random key:

.. code-block:: bash

   openssl rand -base64 32

Create a ``terraform.tfvars`` file:

.. code-block:: hcl

   model_uuid = "<your-model-uuid>"

   nifi_k8s = {
     config = {
       "sensitive-props-key" = "<your-random-key>"
     }
   }

.. note::

   Replace ``<your-model-uuid>`` with your Juju model UUID. You can find it
   with:

   .. code-block:: bash

      juju show-model nifi --format=json | jq -r '.nifi["model-uuid"]'

   Replace ``<your-random-key>`` with the value generated above.

Initialise and apply:

.. code-block:: bash

   terraform init
   terraform apply --var-file="terraform.tfvars"

Monitor the deployment with ``juju status`` and wait until the Apache Nifi application
shows ``active/idle``.

Customise charm parameters
--------------------------

The Apache Nifi charm accepts an object with optional overrides:

.. code-block:: hcl

   nifi_k8s = {
     app_name = "custom-name"       # optional
     channel  = "2.10/edge"         # optional
     units    = 1                   # optional
     config   = {}                  # optional, map(string)
     revision = null                # optional, pin a specific revision
   }

See the `Charmed Apache Nifi Solutions`_ README for the full variable reference.

Expose the UI with ingress
--------------------------

By default the Apache Nifi UI is only reachable inside the Kubernetes cluster.
Add a ``traefik`` block to ``terraform.tfvars`` and apply again: the module
deploys Traefik, relates it to Apache Nifi, and the charm configures its own
proxy settings from the ingress URL.

.. code-block:: hcl

   traefik = {
     enabled = true
   }

This needs a Kubernetes cluster with a load balancer, so that Traefik can obtain
an external address. To set this up by hand instead, or to read about the
available ingress providers, see :doc:`/how-to/integrate/ingress`.

Access the Apache Nifi UI
-------------------------

After deployment completes, retrieve the Apache Nifi unit IP:

.. code-block:: bash

   juju status --format=json \
     | jq -r '.applications["nifi"].units[]."address"'

Open ``http://<unit-ip>:8080/nifi/`` in your browser to reach the Apache Nifi canvas.

If you enabled ingress, ask Traefik for the external URL instead:

.. code-block:: bash

   juju run traefik/0 show-proxied-endpoints

Open the URL it returns with ``/nifi/`` appended.

.. note::

   The current edge release serves the UI over plain HTTP with no login.

Tear down
---------

To remove the deployment:

.. code-block:: bash

   terraform destroy --var-file="terraform.tfvars"

This removes the Apache Nifi application, Traefik if it was enabled, and the
associated resources from the model.

Next steps
----------

* Explore the `Charmed Apache Nifi Solutions`_ repository for more information.


