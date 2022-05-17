本文更新于2022-05-12。

官方文档参阅：[http://supervisord.org/](http://supervisord.org/)

[TOC]

# 安装

## CentOS下安装

以CentOS 7.5 为例。

```shell
yum install supervisor
```

## Debian下安装

以Debian 8.9 (jessie)为例。

```shell
apt install supervisor
```

如使用的apt源找不到相应的包，可参考以下步骤：

通过`vi /etc/apt/sources.list`修改apt源为：

```
deb http://mirrors.163.com/debian/ jessie main non-free contrib
deb http://mirrors.163.com/debian/ jessie-updates main non-free contrib
deb http://mirrors.163.com/debian/ jessie-backports main non-free contrib
deb http://mirrors.163.com/debian-security/ jessie/updates main non-free contrib
deb-src http://mirrors.163.com/debian/ jessie main non-free contrib
deb-src http://mirrors.163.com/debian/ jessie-updates main non-free contrib
deb-src http://mirrors.163.com/debian/ jessie-backports main non-free contrib
deb-src http://mirrors.163.com/debian-security/ jessie/updates main non-free contrib
```

然后执行`apt update`。如出现"[Waiting for headers]"，则需执行以下命令再重新运行`apt update`：

```shell
cd /var/lib/apt/lists
rm *
rm partial/*
```

再执行`apt install supervisor`。

# 开机启动

系统服务名为supervisor或supervisord。

```shell
systemctl enable supervisor
```

或

```shell
systemctl enable supervisord
```

# 命令

下述，“|”表示使用左侧或右侧，“<>”表示其括起的内容可选，“{}”表示其括起的内容为一个整体，“...”表示重复之前的内容，“PROGRAM”为配置中program小节设置的程序名。

* supervisorctl：进入命令行交互界面，可直接输入子命令进行管理。
* supervisorctl reload：重新加载配置文件，并重启supervisord及所有自动启动的程序。
* supervisorctl restart {PROGRAM< ...>}|all：重启程序，all表示重启所有程序。
* supervisorctl start {PROGRAM< ...>}|all：启动程序，all表示启动所有程序。
* supervisorctl status <{PROGRAM< ...>}|all>：查看程序状态，如为all或不指定则查看所有程序状态。
* supervisorctl stop {PROGRAM< ...>}|all：停止程序，all表示停止所有程序。
* supervisorctl update：重新加载配置文件，并启动新配置指定的程序。

# 配置

CentOS中的配置文件为/etc/supervisord.conf，配置文件目录为/etc/supervisord.d/，配置文件目录中的配置文件后缀为.ini。
Debian中的配置文件为/etc/supervisor/supervisord.conf，配置文件目录为/etc/supervisor/conf.d，配置文件目录中的配置文件后缀为.conf。

自定义的配置文件最好放于配置文件目录。每个配置文件可包含多个小节，使用“;”开头进行注释。

## supervisord小节

修改此小节的内容需要使用`systemctl restart supervisor`或`systemctl restart supervisord`重启系统服务。

配置示例如下（各配置均已被注释）：

```
;[supervisord]
;logfile=/var/log/supervisor/supervisord.log ; (main log file;default $CWD/supervisord.log)
;pidfile=/var/run/supervisord.pid            ; (supervisord pidfile;default supervisord.pid)
;childlogdir=/var/log/supervisor             ; ('AUTO' child log dir, default $TEMP)
;minfds=1024
```

* [supervisord]：supervisord服务进程。
* minfds=N：supervisord服务进程启动时必需的最少可用文件描述符数量，也是个program小节定义的被管理程序的最大可用文件描述符数量（即Max open files的值）。默认为1024。

## program小节

修改此小节的内容需要使用`supervisorctl restart PROGRAM`重启被管理的程序。

配置示例如下（各配置均已被注释）：

```
;[program:theprogramname]
;command=/bin/cat              ; the program (relative uses PATH, can take args)
;process_name=%(program_name)s ; process_name expr (default %(program_name)s)
;numprocs=1                    ; number of processes copies to start (def 1)
;directory=/tmp                ; directory to cwd to before exec (def no cwd)
;umask=022                     ; umask for process (default None)
;priority=999                  ; the relative start priority (default 999)
;autostart=true                ; start at supervisord start (default: true)
;autorestart=true              ; retstart at unexpected quit (default: true)
;startsecs=10                  ; number of secs prog must stay running (def. 1)
;startretries=3                ; max # of serial start failures (default 3)
;exitcodes=0,2                 ; 'expected' exit codes for process (default 0,2)
;stopsignal=QUIT               ; signal used to kill process (default TERM)
;stopwaitsecs=10               ; max num secs to wait b4 SIGKILL (default 10)
;user=chrism                   ; setuid to this UNIX account to run the program
;redirect_stderr=true          ; redirect proc stderr to stdout (default false)
;stdout_logfile=/a/path        ; stdout log path, NONE for none; default AUTO
;stdout_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stdout_logfile_backups=10     ; # of stdout logfile backups (default 10)
;stdout_capture_maxbytes=1MB   ; number of bytes in 'capturemode' (default 0)
;stdout_events_enabled=false   ; emit events on stdout writes (default false)
;stderr_logfile=/a/path        ; stderr log path, NONE for none; default AUTO
;stderr_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stderr_logfile_backups=10     ; # of stderr logfile backups (default 10)
;stderr_capture_maxbytes=1MB   ; number of bytes in 'capturemode' (default 0)
;stderr_events_enabled=false   ; emit events on stderr writes (default false)
;environment=A=1,B=2           ; process environment additions (def no adds)
;serverurl=AUTO                ; override serverurl computation (childutils)
```

* [program:PROGRAM]：被管理的程序名。可指定多个program小节。
* command=CMD：执行的命令，可带参数。
* directory=DIR：执行命令时的进程运行目录。
* autostart=AUTO：是否自动启动。默认为true。
* autorestart=AUTO：是否自动重启。为true、false或unexpected，默认为true。如为unexpected，当进程的退出码不为exitcodes中的值时重启。
* exitcodes=CODE1,CODE2：允许的进程退出码。以“,”分隔，默认为0,2。
* stopsignal=SIGNAL：停止进程时发送的信号。默认为TERM。
* user=USER：执行命令使用的系统用户。
* stdout_logfile=FILENAME：stdout的重定向日志文件。
* stderr_logfile=FILENAME：stderr的重定向日志文件。
