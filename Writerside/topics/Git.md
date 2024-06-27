# Git

## Remove a file from the latest commit

<tldr><code-block lang="shell"> git reset --soft HEAD^ </code-block><p>Rollback latest commit</p></tldr>
Say you have included a file in the commit that should not be there.
You didn't push yet, you want to remove the file from the commit.

What you're looking for is to move all files in the commit back to staging, without cancelling any changes to them.

Effectively, you want to roll back the latest commit.

Afterward, you can either unstage the unwanted file, add it to `.gitignore`, or restore to its original pre-commit content using 
`git restore --staged {unwanted file}`