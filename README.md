# 信息

- 版本: v 1.0
- 标题: `Nebula_doc`
- 分类: 技术文档
- 在线发布地址: [Nebula](http://112.74.58.210:8080)

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
git clone git@lab.threathunter.cn:Nebula/Nebula_doc.git
cd Nebula_doc
gitbook install
gitbook serve
```
  
- 打开浏览器中通过 http://localhost:4000/ 进行访问


# 文档修改发布流程

##  修改流程

- 将文档源文件`clone`下来, 直接编辑源文件进行修改提交.

- 在文档阅读界面左上角直接修改提交.

##  更新发布流程

1. 进入服务器`112.74.58.210` /data/nebula/doc目录.

2. 运行`build.sh`脚本生成文档`html`文件.

3. 进入`script`目录, 运行`run.sh`脚本即可.
4. 浏览器打开[Nebula](http://112.74.58.210:8080)查看更改.


