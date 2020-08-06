本文更新于2019-06-29，使用MySQL 5.7，操作系统为Deepin 15.4。

应用可使用PrepareStatement + Bind-Variable来防止SQL注入。

已知的非法符号有：

* (
* )
* <
* \>
* [
* ]
* /*
* */
* --
* \#
* ;
* =
* '
* \+
* 空格
* %
