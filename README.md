# pipe

`pipe` is a package for chaining [exec.Cmd](https://golang.org/pkg/os/exec/#Cmd)s and having the output of each command used as the input for the next.

It has fairy limited uses and isn't as fleshed out as [exec](https://golang.org/pkg/os/exec/) is, but I use it every once in a while for chaining several different commands on the same text file. Eg running `gofmt` and `goimports` on code that I have generated.

This code was initially released as part of a blog post covering how code generation can be used to make more maintainable code in a world without generics. You can find that post here - <https://www.calhoun.io/using-code-generation-to-survive-without-generics-in-go/>

## Example usage

```go
t := template.Must(template.New("queue").Parse(`
package blah

// Bad formatting intentional
func main() {
      fmt.Println("hello world!");
      }
`))
p, err := pipe.New(
	exec.Command("gofmt"),
	exec.Command("goimports"),
)
if err != nil {
	panic(err)
}
r, w := io.Pipe()
p.Stdin = r
p.Stdout = os.Stdout
if err := p.Start(); err != nil {
	panic(err)
}
// You should probably error check these too
t.Execute(w, nil)
w.Close()
if err := p.Wait(); err != nil {
	panic(err)
}
```

or you can use the `Commands()` function

```go
t := template.Must(template.New("queue").Parse(`
package blah

// Bad formatting intentional
func main() {
      fmt.Println("hello world!");
      }
`))
rc, wc, errCh := pipe.Commands(
	exec.Command("gofmt"),
	exec.Command("goimports"),
)
go func() {
	select {
	case err, ok := <-errCh:
		if ok && err != nil {
			panic(err)
		}
	}
}()
t.Execute(wc, d)
wc.Close()
io.Copy(os.Stdout, rc)
```
