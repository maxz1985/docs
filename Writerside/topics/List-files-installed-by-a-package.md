# List files installed by a package

```bash
dpkg-query -L <package_name>
```
Example:
```Bash
dpkg-query -L fzf
```
```Bash
/.
/usr
/usr/bin
/usr/bin/fzf
/usr/bin/fzf-tmux
/usr/share
/usr/share/bash-completion
/usr/share/bash-completion/completions
/usr/share/bash-completion/completions/fzf
/usr/share/doc
/usr/share/doc/fzf
/usr/share/doc/fzf/README.Debian
/usr/share/doc/fzf/changelog.Debian.amd64.gz
/usr/share/doc/fzf/changelog.Debian.gz
/usr/share/doc/fzf/changelog.gz
/usr/share/doc/fzf/copyright
/usr/share/doc/fzf/examples
/usr/share/doc/fzf/examples/completion.zsh
/usr/share/doc/fzf/examples/fzf.vim
/usr/share/doc/fzf/examples/key-bindings.bash
/usr/share/doc/fzf/examples/key-bindings.fish
/usr/share/doc/fzf/examples/key-bindings.zsh
/usr/share/doc/fzf/examples/plugin
/usr/share/fish
/usr/share/fish/vendor_functions.d
/usr/share/fish/vendor_functions.d/fzf_key_bindings.fish
/usr/share/man
/usr/share/man/man1
/usr/share/man/man1/fzf-tmux.1.gz
/usr/share/man/man1/fzf.1.gz
/usr/share/doc/fzf/examples/plugin/fzf.vim
```
For a .deb file
```Bash
dpkg-deb -c <package_name.deb>
```