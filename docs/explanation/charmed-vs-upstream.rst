.. _explanation-charmed-vs-upstream:

Charmed NiFi vs upstream NiFi
==============================

`Apache NiFi`_ is an open-source project maintained by the Apache Software
Foundation. Charmed NiFi packages it as a `Juju`_ charm for Kubernetes, adding
operational tooling on top. This page explains what Charmed NiFi adds and
what it intentionally leaves unchanged.

What Charmed NiFi adds
-----------------------

**Day-0 deployment**
   A single ``juju deploy nifi-k8s`` command (or a Terraform module) is enough
   to run NiFi on Kubernetes — no Helm charts, no hand-editing of
   ``nifi.properties``.

**Declarative configuration**
   Juju config options (such as the sensitive properties key) are passed in as
   Juju secrets. The charm writes them into ``nifi.properties`` and restarts
   NiFi only when the rendered file actually changes.

**Persistent storage**
   Juju provisions and mounts three persistent volumes automatically —
   ``nifi-data`` (flow metadata), ``content`` (FlowFile bytes), and
   ``provenance`` (audit trail). They survive pod restarts without manual
   ``PersistentVolumeClaim`` management.

**Relation-driven integrations**
   Connecting NiFi to a Git repository or an ingress provider is a single
   ``juju integrate`` command. The charm reads the relation data and updates
   ``nifi.properties`` accordingly — no manual Registry Client setup required.

**Lifecycle management**
   Pebble manages the NiFi JVM process inside the container and exposes a
   health check. Juju monitors it and re-starts it if it exits unexpectedly.

What stays the same
--------------------

Charmed NiFi does not modify NiFi itself. The OCI image ships the upstream
NiFi binary unchanged. The canvas, the processor catalogue, the REST API, and
all NiFi configuration semantics are identical to upstream.

Sensitive properties key
-------------------------

NiFi encrypts passwords stored inside flow definitions (connection credentials,
controller service passwords, etc.) using a *sensitive properties key*. This
key is set via ``nifi.sensitive.props.key`` in ``nifi.properties``.

In upstream NiFi you set this key directly in the configuration file. In
Charmed NiFi the key is stored as a `Juju secret`_ and passed to the charm via
the ``sensitive-props-key`` config option, so it is never written to disk in
plain text or exposed in Juju model state.

**Key rotation is supported.** When you add a new revision to the Juju secret,
the charm:

1. Stops NiFi.
2. Runs ``nifi.sh set-sensitive-properties-key <new-key>``, which re-encrypts
   all existing sensitive values in the flow with the new key.
3. Writes the updated ``nifi.properties``.
4. Restarts NiFi.

To rotate the key:

.. code-block:: bash

   juju secret set <secret-id> sensitive-props-key="<new-key>"

NiFi will restart automatically once the charm picks up the secret revision
change.

Requirements for the key:

* Must be at least **12 characters** long (32 recommended).
* Must be provided as a Juju user secret exposing the field
  ``sensitive-props-key``.
* The charm enters ``BlockedStatus`` and will not start NiFi until the key is
  configured.

See :doc:`/how-to/deploy` for the exact commands to create and configure the
secret.
