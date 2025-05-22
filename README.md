# 座位布局生成器

一个用于生成带颜色标签的座位布局Excel文件的Web应用。

## 功能特点

- 上传Excel座位数据和JS布局文件
- 自动将Excel数据映射到JS布局中
- 生成带有颜色标签的Excel文件
- 提供分区颜色图例和数据统计

## 使用方法

1. 上传Excel座位数据文件（包含座位号、分区和名称）
2. 上传JS布局文件（包含座位布局的JSON数据）
3. 设置输出文件名
4. 点击"生成座位布局"按钮
5. 下载生成的Excel文件

## 安装与部署

### 本地运行

```bash
# 克隆仓库
git clone https://github.com/您的用户名/seat-layout-generator.git
cd seat-layout-generator

# 安装依赖
pip install -r requirements.txt

# 运行应用
streamlit run app.py
```

### 服务器部署

请参见DEPLOY.md文件获取详细的服务器部署指南。

## 文件格式要求

### Excel座位数据文件
- 第一列：座位号（格式为"行-列"，如"1-5"）
- 第二列：分区名称
- 第三列：学校或单位名称

### JS布局文件
- 必须是有效的JSON格式
- 每个座位对象应包含row、col、groupName和color属性

## 贡献

欢迎提交问题和改进建议！

## 许可

MIT
