# Mount GCP Bucket as a File System
Create a variable pointing to the `fuse` repo for your distribution.
```bash
export GCSFUSE_REPO=gcsfuse-`lsb_release -c -s`
```
Add `fuse` repo.
```bash
echo "deb https://packages.cloud.google.com/apt $GCSFUSE_REPO main" | \
sudo tee /etc/apt/sources.list.d/gcsfuse.list
```
Add repo `gpg` key.
```Bash
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```
Install `gcsfuse` package
```Bash
sudo apt-get update && sudo apt-get install fuse gcsfuse -y
```
Verify `gcsfuse`
```Bash
gcsfuse -v
```
```Bash
gcsfuse version 2.11.1 (Go version go1.24.0)
```
Authenticate to `GCP` for applications
```Bash
gcloud auth application-default login --no-launch-browser
```
List objects in a bucket
```Bash
gcloud storage ls gs://dev-terraform-state/labs/terraform.tfstate
```
## Mount a bucket and use it like a local filesystem
Create a mount directory
```Bash
md $HOME/gcp-mount
```
Mount the bucket
> use `--only-dir` to mount a specific _directory_ within a _bucket_, otherwise you will not see objects as files
> 
{style="note"}
```Bash
gcsfuse --only-dir lab/terraform.tfstate/ dev-terraform-state "/$HOME/gcp-mount"
```
{type = medium}
`--only-dir `
: mount a **directory** instead of the entire bucket

`lab/terraform.tfstate/`
: directory path from the root of the bucket

`dev-terraform-state`
: bucket name

`"/$HOME/gcp-mount"`
: linux path to local mount directory 


Unmount after done
```Bash
fusermount -u "$HOME/gcp-mount"
```