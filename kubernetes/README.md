# Kubernetes CoreDNS Update Release Process

This document helps define the steps to be followed after a CoreDNS release to update the image/manifests for CoreDNS in the Kubernetes (and related) projects.
After a [CoreDNS release](https://github.com/coredns/release) has been completed by one of the maintainers:


1. Update the [CoreDNS deployment](https://github.com/coredns/deployment/blob/master/kubernetes/coredns.yaml.sed) for the latest release version and ensure all the CI tests pass.
But do not merge yet.

2. Open a PR in the [Kubernetes main repository](https://github.com/kubernetes/kubernetes) that tests the new image (in Kubernetes e2e) before it is pushed to gcr.io. 
      * Clearly title the issue that this is a test PR, not intended for merge.
      * Comment the `/hold` command on the PR so that the PR does not merge by mistake.

3. If the Kubernetes pre-submit e2e tests pass, then close the above kubernetes/kubernetes PR, and merge the coredns/deployment PR.

4. Open an issue in the [Kubernetes main repository](https://github.com/kubernetes/kubernetes) to get the CoreDNS image pushed to the gcr.io repository. 
       * Follow this example issue opened for previous releases: https://github.com/kubernetes/kubernetes/issues/69983. 
       * The issue should be assigned to a Googler, who has permissions to push the image to the gcr.io repository. 
         Currently the person to assign the issue to is [Tim Hockin](https://github.com/thockin).

5. Once the gcr.io image has been pushed, open a PR in the [Kubernetes main repository](https://github.com/kubernetes/kubernetes) to update the CoreDNS image.
for both [kube-up](https://github.com/kubernetes/kubernetes/tree/master/cluster/addons/dns/coredns) and [kubeadm](https://github.com/kubernetes/kubernetes/blob/master/cmd/kubeadm/app/constants/constants.go#L335) install tools.
    * Make sure to write proper release notes mentioning the new version, features, deprecation as applicable from the previous release.
    * Ensure that all the pre-submit e2e tests are passing. 
    * If the tests are passing, get the PR merged.
    
6. If there are any manifest updates apart from the version update from the main [CoreDNS deployment](https://github.com/coredns/deployment/blob/master/kubernetes/coredns.yaml.sed), include them in the PR. 
    * Sync with the kube-dns manifests to ensure that there are no important updates missing.
    * If the manifest updates contain any user-facing changes, write clear release-notes in the PR stating why the changes are being made.


6. Validate the [5k node scale test](https://k8s-testgrid.appspot.com/sig-scalability-gce#gce-scale-performance) to see if the Load and Density scale tests are green. 
    * The 5k node scale test only runs once per day. Hence ensure that the validated test run includes the merged PR. 
    * If the tests are green, proceed to open a PR in the [Kops](https://github.com/kubernetes/kops) repository to update the CoreDNS image and manifest.



## Checklist (must be completed in order)

- [ ] Open a coredns/deployment PR to update the CoreDNS image
- [ ] coredns/deployment passes CI tests
- [ ] Open a temporary kubernetes/kubernetes PR to test the (coredns/coredns:version) image
- [ ] kubernetes/kubernetes passes e2e dns pre-submit conformance tests
- [ ] Close the temporary kubernetes/kubernetes PR
- [ ] Merge the coredns/deployment PR
- [ ] Open a kubernetes/kubernetes issue to push CoreDNS image to k8s.gcr.io
- [ ] Open a kubernetes/kubernetes PR to update the (k8s.gcr.io/coredns:version) image
- [ ] kubernetes/kubernetes image update PR merged
- [ ] Verify that gce-scale-performance test passes (runs once daily)
- [ ] Open a kubernetes/kubernetes PR to update the manifests
- [ ] Open a PR to update the image and manifest for Kops
