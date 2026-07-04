# file-stage

`file-stage` provides `fst`, a small command for staging file operations in one
directory and applying them later in another directory.

## Installation

Install the latest `.deb` package from GitHub Releases:

```sh
wget https://github.com/KIMGEONUNG/file-stage/releases/download/v0.1.1/file-stage_0.1.1-1_all.deb
sudo apt install ./file-stage_0.1.1-1_all.deb
```

## Examples

Stage copy operations:

```sh
fst cp README.md src/main.sh
cd /tmp/target
fst show
fst do cp
```

Stage moves:

```sh
fst mv old-a.txt old-b.txt
cd ~/archive
fst do mv
```

Stage symlinks:

```sh
fst ln ~/data/model.bin
cd ./workdir
fst do ln
```

Flatten source paths into destination names:

```sh
fst cp --flatten src/a.txt src/nested/b.txt
cd /tmp/target
fst do cp
# creates ./src-a.txt and ./src-nested-b.txt
```

Handle many files safely with NUL-delimited input:

```sh
find . -type f -print0 | fst cp --stdin0
cd /tmp/target
fst do cp
```

Run staged operations by type:

```sh
fst do cp
fst do mv
fst do ln
```

Run every staged operation, regardless of type:

```sh
fst run
```

## State

Staged operations are stored under:

```text
${FILE_STAGE_DIR:-${XDG_STATE_HOME:-$HOME/.local/state}/file-stage}
```

Set `FILE_STAGE_DIR` to keep separate queues.

State access is guarded with `flock`. `fst run` atomically moves the current
queue to a run snapshot before executing it. `fst do cp`, `fst do mv`, and
`fst do ln` atomically move only the selected operation type to a run snapshot.
Operations staged during a run remain queued for the next run.

## Commands

```text
fst cp [--flatten] [--stdin|--stdin0] FILE...
fst mv [--flatten] [--stdin|--stdin0] FILE...
fst ln [--flatten] [--stdin|--stdin0] FILE...
fst do cp
fst do mv
fst do ln
fst run
fst show
fst clear
```
