# git-split

`git split` is a simple command that allows you to breakup the last commit into multiple pieces, with the goal of keeping the final contents of the repository exactly the same as they were when the command was first invoked. You might want to use this if you know that you have a correct commit, but it's too big, and you want to split it or rework it into several parts without manually checking whether you can reach your starting point back or not.

WARNING: The command is currently not robust to abuse (like starting a split within a split, and so on), and perhaps to strange situations. For now it's just a simple command, which will hopefully improve with time. 

## Usage ##

`git split` has three main options: `start`, `abort` and `end`, and a `diff` helper option.

`git split start [--hard]` performs a `git reset [--hard] HEAD~1`, undoing the contents of your last commits. The last commit hash is saved internally so it can be referenced later, and is not lost. From here on out, you can start work on your changes. If a git split is in progress, running `git split` will tell you which hash was your starting point.

`git split abort` will abort a running `git split` and `git reset --hard` to the original hash.

`git split end` will terminate the split. It will compute a `git diff` between the current state of the repo and the split starting point, and apply it as a patch. It will open the commit message editor defaulting the commit message to the message of the starting point commit.

`git split diff` will compare the current state of the repo with that of the target, and report a diff stating the changes that yet need to be done to be in parity (essentially the ones that would be performed by `git split end` were it to be called).

## Example ##

Consider the last commit with a new file `text.txt` with contents:
```
First line
Second line
Third line
```
We believe it's too many lines in a single commit, and so we want to split it up. We perform `git split start`. The state of the repository is `reset` softly, so that `text.txt` still exists but has not yet been committed. We remove `Second line` from `text.txt`, and commit the rest, so that `text.txt` is:
```
First line
Third line
```
We can now perform `git split end`. It will automatically create a commit adding `Second line` to `text.txt`, and let us select the commit message. Once again, `text.txt` has the same contents it had before starting the `git split`:
```
First line
Second line
Third line
```
