# Sync directories via rsync/ssh

## Prerequisites
* `rsync` installed on `source` and `destination`
* `source` can access `destination` via `ssh`

## Command
```bash
rsync -avz -e ssh /{full_path_to_directory_on_source}/ {ssh_username}@{destination_host_name}:/{full_path_to_directory_on_destination}/
```
## Example
```Bash
rsync -avz -e ssh /home/john/repos/terraform/ john@123.10.10.11:/home/john/repos/terraform/
```