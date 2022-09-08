# make

From: [https://danishpraka.sh/2019/12/07/using-makefiles-for-go.html]

`make` executes the rule if one of the prerequisites or the target file has been changed

Some targets we always want to execute - even if the local file or directory hasn't changed.
You can specify the target in question to be “phony” by specifying it as a prerequisite to the special target `.PHONY:`

An example is `clean` or `test`.

e.g.

```Makefile
APP=stringifier

.PHONY: build
build: clean
   go build -o ${APP} main.go

.PHONY: run
run:
   go run -race main.go

.PHONY: clean
clean:
   go clean
```
