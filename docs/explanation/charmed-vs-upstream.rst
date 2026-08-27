.. _explanation-charmed-vs-upstream:

Charmed Apache NiFi vs upstream Apache NiFi
===========================================

`Apache NiFi`_ is an open-source project maintained by the Apache Software
Foundation. Charmed Apache NiFi packages it as a `Juju`_ charm for Kubernetes, adding
operational tooling on top. This page explains what Charmed Apache NiFi adds and
what it intentionally leaves unchanged.

What Charmed Apache NiFi adds
-----------------------------

**Day-0 deployment**
   A single ``juju deploy nifi-k8s`` command (or a Terraform module) is enough
   to run Apache NiFi on Kubernetes. No Helm charts, no hand-editing of
   ``nifi.properties``.

**Declarative configuration**
   Juju config options (such as the sensitive properties key) are passed in as
   Juju secrets. The charm writes them into ``nifi.properties`` and restarts
   Apache NiFi only when the rendered file actually changes.

**Persistent storage**
   Juju provisions and mounts three persistent volumes automatically:
   ``nifi-data`` (flow metadata), ``content`` (FlowFile bytes), and
   ``provenance`` (audit trail). They survive pod restarts without manual
   ``PersistentVolumeClaim`` management.

**Relation-driven integrations**
   Connecting Apache NiFi to a Git repository or an ingress provider is a single
   ``juju integrate`` command. The charm reads the relation data and updates
   ``nifi.properties`` accordingly, no manual Registry Client setup required.

**Lifecycle management**
   Pebble manages the Apache NiFi JVM process inside the container and exposes a
   health check. Juju monitors it and re-starts it if it exits unexpectedly.

What stays the same
--------------------

The canvas, the processor catalogue, the REST API, and all Apache NiFi
configuration semantics are identical to upstream.
