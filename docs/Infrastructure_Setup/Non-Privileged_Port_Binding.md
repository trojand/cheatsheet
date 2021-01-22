# Bind to ports 1-1024 without the needing root privileges

Used for running applications that binds to ports 1-1024 (i.e. HTTP & HTTPS ports) as a non-privileged user

Make sure to change

* /path/to/program

```bash
sudo apt-get install libcap2-bin 
sudo setcap 'cap_net_bind_service=+ep' /path/to/program
```

* If you encounter an error such as the one below, then you can try [authbind](https://www.gremwell.com/node/387)

```bash
The value of the capability argument is not permitted for a file. Or the file is not a regular (non-symlink) file
```

