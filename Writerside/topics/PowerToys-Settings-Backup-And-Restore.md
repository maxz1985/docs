# PowerToys Settings Backup And Restore

(includes Keyboard Manager settings)

## Back up setting on the source
<ui-path>Settings -> General -> Backup and restore</ui-path>

![powertoys-settings-backup-and-restore-04.png](powertoys-settings-backup-and-restore-04.png){thumbnail="true"}

The backup file will be located at:
```shell
C:\users\{UserName}\Documents\PowerToys\Backup
```
Copy the `settings_xxxxx.ptb` file to the destination machine in the same path `C:\users\{UserName}\Documents\PowerToys\Backup`

## Restore settings on the target:

<ui-path>Settings -> General -> Backup and restore</ui-path>

Click `Restore`. The settings file will be picked up automatically if placed in the correct location.

PowerToys will automatically restart to reload the restored settings.