# Lambda Canary Deployment

Lambda Development LifeCycle

| AWS Lambda Concept | Software Development Equivalent | Key Characteristics                                                                                          |
|--------------------|---------------------------------|--------------------------------------------------------------------------------------------------------------|
| `$LATEST`          | Development Branch              | Mutable and always reflects the most recent updates to the function code or configuration.                   |
| Published Versions | Releases (Tags)                 | Immutable, production-ready snapshots of the function at a specific point in time.                           |
| Aliases            | Release Channels                | Pointers to specific versions (e.g., `beta`, `prod`, `stable`). They allow traffic shifting between versions |

Assume that:
* Version 1: The current production version.
* Version 2: A new version you want to test.
* the alias `myalias` is pointing to Version 1.
```Bash
# Update $LATEST version of function
aws lambda update-function-code --function-name myfunction ….
```
> * The `$LATEST` version is implicit in this command because you can ONLY write to the `$LATEST`.
> * Commands like `update-function-code` and `update-function-configuration` ALWAYS write to the `$LATEST`.
```Bash
# Publish new version of function
aws lambda publish-version --function-name myfunction
```
> * Version numbers are managed by AWS and CANNOT be specified.
> * Every time you publish a new version using `aws lambda publish-version`, AWS automatically increments the version number by 1.
> * Published versions CANNOT be deleted. To clean up version proliferation, have to recreate Lambda.
> * Unless a version is explicitly published using `publish-version`, the ONLY version that exists after `Lambda` initial creation is the `$LATEST`

## Aliases
> * Default `Lambda` invocations **without specifying the alias** like `aws lambda invoke --function-name my-function` ALWAYS go to the `$LATEST`
> * The Proper way to invoke a production `Lambda` is through the ALIAS which goes into `--qualifier` parameter
> ```Bash
> aws lambda invoke --function-name my-function --qualifier stable
> ```
> * Clients ALWAYS invoke a `Lambda` with ALIAS. `$LATEST` is used in development.
>
{style="warning"}

> * Aliases point to one or more (for traffic shifting) `Lambda` versions.
> * Aliases, for example, like `stable` or `prod` are shared with clients to use with invocations.
> * Developers make sure that the **static alias** (like `stable`) shared with clients is always **pointed to the `Lambda` version that developers want the clients to use**.

```Bash
# Update `myalias` to point to both versions 1 and 2, with version 2 weighted at 5% (version 1 stays at 95% of traffic)
aws lambda update-alias --function-name myfunction --name myalias --routing-config '{"AdditionalVersionWeights" : {"2" : 0.05} }'
```

```Bash
# Verify that the new version is healthy
…
# Set the primary version on the alias to the new version 2 and reset the additional versions (100% weighted)
aws lambda update-alias --function-name myfunction --name myalias --function-version 2 --routing-config '{}'
```
