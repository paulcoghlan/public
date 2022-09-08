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
