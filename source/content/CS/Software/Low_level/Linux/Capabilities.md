> 	"Think of capabilities as a fine-grained breakdown of the permissions
	classically attributed to the all-powerful root user on Linux. A user
	with the capability CAP_NET_ADMIN would be allowed to perform root
	operations on the network stack, such as changing the IP address,
	binding to lower ports, and entering promiscuous mode to sniff
	traffic. The user would, however, be prevented from mounting
	filesystems, for instance. That action requires the CAP_SYS_ADMIN
	capability."

Para ver capabilities
```zsh
cat /dir/dir/file | grep Cap
```