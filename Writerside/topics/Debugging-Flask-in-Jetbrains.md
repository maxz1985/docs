# Debugging Flask in Jetbrains

First of all, use `Flask`'s built-in logging facility—`app.logger`
```Python
app.logger.info('Testing INFO logging line')
app.logger.error('Testing ERROR logging line')
app.logger.error(f"Response data: {json.dumps(employee)}")
```
## Caveats
When using `Pycharm`, the debugging output is printed to `Run` | `Debug` output console depending on 
whether you've selected run or debug from the IDE.

![debugging-flask-in-jetbrains-01.png](debugging-flask-in-jetbrains-01.png) {thumbnail="true"}

The `app.logger.error` lines will *always* print.

The `app.logger.info` lines will only print if the app is configured to start with the optional `debug=True` parameter.
```Python
app.run(debug=True)
```
> NOTE You cannot use Jetbrains' IDE built-in debugger when your app is launched with `debug=True`
> 
{style="note"}