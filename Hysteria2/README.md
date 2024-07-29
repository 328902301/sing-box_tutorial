### **安装sing-box**
#### **更新软件源及安装依赖**
```
apt update && apt -y install wget tar socat
```
#### **下载预发布版sing-box**
- **AMD 内核**
```
wget -c "https://github.com/SagerNet/sing-box/releases/download/v1.5.0-beta.2/sing-box-1.5.0-beta.2-linux-amd64.tar.gz" -O - | tar -xz -C /usr/local/bin --strip-components=1 && chmod +x /usr/local/bin/sing-box
```
- **ARM 内核**
```
wget -c "https://github.com/SagerNet/sing-box/releases/download/v1.5.0-beta.2/sing-box-1.5.0-beta.2-linux-arm64.tar.gz" -O - | tar -xz -C /usr/local/bin --strip-components=1 && chmod +x /usr/local/bin/sing-box
```
### **配置 sing-box 的 systemd 服务**
```
wget -P /etc/systemd/system https://raw.githubusercontent.com/TinrLin/Install_sing-box/main/Hysteria2/sing-box.service
```
### **下载并修改 sing-box 配置文件**
```
mkdir /usr/local/etc/sing-box && wget -P /usr/local/etc/sing-box https://raw.githubusercontent.com/TinrLin/Install_sing-box/main/Hysteria2/congfig.json
```
### **配置证书**
- **安装acme**
```
curl -s https://get.acme.sh | sh -s email=example@gmail.com
```
- **设置 acme 别名**
```
alias acme.sh=~/.acme.sh/acme.sh
```
- **设置 acme 的默认 CA**
```
acme.sh --set-default-ca --server letsencrypt
```
- **生成证书（将www.example.com替换为你的域名）**
```
acme.sh --issue -d www.example.com --standalone
```
- **安装证书（将www.example.com替换为你的域名）**
```
acme.sh --install-cert -d www.example.com --ecc --key-file /etc/ssl/private/private.key --fullchain-file /etc/ssl/private/cert.crt
```
### **启动并运行sing-box**
```
systemctl daemon-reload && systemctl enable --now sing-box && systemctl status sing-box
```
### **Hysteria端口跳跃**
```
# Debian&&Ubuntu

## 安装iptables-persistent
apt install iptables-persistent

## 清空默认规则
iptables -F

## 清空自定义规则
iptables -X

## 允许本地访问
iptables -A INPUT -i lo -j ACCEPT

## 开放SSH端口（假设SSH端口为22）
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

## 开放HTTP端口
iptables -A INPUT -p tcp --dport 80 -j ACCEPT

## 开放UDP端口（10010替换为节点的监听端口）
iptables -A INPUT -p udp --dport 10010 -j ACCEPT

## 开放UDP端口范围（假设UDP端口范围为20000-40000）
iptables -A INPUT -p udp --dport 20000:40000 -j ACCEPT

## 允许接受本机请求之后的返回数据
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT

## 其他入站一律禁止
iptables -P INPUT DROP

## 允许所有出站
iptables -P OUTPUT ACCEPT

## 查看开放的端口
iptables -L

## 添加NAT规则，20000:40000替换为你设置端口跳跃的范围，10010替换为你节点的监听端口
iptables -t nat -A PREROUTING -p udp --dport 20000:40000 -j DNAT --to-destination :10010

## 查看NAT规则
iptables -t nat -nL --line

## 保存iptables规则
netfilter-persistent save
```
```
# CentOS

## 安装iptables-service
yum install iptables-services

## 启用iptables服务
systemctl enable iptables

## 启动iptables服务
systemctl start iptables

## 清空默认规则
iptables -F

## 清空自定义规则
iptables -X

## 允许本地访问
iptables -A INPUT -i lo -j ACCEPT

## 开放SSH端口（假设SSH端口为22）
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

## 开放HTTP端口
iptables -A INPUT -p tcp --dport 80 -j ACCEPT

## 开放UDP端口（10010替换为节点的监听端口）
iptables -A INPUT -p udp --dport 10010 -j ACCEPT

## 开放UDP端口范围（假设UDP端口范围为20000-40000）
iptables -A INPUT -p udp --dport 20000:40000 -j ACCEPT

## 允许接受本机请求之后的返回数据
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT

## 其他入站一律禁止
iptables -P INPUT DROP

## 允许所有出站
iptables -P OUTPUT ACCEPT

## 查看开放的端口
iptables -L

## 添加NAT规则，20000:40000替换为你设置端口跳跃的范围，10010替换为你节点的监听端口
iptables -t nat -A PREROUTING -p udp --dport 20000:40000 -j DNAT --to-destination :10010

## 查看NAT规则
iptables -t nat -nL --line

## 保存iptables规则
service iptables save
``` 
  
