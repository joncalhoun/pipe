# pipe

`pipe` is a package for chaining [exec.Cmd](https://golang.org/pkg/os/exec/#Cmd)s and having the output of each command used as the input for the next.

It has fairy limited uses and isn't as fleshed out as [exec](https://golang.org/pkg/os/exec/) is, but I use it every once in a while for things like running `gofmt` and `goimports` on code that I have generated.
