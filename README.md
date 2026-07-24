# Charmed NiFi documentation

This repository hosts the source for the Charmed NiFi documentation, built with Sphinx
and published on Read the Docs.

Charmed NiFi is a Juju charm for deploying and operating [Apache
NiFi](https://nifi.apache.org/) on Kubernetes.

## Build the documentation locally

Clone the repository and navigate to the `docs` directory:

```shell
git clone git@github.com:canonical/charmed-nifi-documentation.git
cd charmed-nifi-documentation/docs
```

Then run:

```shell
make run
```

This creates a Python virtual environment, installs the necessary dependencies, builds
the documentation, and serves it at http://127.0.0.1:8000.

Other useful targets:

```shell
make html        # build the HTML output
make spelling    # check spelling
make linkcheck   # check for broken links
make woke        # check for non-inclusive language
```

## Community and support

Charmed NiFi is an open-source project that warmly welcomes community involvement.

If you're new to the community, read through the [Ubuntu Code of
Conduct](https://ubuntu.com/community/code-of-conduct) first.

### Reach out

* Report an issue or make a suggestion via
  [GitHub](https://github.com/canonical/charmed-nifi-documentation/issues)
* Come chat with the Canonical Documentation team in our [public Matrix
  channel](https://matrix.to/#/#documentation:ubuntu.com)

### Contribute

* See [CONTRIBUTING.md](CONTRIBUTING.md) for details on contributing to the
  documentation.
* Check the [open issues](https://github.com/canonical/charmed-nifi-documentation/issues).

## License

The Charmed NiFi documentation is licensed under the [GPL-3.0 License](LICENSE).
