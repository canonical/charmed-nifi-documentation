Charmed NiFi
============

Charmed NiFi is a `Juju`_ charm that deploys and operates
`Apache NiFi`_ on Kubernetes.

Apache NiFi is a visual, no-code tool for moving and transforming data between
systems: you build a *flow* by dragging boxes onto a canvas and connecting them.
The charm packages NiFi so you can deploy it with a single command, keep its data
on persistent storage, and version your flows to a Git repository, without
hand-editing config files.

.. note::

   This is an early **edge** release of a **single-node** deployment, built for
   **amd64**. It is not yet production-ready: there is no clustering or high
   availability, and the web UI is served over plain HTTP with no login. See
   :doc:`reference/known-limitations`.

----

In this documentation
----------------------

.. grid:: 1 1 2 2

   .. grid-item-card:: Tutorial
      :link: tutorials/index
      :link-type: doc

      **Start here**: deploy Charmed NiFi and build your first flow, step by step.

   .. grid-item-card:: How-to guides
      :link: how-to/index
      :link-type: doc

      **Task recipes**: deploy, configure, integrate, and troubleshoot.

.. grid:: 1 1 2 2

   .. grid-item-card:: Reference
      :link: reference/index
      :link-type: doc

      **Look it up**: configuration, integrations, statuses, and limitations.

   .. grid-item-card:: Explanation
      :link: explanation/index
      :link-type: doc

      **Understand it**: NiFi concepts and how the charm is put together.

Project and community
---------------------

Charmed NiFi is an open source project that welcomes community contributions,
suggestions, fixes, and feedback.

* Read the `Ubuntu Code of Conduct`_
* Find the charm source on `GitHub <Charmed NiFi on GitHub_>`_
* Browse the `Apache NiFi documentation`_
* :doc:`Contribute to these docs <contribute/index>`

.. toctree::
    :hidden:
    :maxdepth: 1

    tutorials/index
    how-to/index
    reference/index
    explanation/index

.. toctree::
    :hidden:
    :maxdepth: 1

    release-notes/index
    contribute/index
