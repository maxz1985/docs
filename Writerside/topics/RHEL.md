# RHEL

## install notes
### EPEL
enable CRB
```bash
sudo subscription-manager repos --enable codeready-builder-for-rhel-9-$(arch)-rpms
```
install epel 
```Bash
sudo dnf install \
https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
```