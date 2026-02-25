为什么选 K3s？

专门为 ARM / Raspberry Pi 优化

内存占用低

单命令安装

支持 HA

非常适合 homelab

官方项目：
👉 K3s
👉 Rancher Labs

🧱 安装总体结构
Steve        → Control Plane (Master)
nugget1      → Worker
nugget2      → Worker
nugget3      → Worker
🛠️ 第一步：所有 Pi 统一准备

在 每一台 Pi 上做：

1️⃣ 更新系统
sudo apt update && sudo apt upgrade -y
2️⃣ 关闭 swap（必须）
sudo dphys-swapfile swapoff
sudo systemctl disable dphys-swapfile
3️⃣ 打开 cgroup（关键）

编辑：

sudo nano /boot/firmware/cmdline.txt

在同一行最后加：

cgroup_memory=1 cgroup_enable=memory

重启：

sudo reboot
🧠 第二步：在 Steve 上安装 K3s (Master)

在 Steve 上执行：

curl -sfL https://get.k3s.io | sh -

安装完成后查看：

sudo kubectl get nodes

如果显示 Steve → Ready
说明 control plane OK。

🔑 获取加入 token

在 Steve 上：

sudo cat /var/lib/rancher/k3s/server/node-token

复制那一串 token。

👷 第三步：在三个 nugget 上加入集群

在 nugget1 / 2 / 3 上分别执行：

curl -sfL https://get.k3s.io | K3S_URL=https://STEVE_IP:6443 K3S_TOKEN=你的token sh -

例如：

K3S_URL=https://192.168.1.50:6443
✅ 最后验证

回到 Steve：

sudo kubectl get nodes

应该看到：

steve      Ready
nugget1    Ready
nugget2    Ready
nugget3    Ready

这时你就拥有一个真正的 Kubernetes 集群了。