# supervisor&cesi

标签（空格分隔）： 施华

---

[TOC]
# supervisor
+ 安装
```
$pip install supervisor
$#使用非root用户，如anaconda的pip安装的话，supervisor会出现在anaconada3/bin下。
```
+ 配置
```
$echo_supervisord_conf > (自己指定的绝对路径下)/home/shihua/Config/supervisor/supervisord.conf
```
**supervisord.conf示例**
```
; Sample supervisor config file.
;
; For more information on the config file, please see:
; http://supervisord.org/configuration.html
;
; Notes:
;  - Shell expansion ("~" or "$HOME") is not supported.  Environment
;    variables can be expanded using this syntax: "%(ENV_HOME)s".
;  - Quotes around values are not supported, except in the case of
;    the environment= options as shown below.
;  - Comments must have a leading space: "a=b ;comment" not "a=b;comment".
;  - Command will be truncated if it looks like a config file comment, e.g.
;    "command=bash -c 'foo ; bar'" will truncate to "command=bash -c 'foo ".
;
; Warning:
;  Paths throughout this example file use /tmp because it is available on most
;  systems.  You will likely need to change these to locations more appropriate
;  for your system.  Some systems periodically delete older files in /tmp.
;  Notably, if the socket file defined in the [unix_http_server] section below
;  is deleted, supervisorctl will be unable to connect to supervisord.

[unix_http_server]
file=/tmp/supervisor.sock   ; the path to the socket file
;chmod=0700                 ; socket file mode (default 0700)
;chown=nobody:nogroup       ; socket file uid:gid owner
;username=user              ; default is no username (open server)
;password=123               ; default is no password (open server)

; Security Warning:
;  The inet HTTP server is not enabled by default.  The inet HTTP server is
;  enabled by uncommenting the [inet_http_server] section below.  The inet
;  HTTP server is intended for use within a trusted environment only.  It
;  should only be bound to localhost or only accessible from within an
;  isolated, trusted network.  The inet HTTP server does not support any
;  form of encryption.  The inet HTTP server does not use authentication
;  by default (see the username= and password= options to add authentication).
;  Never expose the inet HTTP server to the public internet.

[inet_http_server]         ; inet (TCP) server disabled by default
port=127.0.0.1:9001        ; ip_address:port specifier, *:port for all iface
username=user              ; default is no username (open server)
password=123               ; default is no password (open server)

[supervisord]
logfile=/home/shihua/Log/supervisor/supervisord.log ; main log file; default $CWD/supervisord.log
logfile_maxbytes=50MB        ; max main logfile bytes b4 rotation; default 50MB
logfile_backups=10           ; # of main logfile backups; 0 means none, default 10
loglevel=info                ; log level; default info; others: debug,warn,trace
pidfile=/home/shihua/Log/supervisor/supervisord.pid ; supervisord pidfile; default supervisord.pid
nodaemon=false               ; start in foreground if true; default false
silent=false                 ; no logs to stdout if true; default false
minfds=1024                  ; min. avail startup file descriptors; default 1024
minprocs=200                 ; min. avail process descriptors;default 200
;umask=022                   ; process file creation umask; default 022
;user=supervisord            ; setuid to this UNIX account at startup; recommended if root
;identifier=supervisor       ; supervisord identifier, default is 'supervisor'
;directory=/tmp              ; default is not to cd during start
;nocleanup=true              ; don't clean up tempfiles at start; default false
;childlogdir=/tmp            ; 'AUTO' child log dir, default $TEMP
;environment=KEY="value"     ; key value pairs to add to environment
;strip_ansi=false            ; strip ansi escape codes in logs; def. false

; The rpcinterface:supervisor section must remain in the config file for
; RPC (supervisorctl/web interface) to work.  Additional interfaces may be
; added by defining them in separate [rpcinterface:x] sections.

[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

; The supervisorctl section configures how supervisorctl will connect to
; supervisord.  configure it match the settings in either the unix_http_server
; or inet_http_server section.

[supervisorctl]
serverurl=unix:///tmp/supervisor.sock ; use a unix:// URL  for a unix socket
;serverurl=http://127.0.0.1:9001 ; use an http:// url to specify an inet socket
;username=chris              ; should be same as in [*_http_server] if set
;password=123                ; should be same as in [*_http_server] if set
;prompt=mysupervisor         ; cmd line prompt (default "supervisor")
;history_file=~/.sc_history  ; use readline history if available

; The sample program section below shows all possible program subsection values.
; Create one or more 'real' program: sections to be able to control them under
; supervisor.

;[program:theprogramname]
;command=/bin/cat              ; the program (relative uses PATH, can take args)
;process_name=%(program_name)s ; process_name expr (default %(program_name)s)
;numprocs=1                    ; number of processes copies to start (def 1)
;directory=/tmp                ; directory to cwd to before exec (def no cwd)
;umask=022                     ; umask for process (default None)
;priority=999                  ; the relative start priority (default 999)
;autostart=true                ; start at supervisord start (default: true)
;startsecs=1                   ; # of secs prog must stay up to be running (def. 1)
;startretries=3                ; max # of serial start failures when starting (default 3)
;autorestart=unexpected        ; when to restart if exited after running (def: unexpected)
;exitcodes=0                   ; 'expected' exit codes used with autorestart (default 0)
;stopsignal=QUIT               ; signal used to kill process (default TERM)
;stopwaitsecs=10               ; max num secs to wait b4 SIGKILL (default 10)
;stopasgroup=false             ; send stop signal to the UNIX process group (default false)
;killasgroup=false             ; SIGKILL the UNIX process group (def false)
;user=chrism                   ; setuid to this UNIX account to run the program
;redirect_stderr=true          ; redirect proc stderr to stdout (default false)
;stdout_logfile=/a/path        ; stdout log path, NONE for none; default AUTO
;stdout_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stdout_logfile_backups=10     ; # of stdout logfile backups (0 means none, default 10)
;stdout_capture_maxbytes=1MB   ; number of bytes in 'capturemode' (default 0)
;stdout_events_enabled=false   ; emit events on stdout writes (default false)
;stdout_syslog=false           ; send stdout to syslog with process name (default false)
;stderr_logfile=/a/path        ; stderr log path, NONE for none; default AUTO
;stderr_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stderr_logfile_backups=10     ; # of stderr logfile backups (0 means none, default 10)
;stderr_capture_maxbytes=1MB   ; number of bytes in 'capturemode' (default 0)
;stderr_events_enabled=false   ; emit events on stderr writes (default false)
;stderr_syslog=false           ; send stderr to syslog with process name (default false)
;environment=A="1",B="2"       ; process environment additions (def no adds)
;serverurl=AUTO                ; override serverurl computation (childutils)

; The sample eventlistener section below shows all possible eventlistener
; subsection values.  Create one or more 'real' eventlistener: sections to be
; able to handle event notifications sent by supervisord.

;[eventlistener:theeventlistenername]
;command=/bin/eventlistener    ; the program (relative uses PATH, can take args)
;process_name=%(program_name)s ; process_name expr (default %(program_name)s)
;numprocs=1                    ; number of processes copies to start (def 1)
;events=EVENT                  ; event notif. types to subscribe to (req'd)
;buffer_size=10                ; event buffer queue size (default 10)
;directory=/tmp                ; directory to cwd to before exec (def no cwd)
;umask=022                     ; umask for process (default None)
;priority=-1                   ; the relative start priority (default -1)
;autostart=true                ; start at supervisord start (default: true)
;startsecs=1                   ; # of secs prog must stay up to be running (def. 1)
;startretries=3                ; max # of serial start failures when starting (default 3)
;autorestart=unexpected        ; autorestart if exited after running (def: unexpected)
;exitcodes=0                   ; 'expected' exit codes used with autorestart (default 0)
;stopsignal=QUIT               ; signal used to kill process (default TERM)
;stopwaitsecs=10               ; max num secs to wait b4 SIGKILL (default 10)
;stopasgroup=false             ; send stop signal to the UNIX process group (default false)
;killasgroup=false             ; SIGKILL the UNIX process group (def false)
;user=chrism                   ; setuid to this UNIX account to run the program
;redirect_stderr=false         ; redirect_stderr=true is not allowed for eventlisteners
;stdout_logfile=/a/path        ; stdout log path, NONE for none; default AUTO
;stdout_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stdout_logfile_backups=10     ; # of stdout logfile backups (0 means none, default 10)
;stdout_events_enabled=false   ; emit events on stdout writes (default false)
;stdout_syslog=false           ; send stdout to syslog with process name (default false)
;stderr_logfile=/a/path        ; stderr log path, NONE for none; default AUTO
;stderr_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stderr_logfile_backups=10     ; # of stderr logfile backups (0 means none, default 10)
;stderr_events_enabled=false   ; emit events on stderr writes (default false)
;stderr_syslog=false           ; send stderr to syslog with process name (default false)
;environment=A="1",B="2"       ; process environment additions
;serverurl=AUTO                ; override serverurl computation (childutils)

; The sample group section below shows all possible group values.  Create one
; or more 'real' group: sections to create "heterogeneous" process groups.

;[group:thegroupname]
;programs=progname1,progname2  ; each refers to 'x' in [program:x] definitions
;priority=999                  ; the relative start priority (default 999)

; The [include] section can just contain the "files" setting.  This
; setting can list multiple files (separated by whitespace or
; newlines).  It can also contain wildcards.  The filenames are
; interpreted as relative to this file.  Included files *cannot*
; include files themselves.

[include]
files = /home/shihua/Config/supervisor/*.conf
```
**systemd管理开机自启**
新建supervisord.service 路径：/usr/lib/systemd/system
```
[Unit]
Description=Supervisor Service

[Service]
Type=forking
ExecStart=/home/shihua/anaconda3/bin/supervisord -c /home/shihua/Config/supervisor/supervisord.conf
ExecStop=/home/shihua/anaconda3/bin/supervisorctl shutdown
ExecReload=/home/shihua/anaconda3/bin/suupervisorctl reload
KillMode=process
ReStart=on-failure
RestartSec=42s

[Install]
WantedBy=multi-user.target
```
启动服务
```
$sudo systemctl enable supervisord.service
$sudo systemctl is-enabled supervisord.service
```
+ 使用
在指定文件夹下配置子进程，配置好后使用systemd重启supervisord服务即可生效，默认前端为9001端口。
**dask_scheduler.conf示例**
+ superset run -p 5001 -h 0.0.0.0 --with-threads --reload --debugger
```
[program:dask_scheduler]
directory=/home/shihua/
command=/home/shihua/anaconda3/bin/dask-scheduler
autostart=true
autorestart=true
startsecs=1
user=shihua
stderr_logfile=/home/shihua/Log/supervisor/dask_scheduler_stderr.log
stdout_loggile=/home/shihua/Log/supervisor/dask_scheduler_stdout.log
redirect_stderr=true
stdout_logfile_maxbytes = 20MB
stdout_logfile_backups = 20

```
# cesi
+ 安装
```
$ export CESI_SETUP_PATH=~/cesi
$ mkdir ${CESI_SETUP_PATH}
$ cd ${CESI_SETUP_PATH}

$ # Download the project to ~/cesi directory
$ wget https://github.com/gamegos/cesi/releases/download/v2.7.1/cesi-extended.tar.gz -O cesi.tar.gz
$ tar -xvf cesi.tar.gz

$ pip3 install -r requirements.txt

$ # Run with command line
$ python3 ${CESI_SETUP_PATH}/cesi/run.py --config-file ${CESI_SETUP_PATH}/defaults/cesi.conf.toml
$ cd ${CESI_SETUP_PATH}/cesi/
$ python run.py --config-file /etc/cesi.conf -p 9191
```
+ 配置
**cesi.conf.toml示例**
可以将cesi.conf.toml复制到/etc/cesi.conf，启动的时候指定该路径。
```
# This is the main CeSI toml configuration file. It contains CeSI web application and
# supervisord information to connect

# This is the CeSI's own configuration.
[cesi]
# Database Uri
database = "sqlite:///users.db"                         # Relative path
# Etc
#database = "sqlite:////opt/cesi/< version >/users.db"  # Absolute path
#database = "postgres://<user>:<password>@localhost:5432/<database_name>"
#database = "mysql+pymysql://<user>:<password>@localhost:3306/<database_name>"
activity_log = "activity.log"   # File path for CeSI logs
admin_username = "admin"        # Username of admin user
admin_password = "admin"        # Password of admin user

# This is the definition section for new supervisord node.
# [[nodes]]
# name = "api"          # (String) Unique name for supervisord node.
# environment = ""      # (String) The environment name provides logical grouping of supervisord nodes. It can be used as filtering option in the UI.
# username = ""         # (String) Username of the XML-RPC interface of supervisord Set nothing if no username is configured
# password = ""         # (String) Password of the XML-RPC interface of supervisord. Set nothing if no username is configured
# host = "127.0.0.1"    # (String) Host of the XML-RPC interface of supervisord
# port = "9001"         # (String) Port of the XML-RPC interface of supervisord

# Default supervisord nodes
[[nodes]]
name = "shihua001"
environment = ""
username = "user" ## supervisor web username
password = "123"
host = "127.0.0.1"
port = "9001"

```





