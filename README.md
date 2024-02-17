# UA3F

UA3F 是新一代 HTTP User-Agent 修改方法，对外作为一个 SOCK5 服务，可以部署在路由器等设备等设备进行透明 UA 修改。

![UA3F](https://sunbk201.oss-cn-beijing.aliyuncs.com/img/ua3f)

## 部署

[Release](https://github.com/SunBK201/UA3F/releases) 页面已经提供常见架构的编译版本，可以根据自己架构下载并解压到路由器等设备上。

安装（升级）脚本：
```sh
opkg update
opkg install curl luci-compat
export url='https://blog.sunbk201.site/cdn' && sh -c "$(curl -kfsSl $url/install.sh)"
service ua3f.service reload
```

## 使用

UA3F 已支持 LuCI Web 页面，可以打开 Services -> UA3F 进行相关配置。

![UA3F-LuCI](https://sunbk201.oss-cn-beijing.aliyuncs.com/img/ua3f-luci)

参数:
- `-p <port>`: 端口号，默认 1080
- `-f <UA>`: 自定义 UA，默认 FFF
- `-b <bind addr>`: 自定义绑定监听地址，默认 127.0.0.1
- `-l <log level>`: 日志等级，默认 info，可选：debug，默认日志位置：`/var/log/ua3f.log`

### 作为后台服务运行

安装脚本执行成功后可通过以下命令启动 UA3F：
```sh
# 启动 UA3F
uci set ua3f.enabled.enabled=1
uci commit ua3f
service ua3f.service start
```

关闭或重启 UA3F 命令：
```sh
# 关闭 UA3F
service ua3f.service stop
# 重启 UA3F
service ua3f.service restart
```

配置 UA3：
```sh
# 自定义 UA
uci set ua3f.main.ua="FFF"
# 监听端口号
uci set ua3f.main.port="1080"
# 绑定地址
uci set ua3f.main.bind="127.0.0.1"
# 日志等级
uci set ua3f.main.log_level="info"

# 应用配置
uci commit ua3f
reload_config
```

### 手动启动
```sh
sudo -u nobody /root/ua3f
```

shellclash 用户建议使用以下命令启动:
```sh
sudo -u shellclash /root/ua3f
```

### Clash 的配置建议

- Clash 需要选用 Meta 内核。
- 请确保 `PROCESS-NAME,ua3f,DIRECT` 置于规则列表顶部，`MATCH,ua3f` 置于规则列表底部。
- 可以在 `PROCESS-NAME,ua3f,DIRECT` 与 `MATCH,ua3f` 之间按需加入自定义加密代理规则。如果上述 2 条规则之间加入 DIRECT 规则，请确保匹配该规则的流量属于非 HTTP 协议流量。

```yaml
proxies:
  - name: "ua3f"
    type: socks5
    server: 127.0.0.1
    port: 1080
    url: http://connectivitycheck.platform.hicloud.com/generate_204
    udp: false

rules:
  - PROCESS-NAME,ua3f,DIRECT
  - NETWORK,udp,DIRECT
  - MATCH,ua3f
```

请不要将从 [Release](https://github.com/SunBK201/UA3F/releases) 下载解压得到的 `ua3f` 二进制文件修改名称，
如需修改，则需要在 `PROCESS-NAME,ua3f,DIRECT` 中修改相应的名称。

### Clash 懒人配置

提供 2 个参考配置：
1. 国内版，无需进行任何修改，可直接使用 [ua3f-cn.yaml](https://cdn.jsdelivr.net/gh/SunBK201/UA3F@master/clash/ua3f-cn.yaml) (Clash 需要选用 Meta 内核。)
2. 国际版，针对有特定需求的特殊用户进行适配，[ua3f-global.yaml](https://cdn.jsdelivr.net/gh/SunBK201/UA3F@master/clash/ua3f-global.yaml)，注意需要在 proxy-providers > Global-ISP > url 中（第 76 行）加入你的代理节点订阅链接。(Clash 需要选用 Meta 内核。)

## Roadmap

- [x] 支持 LuCI
- [x] 优化部署流程
- [ ] 支持 SOCK5 Auth
- [ ] 支持 UDP
- [ ] 支持 IPv6
- [ ] 性能提升