# Linux Snippets

## List Files OCTAL permissions
<tabs>
    <tab title="MacOS">
        <code-block lang="shell">stat -f '%A %N' *"</code-block>
    </tab>
    <tab title="Linux">
        <code-block lang="shell">stat -c '%a %n' *</code-block>
    </tab>
</tabs>

## TMUX quick reference

| Action            | Command        |
|-------------------|----------------|
| List sessions     | `sudo tmux ls` |
| Attach to session | `sudo tmux a`  |
| Detach session    | `CTRL-B; D`    |
