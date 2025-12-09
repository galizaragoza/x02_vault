[Repo](https://github.com/Ne0nd0g/merlin/)
# Setup
```go
add-apt-repository ppa:longsleep/golang-backports
apt update && sudo apt install golang-go
go version

//go version go1.13 linux/amd64

git clone https://github.com/Ne0nd0g/merlin && cd merlin
```

> 	If we compile a standard agent out of the box, it will be
	immediately busted by any regular antivirus agent doing simple
	string lookups for general conspicuous terms, so we need to make
	some adjustments. We’ll rename suspicious functions like ExecuteShell
	and remove references to the original package name.