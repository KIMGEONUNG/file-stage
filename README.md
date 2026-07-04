# file-stage

`file-stage` provides `fst`, a small command for staging file operations in one
directory and applying them later in another directory.

## Examples

Stage copy operations:

```sh
fst cp README.md src/main.sh
cd /tmp/target
fst show
fst run
```

Stage moves:

```sh
fst mv old-a.txt old-b.txt
cd ~/archive
fst run
```

Stage symlinks:

```sh
fst ln ~/data/model.bin
cd ./workdir
fst run
```

Flatten source paths into destination names:

```sh
fst cp --flatten src/a.txt src/nested/b.txt
cd /tmp/target
fst run
# creates ./src-a.txt and ./src-nested-b.txt
```

Handle many files safely with NUL-delimited input:

```sh
find . -type f -print0 | fst cp --stdin0
cd /tmp/target
fst run
```

Run the underlying operation immediately:

```sh
fst do cp -a src/ dst/
fst do mv old new
fst do ln target link-name
```

## State

Staged operations are stored under:

```text
${FILE_STAGE_DIR:-${XDG_STATE_HOME:-$HOME/.local/state}/file-stage}
```

Set `FILE_STAGE_DIR` to keep separate queues.

State access is guarded with `flock`. `fst run` atomically moves the current
queue to a run snapshot before executing it, so operations staged during a run
remain queued for the next run.

## Commands

```text
fst cp [--flatten] [--stdin|--stdin0] FILE...
fst mv [--flatten] [--stdin|--stdin0] FILE...
fst ln [--flatten] [--stdin|--stdin0] FILE...
fst do cp ARGS...
fst do mv ARGS...
fst do ln ARGS...
fst run
fst show
fst clear
```
