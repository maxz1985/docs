# CircleCI Self-Hosted Runner Docker Issue

<tldr><p>Add <code>circleci</code> user to the <code>docker</code> group</p></tldr>

When pushing a Docker container build to a self-hosted machine runner (Ubuntu 24.04), build fails with the following error:
```shell
#!/bin/bash -eo pipefail
docker build -t $(<namespace>/<resource-class>):$CIRCLE_BRANCH .
---
ERROR: permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Head "http://%2Fvar%2Frun%2Fdocker.sock/_ping": dial unix /var/run/docker.sock: connect: permission denied
Exited with code exit status 1
---
CircleCI received exit code 1
```
On a Linux system, only users that belong to the `docker` group can run docker commands without `sudo`.

Find user under which the `circleci-runner` service is running.
```shell
systemctl show -pUser,UID circleci-runner
```
```shell
UID=1001
User=circleci
```
Add `circleci` user to the `docker` group.
```shell
sudo usermod -aG docker circleci
```
Restart `circleci-runner` service for the change to take effect.
```shell
sudo systemctl restart circleci-runner
```