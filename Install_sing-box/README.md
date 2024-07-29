## 查看系统内核架构
```
uname -m
```
- **amd64(x86_64、x64)：表示基于 AMD 的 6 4位内核。**
- **386（i686或i386）：表示 32 位内核。**
- **armv7l：表示基于 ARM 的 32 位内核。**
- **arm64(aarch64)：表示基于 ARM 的 64 位内核。**
## go 官网下载地址 [https://golang.org/dl/](https://go.dev/dl/)。
```
wget -c https://go.dev/dl/go1.20.7.linux-amd64.tar.gz -O - | tar -xz -C /usr/local && echo 'export PATH=$PATH:/usr/local/go/bin' > /etc/profile && source /etc/profile &&  go version
```
- **'-c'：此选项告诉 'wget' 如果文件已存在则继续先前中断的下载，从而允许从中断处恢复下载。**
- **'-O -'：此选项告诉 'wget' 将下载的文件输出到标准输出，而不是将其保存到磁盘上，连字符 '-' 表示标准输出。**
- **'|'：一个管道符号，用于将一个命令的标准输出重定向到另一命令的标准输入。**
- **'tar -xz -C /usr/local'：这部分命令使用 'tar' 从标准输入中提取下载的文件，并将其解压到 '/ usr/local' 目录。**
- **'PATH=$PATH:/usr/local/go/bin'：设置 PATH 环境变量。**
- **'source /etc/profile' 加载并应用环境变量。**
- **'go version' 显示系统上安装的 go 版本。**
## 编译安装 sing-box
```
go install -v -tags \
with_quic,\
with_grpc,\
with_dhcp,\
with_wireguard,\
with_shadowsocksr,\
with_ech,\
with_utls,\
with_reality_server,\
with_acme,\
with_clash_api,\
with_v2ray_api,\
with_gvisor,\
with_embedded_tor,\
with_lwip \
github.com/sagernet/sing-box/cmd/sing-box@latest
```
- **每个选项的具体意思请到 sing-box官方文档查看，官方文档地址：[https://sing-box.sagernet.org/zh/installation/from-source/](https://sing-box.sagernet.org/zh/installation/from-source/)**
## 下载预编译版 sing-box
### 预编译版下载地址 [https://github.com/SagerNet/sing-box/releases](https://github.com/SagerNet/sing-box/releases).
```
wget -c "https://github.com/SagerNet/sing-box/releases/download/v1.4.0-beta.6/sing-box-1.4.0-beta.6-linux-amd64.tar.gz" -O - | tar -xz -C /usr/local/bin --strip-components=1
```
- **'-c' 此选项告诉 'wget' 如果文件已存在则继续先前中断的下载，从而允许从中断处恢复下载。**
- **'-O -'：此选项告诉 'wget' 将下载的文件输出到标准输出，而不是将其保存到磁盘上，连字符 '-' 表示标准输出。**
- **'|'：一个管道符号，用于将一个命令的标准输出重定向到另一命令的标准输入。**
- **'tar -xz -C /usr/local/bin --strip-components=1'：这部分命令使用 'tar' 来提取下载的 tar.gz 文件。选项 '-x' 和 '-z' 告诉' tar' 提取并解压缩 gzip 文档。'-C /usr/local/bin' 选项指定解压缩文件的目标目录，'--strip-components=1' 选项告诉 'tar' 在解压时删除上一级目录，这样文件直接放在 '/usr/local/bin' 中，而没有不必要的目录。**

## 配置sing-box开机自启服务
```
cat > /etc/systemd/system/sing-box.service <<EOF
[Unit]
Description=sing-box service
Documentation=https://sing-box.sagernet.org
After=network.target nss-lookup.target

[Service]
User=root
CapabilityBoundingSet=CAP_NET_ADMIN CAP_NET_BIND_SERVICE CAP_SYS_PTRACE CAP_DAC_READ_SEARCH
AmbientCapabilities=CAP_NET_ADMIN CAP_NET_BIND_SERVICE CAP_SYS_PTRACE CAP_DAC_READ_SEARCH
ExecStart=/usr/local/bin/sing-box run -c /usr/local/etc/sing-box/config.json
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure
RestartSec=10s
LimitNOFILE=infinity

[Install]
WantedBy=multi-user.target
EOF
```
- **'ExecStart=/usr/local/bin/sing-box run -c /usr/local/etc/sing-box/config.json' 'run' 前半部分的路径是sing-box程序的存放路径，'run' 后面的路径是sing-box配置文件存放的路径。**
