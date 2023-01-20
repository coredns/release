# CoreDNS release scripts

This script `release-coredns` works in conjunctions `Makefile.release` in the CoreDNS repo and
[dreck](https://github.com/miekg/dreck).
The script `docker-coredns` works with `Makefile.docker` in the coredns repo.

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
    /release (.*) -> /exec /opt/bin/release-coredns $1
  _ |
    /docker (.*) -> /exec /opt/bin/docker-coredns $1
~~~

The version to release is determined from `coremain/version.go` and should already be submitted.

The full commandline synopsis is: `release-coredns [-t] [-v GOVERSION] BRANCH VERSION`. And for the
docker one: `docker-coredns [-t] VERSION`.

`-t` is used for testing.

## Releasing CoreDNS

Releasing CoreDNS consists out of three steps.

1. Get the master branch into shape.
2a. Open an issue and give the `/release master <version>` command in an issue comment. Where
    version if the version you are releasing.
2b. For docker you can do `/docker <version>` which should (hopefully) build some docker images.
3. Finalize the release notes and update https://coredns.io. The notes should also be committed
   to the coredns repo itself.

Further documentation can be found in Makefile.release and Makefile.docker in the coredns repo.

## Testing

The script can be tested with `./release-coredns -t`. The branch name and version are mandatory.
Same is true for `/docker-coredns`, this does everything except the actual upload.

## Security

**All** the output of the script will be put in a github issue comment, don't echo password an such
 in the script, thinking that would be a safe thing to do.
