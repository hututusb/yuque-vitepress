## 完毕！您的设备现在可以从任何地方进行连接。
Tailscale 网络中的每台设备都有一个私有 100.xyz IP 地址，无论您身在何处都可以访问该地址。每个协议都有效 - SSH、RDP、HTTP、Minecraft - 在 Tailscale 运行时使用您想要的任何协议。
试试看！在您的设备之间发送 ping。从**izbp13jpvkrismpyagqi4kz**上的终端运行此命令以 ping **ubuntu-s-1vcpu-2gb-amd-sgp1-01**（交换）
```
ping 100.121.49.80 -c 4
```
如果有效，您将看到“<number> bytes from 100.121.49.80”
成功了，有效！[故障排除](https://tailscale.com/kb/1023/troubleshooting/)

> 来自: [尾鳞](https://login.tailscale.com/admin/welcome)

