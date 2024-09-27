# 1Password on Pixel Unable to Fill Passkeys

## Symptoms
Even though `1Password` is installed and configured correctly on Pixel, it is unable to fill *passkeys*

## Resolution

### Make sure that `1Password` is the default password manager
Navigate to
<ui-path>Settings->Passwords & Accounts</ui-path>

Make sure `1Password` is selected under `Passwords, passkeys, and data service`

Make sure `Google` is turned off

![Screenshot_20240321-164408.png](1password-on-pixel-unable-to-fill-passkeys-01.png) {thumbnail="true"}

### Configure Chrome to allow third Party applications to fill passkeys

Open a new tab in the phone's Chrome app and navigate to `chrome://flags`

Search for `Android Credential Management for passkeys` setting.

Set the value to `Enabled for 3rd party passkeys`

![Screenshot_20240321-163607.png](1password-on-pixel-unable-to-fill-passkeys-02.png) {thumbnail="true"}