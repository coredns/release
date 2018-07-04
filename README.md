# CoreDNS release script

This script `release-coredns` works in conjunctions `Makefile.release` in the CoreDNS
repo and [dreck](github.com/miekg/dreck).

A server needs to be configured with *dreck* and access to this script. In the configuration
section for dreck, you'll need the following environment variables set.

* GITHUB_ACCESS_TOKEN
* DOCKER_LOGIN
* DOCKER_PASSWORD

This script will download the latest Go, setup a compile environment, compile CoreDNS and upload
is to github (hence GITHUB_ACCESS_TOKEN), and upload it to the docker hub (hence DOCKER_).

The OWNERS file of CoreDNS must allow this script to be called, so it will need a section like:

~~~ yaml
aliases:
  - |
    /release -> /exec: /opt/bin/release
~~~

The version to release is determined from `coremain/version.go` and should already be submitted.

## Releasing CoreDNS

Releasing CoreDNS consists out of two steps.

1. Get the master branch into shape
2. Open an issue and give the `/release` command

### Step 1

* Up the version in coremain/version.go to the version to be released.
* Create the man pages with a `make -f Makefile.doc` (requires ronn to be installed).
* perform a `go generate`
* Send PR to get this merged.

### Step 2

* Open an issue for this release, name it 'CoreDNS Release VERSION'
* In an issue comment give the command: /release

### Step 3

Finalize the release notes and publicize the new release.

This will trigger this release script that should automatically create all the release artifacts.
The last lines of `release-coredns` show the make target we call to release.
