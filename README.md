# pipe

`pipe` is a package for chaining [exec.Cmd](https://golang.org/pkg/os/exec/#Cmd)s and having the output of each command used as the input for the next.

It has fairy limited uses and isn't as fleshed out as [exec](https://golang.org/pkg/os/exec/) is, but I use it every once in a while for chaining several different commands on the same text file. Eg running `gofmt` and `goimports` on code that I have generated.

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
p.Start()
t.Execute(w, nil)
w.Close()
p.Wait()
```
