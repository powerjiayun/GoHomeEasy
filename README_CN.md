# 🚀 GoHomeEasy

## [English](README.md) | 中文

**GoHomeEasy** 是一个基于 Cloudflare Workers 的 Shadowsocks/Clash 订阅管理工具，专为 **没有公网 IP 的家庭宽带用户** 设计，能够在外部网络访问家庭局域网。

它利用 **Lucky提供的内网穿透**，并结合订阅自动更新，使用户可以在 **任何地方远程访问家中的 Shadowsocks 服务器**，无需手动频繁更换动态IP地址和端口。

---

## 🌟 **功能特点**

✅ **适合没有公网 IP 的家庭宽带用户，远程访问家庭局域网**  

✅ **支持 Lucky Webhook 自动更新 Shadowsocks 订阅**  

✅ **支持动态配置 Shadowsocks `method`（加密方式）和 `password`（密码）**  

✅ **基于 Cloudflare Workers + KV，无需自建服务器**  

✅ **支持 API Key 认证，确保数据安全**  

✅ **支持 Cloudflare 自有域名访问，绕过 `workers.dev` 在中国大陆的屏蔽**  

---

## ⚙️ **部署前置条件**

要成功部署 **GoHomeEasy**，你需要准备以下环境：

🔹 **Linux家庭服务器或OpenWRT软路由**

🔹 **配置Shadowsocks服务器**（推荐使用OpenWRT中的[PassWall2插件](https://github.com/xiaorouji/openwrt-passwall2)）

🔹 **安装[Lucky 内网穿透](https://lucky666.cn)**，按教程开启内网穿透功能，将Shadowsocks服务器端口映射到公网

🔹 **Cloudflare账号**（免费账号即可，用于 Workers 部署）

🔹 **使用Cloudflare管理DNS解析的域名**（可选，仅用于绕过中国大陆对workers.dev域名的屏蔽）

🔹 **支持解析Shadowsocks或Clash YAML订阅的手机/电脑客户端**（如iOS中的Shadowrocket APP）

## 💻 **配置Shadowsocks服务器**

以Passwall2为例：

### **1️⃣ 在Passwall2的 “服务器端” 选项卡中点击 “添加” 按钮**
### **2️⃣ 按以下内容配置：**
   - 启用：勾选
   - 备注：自定名称
   - 类型：Sing-Box
   - 协议名称：Shadowsocks
   - 监听端口：8000（或自选）
   - 密码：自定
   - 加密：自选，建议选择chacha20-ietf-poly1305
   - 接受局域网访问：**请务必勾选**
   - 其他保持默认即可
### **3️⃣ 点击右下角“保存并应用“按钮，回到主菜单**
### **4️⃣ 勾选“启用”，之后点击右下角“保存并应用“按钮**


---

## 🛠 **配置 Cloudflare Workers**

### **1️⃣ 创建 Workers 服务**
1. 登录 **[Cloudflare Dashboard](https://dash.cloudflare.com/)**
2. 进入左侧 **Workers & Pages**，点击蓝色 **“创建”** 按钮
3. 选择 **“从模板开始”** 中的 **“Hello world”**
4. 输入 **服务名称**（如 `GoHomeEasy`），点击右下角蓝色 **“部署”** 按钮

### **2️⃣ 编辑 Workers 代码**
1. 进入 **新建的 Worker**，点击右上角 **“< / >” 按钮**，编辑项目代码
2. **删除默认代码**
3. **粘贴 `GoHomeEasy_XXX.js` 代码**

   3.1 本项目的 `GoHomeEasy_SS.js` 文件对应Shadowsocks标准订阅格式。
   
   3.2 本项目的 `GoHomeEasy_Clash.js` 文件对应Clash YAML标准订阅格式。

   如果使用iOS Shadowrocket APP，二者都可以。否则请根据你使用的APP选择支持的格式。
   
5. **修改源代码中的"your_secure_api_key"**，并保留好这一密钥字段
6. **点击 “部署” 按钮**

### **3️⃣ 绑定 Cloudflare KV 存储**
1. 进入左侧菜单 **对象和数据库** → **KV**
2. 点击蓝色 **+创建** 按钮，命名为 `GoHomeEasy_KV`
3. 进入刚刚创建的 **Worker** → **“设置”**
4. **点击左侧菜单 “绑定” ，再点击右侧 “定义 Worker 可用的资源集”旁的“+添加”，选择“KV命名空间”**
   - **变量名称**: `KV_NAMESPACE`
   - **KV命名空间**: 选择 `GoHomeEasy_KV`
1. 点击右下角蓝色 **部署** 按钮

---

## 🌍 **使用 Cloudflare 自有域名访问 Workers（可选，用于绕过中国大陆屏蔽）**

#### **按此方法设置：https://github.com/zizifn/edgetunnel/issues/27**

---

## 🔗 **配置 Lucky STUN内网穿透Webhook**

在 **Lucky STUN内网穿透配置页面**，填写以下内容：

1. **穿透类型**：IPV4-TCP
2. **UPnP**：开启（如果你的Lucky安装在非拨号路由器设备，需要开启本选项，并需要开启拨号路由器设备的UPnP）
3. **不使用Lucky内置端口转发**：建议开启，可以有效增加访问速度）如果遇到问题，可以尝试关闭）
4. **UPnP 内部端口自定义**：（Passwall中设置的）Shadowrocket服务端口，如8000
5. **Webhook**：开启，并填写以下内容：

### **1️⃣ Webhook URL（POST 请求）**

请更改为你自己的域名：

- 🌍 **Cloudflare Workers 原生域名**（适用于未被屏蔽地区）：
 ```
 https://your-worker-name.your-account-name.workers.dev/
 ```
- 🇨🇳 **Cloudflare 自有域名**（适用于中国大陆）：
 ```
 https://your-gohome-name.yourdomain.com/
 ```

### **2️⃣ 请求头（Headers）**
```
   Content-Type: application/json
   Authorization: Bearer 此处替换为你的API_KEY 
```

### **3️⃣ 请求体（Body）**
```json
{
  "ip": "#{ip}",
  "port": "#{port}",
  "method": "chacha20-ietf-poly1305",
  "password": "your_password"
}
```

📌 **说明**：
- `#{ip}` 和 `#{port}` 是 Lucky 自动填充的公网 IP 和端口。
- `method` 可根据 Shadowsocks 服务器的加密方式调整（推荐使用 `chacha20-ietf-poly1305`）。
- `password` 为 Shadowsocks 服务器的连接密码。

**禁用接口调用成功字符串检测**：打开

### **重要❗️：（给中国大陆用户）

1. 如果你的Lucky所在服务器可自动翻墙，请务必在翻墙服务设置STUN服务的3478端口为【直连（不翻墙）】，否则获取到的公网IP会错误。**
2. 如果你的Lucky所在服务器不可自动翻墙，HTTPS协议访问Cloudflare域名可能会遇到404错误，导致webhook更新失败。如遇到此问题，建议配置将该服务器可自动翻墙，或者可以尝试在Webhook URL改为http访问（但因此请求会明文发送，导致API_KEY泄漏）。

---

## 📥 **4️⃣ 客户端订阅配置**

以Shadowrocket🚀APP为例：

### **1️⃣ 添加订阅 URL**

1. **打开 Shadowrocket，点击右上角 `+` 号，类型选择“订阅”**  
2. **URL 填写**（适用于不同网络环境）：
   - 🌍 **Cloudflare Workers 原生域名**（适用于未被屏蔽地区）：
     ```
     https://your-worker-name.workers.dev/?api_key=your_secure_api_key
     ```
   - 🇨🇳 **Cloudflare 自有域名**（适用于中国大陆）：
     ```
     https://gohome.yourdomain.com/?api_key=your_secure_api_key
     ```
1. **点击“保存”**，订阅会自动更新  ✅  

---

### **2️⃣ 修改 Shadowrocket 分流规则**

1. **进入 `配置`**  
2. **选择 `规则`**  
3. **在规则列表的第一行（最优先）添加以下规则：**  
   - 📌 **类型**：`IP-CIDR`  
   - 📌 **IP CIDR**：`192.168.1.0/24`（或你的家庭局域网网段）  
   - 📌 **策略**：选择 `GoHomeEasy` 代理节点
4. **回到上一级菜单，选择`通用`，删除以下内容：**
   - “跳过代理”中包含你的家庭局域网的网段，如192.168.0.0./16
   - “TUN旁路路由”中包含你的家庭局域网的网段，如192.168.0.0./16
5. **保存规则，重启VPN，应用配置**  ✅  

---

### **3️⃣ 网段冲突时的设置（可选）**

如果你在 **另一个与家庭局域网相同网段的网络**（如同为192.168.1.X）中，仍想访问家庭局域网，请调整 **Shadowrocket 设置**：

1. **进入 `Shadowrocket → 设置`**  
2. **找到 `按需连接`**  
3. **开启以下选项**：
   - ✅ **包括所有网络**  
   - ✅ **包括本地网络**  
4. **这样可以确保在同一网段的外部网络中仍然可以访问家庭局域网设备** 🎯  

---

🚀 **GoHomeEasy，让没有公网 IP 的家庭宽带用户也能随时随地访问家庭局域网！** 🌎
