# 信息

- 版本: v 1.0
- 标题: `Nebula_doc`
- 分类: 技术文档

# 文件结构

- chapter *: 章节文件存储目录
- `book.json`: `Gitbook`文档配置文件
- `Introduction.md`: 前言
- `SUMMARY.md`: 文档目录配置文件

# 本地运行

- 环境要求: `Node.js` (v4.0.0及以上)

- 安装`Gitbook`(`NPM`安装)

```shell
npm install gitbook-cli -g
```
其中`gitbook-cli`是`Gitbook`的一个命令行工具, 通过它可以在电脑上安装和管理`Gitbook`的多个版本.

- `clone`代码并运行
  
```shell
git clone https://github.com/threathunterX/nebula_doc.git
cd nebula_doc
gitbook install
gitbook serve
```
  
- 打开浏览器中通过 http://localhost:4000/ 进行访问




