# mtr on macOS is not found after brew installation

After running `brew install mtr`, running `mtr` responds with `command not found`

`brew` links `mtr` to `/usr/local/sbin`. That directory may not be in your macOS `PATH`

Add `/usr/local/sbin` to `/etc/path` file, see [](Add-Paths-on-macOS.md)