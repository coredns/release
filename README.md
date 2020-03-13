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
upload it to the docker hub (hence `DOCKER_`). Note the web server needs to be able to build docker
images, this requires the user it runs at to be in the docker group(s).

The `.dreck.yaml` file of CoreDNS must allow this script to be called, so it will need a section like:

~~~ yaml
aliases:
  - |
    /release (.*) -> /exec: /opt/bin/release-coredns $1
~~~

The version to release is determined from `coremain/version.go` and should already be submitted.

The full commandline synopsis is: `release-coredns [-t] [-v GOVERSION] BRANCH VERSION`.

## Releasing CoreDNS

Releasing CoreDNS consists out of three steps.

1. Get the master branch into shape.
2. Open an issue and give the `/release master <version>` command in an issue comment. Where
   version if the version you are releasing.
3. Finalize the release notes and update https://coredns.io. The notes should also be committed
   to the coredns repo itself.

### Step 1

1. Up the version in coremain/version.go to the version to be released.
1. Create the man pages with a `make -f Makefile.doc` (requires mmark to be installed).
1. Perform a `go generate`.
1. Send PR to get this merged.

Not the steps 2. and 3. have been automated by GitHub actions that run on every merged PR, so this
is not needed any more.

### Step 2

* Open an issue for this release, name it 'CoreDNS Release VERSION'.
* In an issue comment give the command: `/release master VERSION`.

Doing a release with a different Go version than the latest released one:

* `/release -v 1.13.8 master VERSION`

And actual example: `/release -v 1.13.8 master 1.6.8`

### Step 3

Finalize the release notes and publicize the new release.

## Testing

The script can be tested with `./release-coredns -t`. The branch name and version are mandatory.

## Security

**All** the output of the script will be put in a github issue comment, don't echo password an such
 in the script, thinking that would be a safe thing to do.
