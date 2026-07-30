.. _contribute:

Contribute
==========

Charmed NiFi is open source and contributions are welcome, whether to the charm
itself or to this documentation.

Contribute to the charm
-----------------------

The charm lives at
`canonical/nifi-k8s-operator <Charmed NiFi on GitHub_>`_.
See its ``CONTRIBUTING.md`` for how to set up a development environment, build the
charm, and run the tests.

Contribute to the documentation
-------------------------------

These docs live at
`canonical/charmed-nifi-documentation <Documentation repository_>`_.

To build them locally:

.. code-block:: bash

   git clone https://github.com/canonical/charmed-nifi-documentation.git
   cd charmed-nifi-documentation/docs
   make run

This serves the docs at ``http://127.0.0.1:8000`` with live reload. Other useful
checks:

.. code-block:: bash

   make spelling    # check spelling
   make linkcheck   # check for broken links
   make woke        # check for non-inclusive language

Open a pull request with your changes. Small fixes (typos, broken links) are just
as welcome as new pages.

Code of conduct
---------------

All participation is governed by the
`Ubuntu Code of Conduct`_.
