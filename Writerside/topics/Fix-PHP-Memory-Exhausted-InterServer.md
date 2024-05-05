# Fix PHP Memory Exhausted InterServer

WordPress on InterServer.

A site responds with `HTTP 500 Internal Server Error`

Log into cPanel and navigate to
<ui-path>Tools → Metrics → Errors</ui-path>

This error is in the log
```Shell
PHP Fatal error:  Allowed memory size of 41943040 bytes exhausted (tried to allocate 20480 bytes) in /wp-includes/plugin.php on line 177
```

The `41943040 bytes` (`40MB`) is referring to a default value of the `memory_limit` configuration parameter in the `php.ini`

In cPanel navigate to 
<ui-path>Tools → Software → MultiPHP INI Editor</ui-path>

Select the WP Site that exhibits the problem and adjust `memory_limit` to `256MB`



