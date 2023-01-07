本文更新于2022-04-05。

1. 创建.vbs脚本（文件名自定，笔者使用say.vbs），文件内容如下：
	```vbs
	set spvoice = CreateObject("SAPI.SpVoice")
	set spfilestream = CreateObject("SAPI.SpFileStream")
	set args = Wscript.Arguments
	wavfile = Replace(args(0), "/", "\")
	text = args(1)
	Wscript.echo wavfile
	spfilestream.open wavfile, 3
	set spvoice.AudioOutputStream = spfilestream
	spvoice.Speak text
	spfilestream.close
	```
1. 在cmd中运行命令：
	```bat
	cscript VBSFILENAME VOICEFILENAME TEXT
	```
	* VBSFILENAME：上述.vbs脚本文件名（笔者使用say.vbs）。
	* VOICEFILENAME：声音文件名，文件格式为.wav，最好使用.wav的扩展名。
	* TEXT：生成声音的文本内容。
