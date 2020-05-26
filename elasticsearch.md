
**DEBUG elasticsearch call with tcpdump**

	sudo tcpdump -A -nn -s 0 'tcp dst port 9200 and (((ip[2:2] - ((ip[0]&0xf)<<2)) - ((tcp[12]&0xf0)>>2)) != 0)'
