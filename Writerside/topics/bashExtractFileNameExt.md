# Extract filename and extension in bash

From StackOverflow examples

```bash

~% FILE="example.tar.gz"

~% echo "${FILE%%.*}"
example

~% echo "${FILE%.*}"
example.tar

~% echo "${FILE#*.}"
tar.gz

~% echo "${FILE##*.}"
gz
```