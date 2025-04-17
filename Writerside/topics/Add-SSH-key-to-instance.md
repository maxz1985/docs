# Add an SSH key to an instance
Follow this to add a public SSH key to an existing GCP instance.

Updating the ` ssh-keys ` metadata field for an existing instance will **overwrite** the content of the field. 

To add a key to the VM, create a file combining all _existing_ keys with the _new_ key and write that to the metadata.

* Place all public keys in the same directory
* If you don't have the public keys already attached to the VM, run
```Bash
gcloud compute instances describe {INSTANCE_NAME} \
  --zone={ZONE} \
  --format="get(metadata.items.ssh-keys)" > combined_keys.txt
```
* If it doesn't exist, create a `combined_keys.txt` file with ALL public keys allowed to log in
```bash
echo "user1:$(< key1.pub)" > combined_keys.txt
```
where `user1` is the Linux username and should match the username at the end of the public key.

* Add additional public keys to an existing `combined_keys.txt`
```bash
echo "user2:$(< key2.pub)" >> combined_keys.txt
```
* Verify that the resulting `combined_keys.txt` file is the following format:
```Bash
alice:ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBgXeYg7... alice
bob:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC5... bob
carol:ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBQk4Tr... carol
```
Once the file is ready with all the keys, apply to existing vm using `gcloud`.
```bash
gcloud compute instances add-metadata {INSTANCE_NAME} \
 --zone="us-east4-a" \
 --metadata=ssh-keys="$( < combined_keys.txt)"
```