# List File Octal Permissions

<tabs>
    <tab title="MacOS">
        <code-block lang="shell">stat -f '%A %N' *"</code-block>
    </tab>
    <tab title="Linux">
        <code-block lang="shell">stat -c '%a %n' *</code-block>
    </tab>
</tabs>