.. _tutorials:

Tutorial
========

New to Charmed Apache NiFi? This tutorial takes you from an empty machine to a working Apache NiFi with your first data flow running on it. No prior Apache NiFi or Juju experience is needed. Every command is spelled out.

By the end you will have:

* a single-node Charmed Apache NiFi running on a local Kubernetes,
* the Apache NiFi web UI (the *canvas*) open in your browser, and
* a small flow that generates data and logs it: proof that data is moving.

It takes about **30 minutes** and runs entirely on your own machine.

What you need
-------------

* A Linux machine (Ubuntu recommended) on **amd64**.
* At least **4 GB of free RAM** and **60 GB of free disk** (Apache NiFi keeps three data stores on disk).
* A terminal and an internet connection.

.. toctree::
  :maxdepth: 1

  deploy-using-terraform
  first-flow
