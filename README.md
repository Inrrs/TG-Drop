# Drop中转站

Drop中转站是一个基于 Cloudflare Pages 的多功能内容分享平台，支持文本、代码、诗歌、图片和文件的在线分享和存储。

## 功能特点

- 多种内容类型支持：
  - 普通文本：支持markdown格式
  - 代码：自动语法高亮
  - 诗歌：优雅排版展示
  - 图片：支持预览和下载
  - 文件：支持所有类型文件的上传和下载
- 美观的界面设计：
  - 响应式布局，完美适配手机和电脑
  - 优雅的中文字体渲染
  - 简洁现代的界面风格
- 便捷的操作体验：
  - 一键添加新内容
  - 拖拽上传文件
  - 实时预览效果
  - 快速编辑和删除

## 技术架构

- 前端：
  - HTML5 + CSS3 + JavaScript
  - Prism.js 用于代码高亮
- 后端：
  - Cloudflare Pages 托管静态资源
  - Cloudflare Workers 处理动态请求
  - Cloudflare D1 SQLite 数据库存储内容
  - Cloudflare KV 存储图片和文件
  - Telegram Bot API 作为可选的文件存储后端

## 项目结构

```
.
├── index.html                    # 主页面
├── css/                         # 样式文件
│   └── style.css               # 主样式文件
├── js/                         # JavaScript文件
│   ├── main.js                # 主逻辑文件
│   └── theme.js               # 主题相关
├── functions/                  # Cloudflare Functions
│   ├── contents/              # 内容管理相关API
│   │   └── [id].js           # 内容CRUD操作
│   ├── images/               # 图片处理相关API
│   │   └── [name].js        # 图片上传和获取
│   └── files/               # 文件处理相关API
│       ├── upload.js        # 文件上传
│       └── [name].js        # 文件获取和删除
├── schema.sql                # 数据库结构
├── _routes.json             # API路由配置
└── README.md               # 项目文档
```

## 使用教程

### 1. 添加新内容

1. 点击页面顶部的"添加新内容"按钮
2. 在弹出的对话框中选择内容类型：
   - 普通文本：直接输入或粘贴文本内容
   - 代码：输入代码，支持自动语法高亮
   - 诗歌：按诗歌格式排版输入
   - 图片：点击上传或拖拽图片文件
   - 文件：点击上传或拖拽任意类型文件
3. 填写标题
4. 点击"保存"按钮完成添加

### 2. 编辑内容

1. 找到要编辑的内容卡片
2. 点击右上角的编辑图标
3. 在弹出的对话框中修改内容
4. 点击"保存"保存修改

### 3. 删除内容

1. 找到要删除的内容卡片
2. 点击右上角的删除图标
3. 确认删除操作

### 4. 下载文件/图片

1. 找到要下载的文件/图片卡片
2. 点击下载图标或文件名即可下载

## 部署教程

### 1. 准备工作

1. 注册 Cloudflare 账号
2. 安装 Node.js 和 npm
3. 安装 Wrangler CLI：
   ```bash
   npm install -g wrangler
   ```
4. （可选）如果使用 Telegram 存储：
   - 创建 Telegram Bot
   - 创建用于存储的 Telegram 频道或群组

### 2. Telegram 机器人配置（可选）

如果你选择使用 Telegram 作为文件存储后端，需要完成以下步骤：

1. 创建 Telegram Bot：
   - 在 Telegram 中找到 [@BotFather](https://t.me/BotFather)
   - 发送 `/newbot` 命令
   - 按提示设置机器人名称和用户名
   - 保存获得的 Bot Token

2. 创建存储频道：
   - 在 Telegram 创建一个新频道或群组
   - 将机器人添加为频道/群组管理员
   - 获取频道/群组 ID：
     - 将频道/群组设为公开
     - 发送一条消息并转发到 [@getidsbot](https://t.me/getidsbot)
     - 记录获得的 chat id（格式如：-1001234567890）

3. 配置环境变量：
   在 Cloudflare Pages 的环境变量中添加：
   - `STORAGE_TYPE`: 设置为 `TELEGRAM`
   - `TELEGRAM_BOT_TOKEN`: 你的机器人 Token
   - `TELEGRAM_CHAT_ID`: 存储频道/群组的 ID

4. 权限设置：
   - 确保机器人在频道/群组中具有发送消息和文件的权限
   - 建议将频道/群组设为私密，以保护文件安全

5. 存储限制：
   - 单个文件最大支持 2GB
   - 图片和视频会自动压缩
   - 所有类型文件都支持上传
   - 下载链接格式：
     - 小于 20MB 的文件：直接下载链接
     - 大于 20MB 的文件：Telegram 消息链接

### 3. 本地开发

1. 克隆项目：
   ```bash
   git clone <项目地址>
   cd dropbox
   ```

2. 安装依赖：
   ```bash
   npm install
   ```

3. 配置环境：
   ```bash
   # 登录到 Cloudflare
   wrangler login
   
   # 创建 D1 数据库
   wrangler d1 create drop-db
   
   # 创建 KV 命名空间
   wrangler kv:namespace create IMAGES
   wrangler kv:namespace create FILES
   ```

4. 初始化数据库：
   ```bash
   wrangler d1 execute drop-db --file=./schema.sql
   ```

5. 启动开发服务器：
   ```bash
   npm run dev
   ```

### 4. 部署到生产环境

1. 在 Cloudflare Pages 中创建新项目
2. 连接 GitHub 仓库
3. 配置构建设置：
   - 构建命令：`npm run build`
   - 输出目录：`/`
4. 配置环境变量：
   - `DB`: D1 数据库绑定
   - `IMAGES`: KV 命名空间绑定
   - `FILES`: KV 命名空间绑定
   - `SYNC_INTERVAL`: 内容同步间隔（毫秒），例如：
     - 30秒：设置为 `30000`
     - 1分钟：设置为 `60000`
     - 5分钟：设置为 `300000`
     - 注意：最小值为5000（5秒）
5. 部署：
   ```bash
   npm run deploy
   ```

### 5. 配置说明

#### 同步间隔设置
- 默认值：30秒（30000毫秒）
- 修改方法：在 Cloudflare Pages 的环境变量中设置 `SYNC_INTERVAL`
- 设置位置：Cloudflare Dashboard > Pages > 你的项目 > Settings > Environment variables
- 生效时间：修改后用户刷新页面即可生效
- 注意事项：
  - 值必须大于等于5000（5秒）
  - 较短的同步间隔会增加API请求频率
  - 建议根据实际需求和免费额度（10万次/天）合理设置
  - 计算公式：`(24小时 * 3600秒) / (同步间隔秒数) = 每天请求次数/用户`

## 常见问题

1. **Q: 上传文件大小有限制吗？**
   A: 
   - 使用 Cloudflare KV 存储时，单个文件最大支持 25MB
   - 使用 Telegram 存储时，单个文件最大支持 2GB

2. **Q: 支持哪些代码语言的高亮？**
   A: 支持所有主流编程语言，包括但不限于：JavaScript、Python、Java、C++、Go等。

3. **Q: 如何备份数据？**
   A: 
   - 数据库内容：通过 Cloudflare D1 的导出功能备份
   - KV 存储的文件：需要单独下载备份
   - Telegram 存储的文件：在 Telegram 频道/群组中永久保存

4. **Q: 为什么选择 Telegram 作为存储后端？**
   A:
   - 无需额外服务器和存储费用
   - 支持大文件（最大 2GB）
   - 全球 CDN 加速
   - 可靠的文件存储和访问
   - 方便的文件管理和备份

5. **Q: Telegram 存储的文件安全吗？**
   A: 
   - 文件存储在私密频道/群组中
   - 访问需要通过应用的 API
   - 建议定期检查频道/群组权限设置
   - 敏感文件建议加密后再上传

## 技术支持

如果遇到问题或需要帮助，可以：
1. 提交 GitHub Issue
2. 查看 Cloudflare 官方文档
3. 参考代码注释

## 开源协议

本项目采用 MIT 协议开源。