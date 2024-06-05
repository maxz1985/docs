# Switching to another user&apos;s context

* make sure your `sudoers` file contains the line `${your_user} ALL=(ALL:ALL) NOPASSWD:ALL`
* switch to another user's context
```shell
sudo su - ${other_user}
```
Example
```shell
sudo su - circleci
```
```shell
circleci@linuxhost:~$
```
For other scenarios, see [How to Switch (su) to Another User Account without a Password](https://www.tecmint.com/switch-user-account-without-password/)