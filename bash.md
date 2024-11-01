# Bash

## Quoting

- Single quotes are literal
- Single quote doesn't expand: `echo '$USER'` gives $USER
- Double quotes dereference variables - e.g. `echo "$USER"` returns value of $USER

## Looping

- Loop over files:
`for f in <something>; do <script.sh> $f; done`

	- Loop over lines in `while read LINE ; do command $LINE; done`
- Loop over range:

```sh
for i in `seq 4  12`; do curl https://somesite/$i.zip -o download$i.zip; done
```

- Repeat until success:

```sh
until passwd ; sleep 5
do
  echo "Try again"
done
```

## Redirects

- `2>` redirect stderr
- `&>` redirect stdout and stderr
- Add a `>` to append to file, e.g.: `&>>`

You can arrange to run any command and pass it input from a file in the following way:
`$ some-command < /path/to/some/file`

Show content of file without paging:
`cat /path/to/filename`

- Run commands in sequence: use semi-colon (;) between commands
- Run `command2` only if `command1` is successful: `command1 && command2`
- Run `command2` only if `command1` fails: `command1 || command2`
- Run `command1` in the background whilst `command2` runs: `command1 & command2`

## Navigating

CTRL-R reverse search
CTRL-N go forwards in history
CTRL-P go backwards in history

Goto previous directory:
`cd -`

## Shortcuts

- `!!` Previous command
- `!thing` Most recent command starting with thing
- `!$` Last word of previous command
- `!N` Execute Nth command in history
- `$(!!)` - Use output of last command, e.g. echo $(!!)
- `:h` gets folder, e.g:
- `$?` return code of last command - 0=OK=true, 1-255=error=false

```sh
grep isthere /long/path/to/some/file/or/other.txt
cd !$:h
```

- `!:1-$` All the arguments from the previous command, eg.:

```sh
grep isthere /long/path/to/some/file/or/other.txt
egrep !:1-$
```

## `<()`

Use filenames as argument, e.g.
`diff <(grep somestring file1) <(grep somestring file2)`

## Scripting

- `` is the same as `$()`, but latter is safer
- `set -e` exits if any command is non-zero
- `0` = OK
- Not Zero = error

## Test = square bracket in conditionals

- Single brackets more compliant (POSIX)
- Double brackets are safer - see [https://mywiki.wooledge.org/BashFAQ/031]

(Add :p to just print)

## Bash Parameter Expansion

There are also expansions for removing prefixes and suffixes. The form `${VAR#pattern}` removes any prefix from the expanded value that matches the pattern. The removed prefix is the shortest matching prefix, if you use double pound-signs/hash-marks the longest matching prefix is removed. Similarily, the form ${VAR%pattern} removes a matching suffix (single percent for the shortest suffix, double for the longest). For example:

```sh
  file=data.txt
  echo ${file%.*}
  echo ${file#*.}
```

outputs the file base and extension respectively ("data" and "txt")

## Bash tips

From https://sharats.me/posts/shell-script-best-practices/

- Use `shellcheck`
- `set -o nounset` to make the script fail when accessing an unset variable
  - When you want to access a variable that may or may not have been set, use `"${VARNAME-}"` instead of `"$VARNAME"`
- `set -o pipefail` to ensure that a pipeline command is treated as failed, even if one command in the pipeline fails
- `set -o xtrace` with a check on $TRACE env variable.
  - Add `if [[ "${TRACE-0}" == "1" ]]; then set -o xtrace; fi`.
  - Debug scripts with `TRACE=1 ./script.sh` instead of `./script.sh`.
- Use `[[ ]]` for conditions in `if` / `while` statements, instead of `[ ]` or test.
- Always quote variable accesses with double-quotes.
- Use local variables in functions.
- Print error messages to `stderr`:`. `echo 'Something unexpected happened' >&2`
- Change to the script’s directory close to the start of the script.
  - Use `cd "$(dirname "$0")"`
  
Template:

```sh
#!/usr/bin/env bash

set -o errexit
set -o nounset
set -o pipefail
if [[ "${TRACE-0}" == "1" ]]; then
    set -o xtrace
fi

if [[ "${1-}" =~ ^-*h(elp)?$ ]]; then
    echo 'Usage: ./script.sh arg-one arg-two

This is an awesome bash script to make your life better.

'
    exit
fi

cd "$(dirname "$0")"

main() {
    echo do awesome stuff
}

main "$@"
```