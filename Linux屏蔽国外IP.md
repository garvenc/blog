本文更新于2021-07-05，操作系统为Debian 8.9。

**致谢：[https://hostloc.com/thread-484625-1-1.html](https://hostloc.com/thread-484625-1-1.html)**

1. 安装ipset。

	```shell
	sudo apt-get install ipset
	```
1. 将以下脚本保存至文件allcn.sh。

	```shell
	mmode=$1
	
	#下面语句可以单独执行，不需要每次执行都获取网段表
	#wget -q --timeout=60 -O- 'http://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest' | awk -F\| '/CN\|ipv4/ { printf("%s/%d\n", $4, 32-log($5)/log(2)) }' > china_ssr.txt
	
	CNIP="china_ssr.txt"
	
	gen_iplist() {
		cat <<-EOF
		$(cat ${CNIP:=/dev/null} 2>/dev/null)
		EOF
	}
	
	flush_r() {
		iptables -F ALLCNRULE 2>/dev/null
		iptables -D INPUT -p tcp -j ALLCNRULE 2>/dev/null
		iptables -X ALLCNRULE 2>/dev/null
		ipset -X allcn 2>/dev/null
	}
	
	mstart() {
		ipset create allcn hash:net 2>/dev/null
		ipset -! -R <<-EOF 
		$(gen_iplist | sed -e "s/^/add allcn /")
		EOF
		
		iptables -N ALLCNRULE 
		iptables -I INPUT -p tcp -j ALLCNRULE 
		iptables -A ALLCNRULE -s 127.0.0.0/8 -j RETURN
		iptables -A ALLCNRULE -s 169.254.0.0/16 -j RETURN
		iptables -A ALLCNRULE -s 224.0.0.0/4 -j RETURN
		iptables -A ALLCNRULE -s 255.255.255.255 -j RETURN
		#可在此增加你的公网网段，避免调试ipset时出现自己无法访问的情况
		iptables -A ALLCNRULE -m set --match-set allcn src -j RETURN
		iptables -A ALLCNRULE -p tcp -j DROP
	}
	
	if [ "$mmode" == "stop" ] ;then
		flush_r
		exit 0
	fi
	 
	flush_r
	sleep 1
	mstart
	```
1. 下载国内网段表。如上述脚本未注释以下语句，此步骤可不执行。

	```shell
	wget -q --timeout=60 -O- 'http://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest' | awk -F\| '/CN\|ipv4/ { printf("%s/%d\n", $4, 32-log($5)/log(2)) }' > china_ssr.txt
	```
1. 屏蔽国外IP。

	```shell
	sudo bash allcn.sh
	```
1. 解除屏蔽国外IP。

	```shell
	sudo bash allcn.sh stop
	```
1. 查看iptables，检查屏蔽或解除屏蔽的结果。

	```shell
	iptables -L
	```
