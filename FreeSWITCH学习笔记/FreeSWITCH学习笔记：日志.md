本文更新于2022-12-21，使用FreeSWITCH 1.10.7。

[TOC]

常见日志示例如下。

# 拨号计划

```
a5fe78c0-84cd-11ec-9fdf-b7e8785a7986 2022-02-03 16:45:33.074287 96.50% [INFO] mod_dialplan_xml.c:639 Processing 1000 <1000>->1234 in context default
a5fe78c0-84cd-11ec-9fdf-b7e8785a7986 Dialplan: sofia/internal/1000@101.102.103.104 parsing [default->My Echo Test] continue=false
a5fe78c0-84cd-11ec-9fdf-b7e8785a7986 Dialplan: sofia/internal/1000@101.102.103.104 Regex (PASS) [My Echo Test] destination_number(1234) =~ /^echo|1234$/ break=on-false
a5fe78c0-84cd-11ec-9fdf-b7e8785a7986 Dialplan: sofia/internal/1000@101.102.103.104 Action answer()
a5fe78c0-84cd-11ec-9fdf-b7e8785a7986 Dialplan: sofia/internal/1000@101.102.103.104 Action echo()
a5fe78c0-84cd-11ec-9fdf-b7e8785a7986 2022-02-03 16:45:33.074287 96.50% [DEBUG] switch_core_state_machine.c:281 (sofia/internal/1000@101.102.103.104) State Change CS_ROUTING -> CS_EXECUTE
```

1. 第1行，Processing说明是在处理Dialplan。
1. 第2行，呼叫进入parsing（解析XML）阶段。
1. 第3行，测试被叫号码（1234）是否与配置文件中的正则表达式相匹配。
1. 第4行，执行配置文件中的动作answer。
1. 第5行，执行配置文件中的动作echo。
1. 第6行，电话从路由阶段（ROUTING）进入执行阶段（EXECUTE）。

# 媒体协商

```
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] sofia.c:7499 Channel sofia/internal/1000@113.73.147.164:13933 entering state [ready][200]
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5650 Audio Codec Compare [opus:102:48000:20:0:1]/[opus:116:48000:20:0:1]
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5705 Audio Codec Compare [opus:116:48000:20:0:1] ++++ is saved as a match
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5650 Audio Codec Compare [opus:102:48000:20:0:1]/[G722:9:8000:20:64000:1]
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5650 Audio Codec Compare [opus:102:48000:20:0:1]/[PCMU:0:8000:20:64000:1]
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5650 Audio Codec Compare [opus:102:48000:20:0:1]/[PCMA:8:8000:20:64000:1]
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5650 Audio Codec Compare [PCMU:0:8000:20:64000:1]/[opus:116:48000:20:0:1]
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5650 Audio Codec Compare [PCMU:0:8000:20:64000:1]/[G722:9:8000:20:64000:1]
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5650 Audio Codec Compare [PCMU:0:8000:20:64000:1]/[PCMU:0:8000:20:64000:1]
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5705 Audio Codec Compare [PCMU:0:8000:20:64000:1] ++++ is saved as a match
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5650 Audio Codec Compare [PCMU:0:8000:20:64000:1]/[PCMA:8:8000:20:64000:1]
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5650 Audio Codec Compare [PCMA:8:8000:20:64000:1]/[opus:116:48000:20:0:1]
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5650 Audio Codec Compare [PCMA:8:8000:20:64000:1]/[G722:9:8000:20:64000:1]
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5650 Audio Codec Compare [PCMA:8:8000:20:64000:1]/[PCMU:0:8000:20:64000:1]
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5650 Audio Codec Compare [PCMA:8:8000:20:64000:1]/[PCMA:8:8000:20:64000:1]
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5705 Audio Codec Compare [PCMA:8:8000:20:64000:1] ++++ is saved as a match
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5566 Set telephone-event payload to 105@48000
2022-02-26 11:30:39.166395 96.57% [DEBUG] mod_opus.c:619 Opus encoder: set bitrate to local settings [72000bps]
2022-02-26 11:30:39.166395 96.57% [DEBUG] mod_opus.c:619 Opus encoder: set bitrate to local settings [72000bps]
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:3870 Set Codec sofia/internal/1000@113.73.147.164:13933 opus/48000 20 ms 960 samples 0 bits 1 channels
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_codec.c:111 sofia/internal/1000@113.73.147.164:13933 Original read codec set to opus:116
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5915 Set telephone-event payload to 105@48000
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:5973 sofia/internal/1000@113.73.147.164:13933 Set 2833 dtmf send payload to 105 recv payload to 101
75ac5392-96b4-11ec-9183-ab82c99341f7 2022-02-26 11:30:39.166395 96.57% [DEBUG] switch_core_media.c:8777 AUDIO RTP [sofia/internal/1000@113.73.147.164:13933] 172.26.185.25 port 27160 -> 192.168.3.101 port 7078 codec: 102 ms: 20
```

1. Audio Codec Compare所在的行，逐一比较客户端提供的编码与服务器支持的编码。方括号中的字段依次为编码名字、载荷类型、采样频率（Hz）、打包时间间隔（ms）。++++表示匹配成功。
1. Set Codec所在的行，表示媒体协商成功并设置该Channel的编码。

# NAT打洞

```
2022-03-12 12:07:17.720313 0.00% [ERR] switch_stun.c:900 STUN Failed! [Timeout]
2022-03-12 12:07:17.720367 0.00% [ERR] switch_xml.c:175 stun-set failed.
2022-03-12 12:07:17.966625 0.00% [INFO] switch_stun.c:897 External ip address detected using STUN: 101.102.103.104
2022-03-12 12:07:18.449279 0.00% [INFO] switch_nat.c:417 Scanning for NAT
2022-03-12 12:07:18.449422 0.00% [DEBUG] switch_nat.c:170 Checking for PMP 1/5
2022-03-12 12:07:19.449507 0.00% [DEBUG] switch_nat.c:170 Checking for PMP 2/5
2022-03-12 12:07:20.449635 0.00% [DEBUG] switch_nat.c:170 Checking for PMP 3/5
2022-03-12 12:07:21.449807 0.00% [DEBUG] switch_nat.c:170 Checking for PMP 4/5
2022-03-12 12:07:22.449949 0.00% [DEBUG] switch_nat.c:170 Checking for PMP 5/5
2022-03-12 12:07:23.450090 0.00% [ERR] switch_nat.c:199 Error checking for PMP [general error]
2022-03-12 12:07:23.450148 0.00% [DEBUG] switch_nat.c:422 Checking for UPnP
2022-03-12 12:07:35.450686 0.00% [INFO] switch_nat.c:438 No PMP or UPnP NAT devices detected!
```

以上表示NAT打洞失败。

# RTP自动调整

```
d813a182-9e22-11ec-8aa8-f13e5a2da8b2 2022-03-07 22:28:32.437757 96.53% [INFO] switch_rtp.c:7894 Auto Changing audio port from 10.153.210.122:7076 to 117.136.31.145:60993
```

收到客户端发送的RTP包后，将保存的客户端地址从SDP中的私网地址调整为RTP的公网地址，后续就可以向客户端发送RTP包。

# local_stream流

```
EXECUTE [depth=0] sofia/internal/1000@101.102.103.104 playback(local_stream://moh)
2022-04-03 15:05:00.173402 97.00% [WARNING] mod_local_stream.c:874 Unknown source moh, trying 'default'
2022-04-03 15:05:00.173402 97.00% [DEBUG] mod_local_stream.c:911 Opening Stream [default] 8000hz
2022-04-03 15:05:00.173402 97.00% [DEBUG] switch_core_file.c:444 File moh sample rate 8000 doesn't match requested rate 48000
```

# 主叫号码翻转

```
2022-03-13 20:17:28.119235 97.20% [INFO] switch_channel.c:3270 sofia/internal/1000@116.28.141.243:3875 Flipping CID from "test" <0000000000> to "Outbound Call" <1000>
```
