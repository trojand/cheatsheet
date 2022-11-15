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

## Print Full HTTP Request and Response
Includes Headers and Body in a well formatted way [^2]
* Request
    * "_Use `httputil.DumpRequest()` if you want to pretty-print the request on the server side._"
    * "_Use `httputil.DumpRequestOut()` if you want to dump the request on the client side._"
```
    reqDump, err := httputil.DumpRequestOut(req, true)
    if err != nil {
        log.Fatal(err)
    }

    fmt.Printf("REQUEST:\n%s", string(reqDump))

```

* Response
```
    respDump, err := httputil.DumpResponse(resp, true)
    if err != nil {
        log.Fatal(err)
    }

    fmt.Printf("RESPONSE:\n%s", string(respDump))

```

[^1]: [Siongui](https://siongui.github.io/2018/03/06/go-print-http-response-header/)
[^2]: [Go Samples](https://gosamples.dev/print-http-request-response/)
