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
    /release: -> /exec: /opt/bin/release
~~~

The version to release is determined from `coremain/version.go` and should already be submitted.
