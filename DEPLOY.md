# 部署指南

本文档提供了将座位布局生成器应用部署到服务器的详细步骤。

## 前提条件

- 一台运行Linux的服务器（如Ubuntu、CentOS等）
- Python 3.8或更高版本
- 基本的命令行操作知识

## 步骤一：准备环境

1. **安装Python和pip**：
   ```bash
   # Ubuntu/Debian
   sudo apt update
   sudo apt install python3 python3-pip
   
   # CentOS/RHEL
   sudo yum install python3 python3-pip
   ```

2. **创建项目目录**：
   ```bash
   mkdir -p ~/seat_layout_app
   cd ~/seat_layout_app
   ```

3. **创建虚拟环境**（推荐）：
   ```bash
   python3 -m venv venv
   source venv/bin/activate  # 激活虚拟环境
   ```

## 步骤二：获取应用代码

1. **克隆仓库**：
   ```bash
   git clone https://github.com/您的用户名/seat-layout-generator.git .
   ```
   
   或者手动上传项目文件到服务器。

## 步骤三：安装依赖

1. **安装必要的Python包**：
   ```bash
   pip install -r requirements.txt
   ```

## 步骤四：运行应用

1. **本地测试运行**：
   ```bash
   streamlit run app.py
   ```
   这将在本地启动应用，通常在http://localhost:8501

2. **设置为后台服务**（使用screen或tmux）：
   ```bash
   # 安装screen
   sudo apt install screen
   
   # 创建新的screen会话
   screen -S seat_app
   
   # 在screen会话中启动应用
   cd ~/seat_layout_app
   streamlit run app.py --server.port 8501
   
   # 按Ctrl+A然后按D来分离screen会话（应用将继续在后台运行）
   ```

3. **重新连接到screen会话**：
   ```bash
   screen -r seat_app
   ```

## 步骤五：配置公网访问

1. **如果您的服务器有公网IP**：
   - 确保防火墙允许8501端口访问
   ```bash
   sudo ufw allow 8501/tcp  # 对于使用ufw的系统
   ```
   - 使用您的公网IP访问应用：http://您的公网IP:8501

2. **使用域名和Nginx**：
   - 将您的域名解析到服务器IP
   - 配置Nginx反向代理：

   ```bash
   # 安装Nginx
   sudo apt install nginx
   
   # 创建Nginx配置文件
   sudo nano /etc/nginx/sites-available/seat_app
   ```

   添加以下内容：
   ```
   server {
       listen 80;
       server_name your-domain.com;  # 替换为您的域名
       
       location / {
           proxy_pass http://localhost:8501;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection "upgrade";
           proxy_set_header Host $host;
           proxy_cache_bypass $http_upgrade;
       }
   }
   ```

   启用配置并重启Nginx：
   ```bash
   sudo ln -s /etc/nginx/sites-available/seat_app /etc/nginx/sites-enabled/
   sudo nginx -t  # 测试配置
   sudo systemctl restart nginx
   ```

3. **添加HTTPS**（推荐）：
   ```bash
   sudo apt install certbot python3-certbot-nginx
   sudo certbot --nginx -d your-domain.com
   ```

## 步骤六：设置开机自启动（可选）

1. **创建systemd服务**：
   ```bash
   sudo nano /etc/systemd/system/seat-app.service
   ```

2. **添加以下内容**：
   ```
   [Unit]
   Description=Seat Layout Generator App
   After=network.target
   
   [Service]
   User=your-username  # 替换为您的用户名
   WorkingDirectory=/home/your-username/seat_layout_app
   ExecStart=/home/your-username/seat_layout_app/venv/bin/streamlit run app.py --server.port 8501
   Restart=always
   
   [Install]
   WantedBy=multi-user.target
   ```

3. **启用并启动服务**：
   ```bash
   sudo systemctl enable seat-app
   sudo systemctl start seat-app
   ```

## 故障排除

1. **如果应用无法启动**：
   - 检查日志：`journalctl -u seat-app`
   - 确保所有依赖都已安装
   - 确保端口未被占用：`sudo lsof -i:8501`

2. **如果无法从外部访问**：
   - 检查防火墙设置
   - 确认服务器安全组/网络ACL设置（如使用云服务器）
   - 验证Nginx配置（如使用反向代理）
