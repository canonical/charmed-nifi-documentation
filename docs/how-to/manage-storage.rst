.. _how-to-manage-storage:

Manage persistent storage
=========================

Charmed Apache NiFi keeps its data on three Juju storage volumes, each mounted
into the NiFi container. They survive pod restarts.

.. list-table::
   :header-rows: 1
   :widths: 25 15 60

   * - Volume
     - Default size
     - What it holds
   * - ``nifi-data``
     - 10G
     - NiFi's working data, including the flowfile store (in-flight data)
   * - ``content-repo``
     - 20G
     - the bytes of the data flowing through NiFi
   * - ``provenance-repo``
     - 20G
     - the audit trail of what happened to each item of data

Set storage sizes at deploy time
--------------------------------

Storage sizes are chosen **when you deploy** and cannot be changed afterwards.
Pass ``--storage`` with the sizes you need:

.. code-block:: bash

   juju deploy nifi-k8s --channel 2.10/edge \
     --storage nifi-data=10G \
     --storage content-repo=50G \
     --storage provenance-repo=30G

Size ``content-repo`` for how much data you expect to flow through, and
``provenance-repo`` for how much history you want to keep.

Check storage usage
-------------------

List the volumes and their state:

.. code-block:: bash

   juju storage

See how full they are from inside the workload container:

.. code-block:: bash

   juju ssh --container nifi nifi-k8s/0 "df -h /var/lib/nifi"

.. important::

   If ``content-repo`` fills up, NiFi stops accepting new data until space is
   freed. Because volumes cannot be resized after deployment, size it generously
   at deploy time.
