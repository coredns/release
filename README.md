# CoreDNS release script

This script `release-coredns` works in conjunctions `Makefile.release` in the CoreDNS repo and
[dreck](github.com/miekg/dreck).

A server needs to be configured with *dreck* and access to this script. In the configuration section
for dreck, you'll need to set the following environment variables:

* `GITHUB_ACCESS_TOKEN`
* `DOCKER_LOGIN`
* `DOCKER_PASSWORD`

This script will download the latest Go, setup a compile environment, and call into
`Makefile.release` to compile CoreDNS and upload it to github (hence `GITHUB_ACCESS_TOKEN`), and
upload it to the docker hub (hence `DOCKER_`). Note the webserver needs to be able to build docker
images, this requires the user it runs at be to in the docker group(s).

The `OWNERS` file of CoreDNS must allow this script to be called, so it will need a section like:

~~~ yaml
aliases:
  - |
    /release (.*) -> /exec: /opt/bin/release-coredns $1
~~~

The version to release is determined from `coremain/version.go` and should already be submitted.

The full commandline synopsis is: `release-coredns [-t] BRANCH VERSION`.

## Releasing CoreDNS

Releasing CoreDNS consists out of three steps.

1. Get the master branch into shape.
2. Open an issue and give the `/release: master <version>` command in an issue comment. Where
   version if the version you are releasing.
3. Finalize the release notes and update https://coredns.io.

### Step 1

* Up the version in coremain/version.go to the version to be released.
* Create the man pages with a `make -f Makefile.doc` (requires ronn to be installed).
* Perform a `go generate`.
* Send PR to get this merged.

### Step 2

* Open an issue for this release, name it 'CoreDNS Release VERSION'.
* In an issue comment give the command: `/release: master VERSION`.

### Step 3

Finalize the release notes and publicize the new release.

## Testing

The script can be tested with `./release-coredns -t`. The branch name and version are mandatory.
