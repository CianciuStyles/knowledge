# Lsof

```
# list all IPv4 network files
sudo lsof -i4

# list all IPv6 network files
sudo lsof -i6

# list all open sockets
lsof -i

# find which program is using port 80
lsof -i TCP:80

# list all connections to a specific host
lsof -i@192.168.1.5

# list all processes accesing a particular file/directory
lsof <path>

# list all files/network connections a command is using
lsof -c <command>

# list all files a process has open
lsof -p <pid>

# list all files used by given user
lsof -u <username>
```
