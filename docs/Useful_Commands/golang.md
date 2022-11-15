 # Go 

## Print all HTTP Response Headers

Including Status code [^1]

```
fmt.Printf("RESPONSE:\n")
fmt.Println(resp.Status)
for k, v := range resp.Header {
  fmt.Print(k)
  fmt.Print(" : ")
  fmt.Println(v)
}
```

[^1]: [Siongui](https://siongui.github.io/2018/03/06/go-print-http-response-header/)
