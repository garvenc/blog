本文更新于2024-08-13。操作系统为Debian 12.6 (bookworm)。

以下假设新磁盘为/dev/sdb，要创建一个分区/dev/sdb1，文件系统类型为xfs。（请根据实际情况，自行选择。）

# 使用parted进行分区

1. 若未安装parted和xfsprogs，先安装之。

	```shell
	apt update
	apt install parted
	apt install xfsprogs
	```
1. 使用`fdisk -l`查看磁盘/dev/sdb（请使用实际的设备）是否存在。
1. 使用`parted /dev/sdb`（请使用实际的设备）进行分区，需依次使用如下子命令：

	1. `mklabel gpt`，将设备转换成GPT分区格式。
	1. `mkpart primary 1 100%`，使用全部空间创建一个主分区（请根据实际创建）。通常于前面留出1M的空余空间。
	1. `align-check optimal 1`，检查分区是否对齐（请使用实际的分区号，如有多个分区，均需检查），若打印“1 aligned”表示已对齐。
	1. `print`，查看分区表。
	1. `quit`，退出。
1. 使用`partprobe`，令系统重新读取分区表。此时使用`fdisk -l`应能查看到分区/dev/sdb1（会显示实际的分区名）。
1. 使用`mkfs -t xfs /dev/sdb1`（请使用实际的文件系统类型和分区名）为分区格式化文件系统。如分区已被格式化文件系统，则使用`mkfs -t xfs -f /dev/sdb1`强行重新格式化。
1. 使用`mkdir /data`（请使用实际的目录）创建挂载点目录。可使用已有目录，挂载后目录中的内容会被隐藏。
1. 使用`mount /dev/sdb1 /data`（请使用实际的分区和目录）将分区挂载至目录。
1. 使用`df -Th`应能查看到该分区挂载的目录的磁盘使用情况。
1. 备份/etc/fstab。
1. 使用`echo /dev/sdb1 /data xfs defaults 0 0 >> /etc/fstab`（请使用实际的分区名、目录名、文件系统类型和选项，也可使用`$(blkid /dev/sdb1 | awk '{print $2}' | sed 's/\"//g')`以UUID替换`/dev/sdb1`）将分区信息写入配置文件，令开机自动挂载分区。完成后查看/etc/fstab的内容是否正确。
1. 使用`mount -a`重新挂载所有分区，再次确定/etc/fstab的内容是否正确。（如/etc/fstab有其它不希望挂载的分区，则不能执行此命令）
