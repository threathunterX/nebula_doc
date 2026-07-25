> # ⚠️ 本仓库已归档 —— Nebula 1.x 历史版本
>
> **本项目已停止维护,请勿用于生产环境。**
>
> Nebula 1.x 于 2019 年开源,此后代码未再更新。其依赖的技术栈(Python 2、
> OpenResty 1.11、Esper 6、jackson 1.9、commons-collections 3.2.1 等)均已
> 停止支持并存在已知漏洞,其中 commons-collections 3.2.1 含经典反序列化利用链。
>
> ### 👉 新版本:[Nebula 2.0](https://github.com/threathunterX/nebula2)
>
> 2.0 基于 Flink 重写,继承了 1.x 的全部风控领域资产(17 个事件模型、
> 253 个统计变量、170 条策略模板),并解决了 1.x 的架构与安全问题。
> 目前处于开发阶段,进展见新仓库。
>
> ### 关于本仓库的当前状态
>
> - 已清理历史遗留的凭据、内部地址与示例数据中的第三方信息;配置中的口令
>   已替换为 `CHANGE_ME_*` 占位符
> - 仓库保留公开,仅作历史存档与设计参考
> - **注意**:清理仅作用于当前代码,git 历史中仍保留原始内容;此前 fork
>   出去的副本亦不受影响
>
> ### 文档可读性说明
>
> 本文档的大部分截图外链在微博图床(sinaimg.cn),该图床启用防盗链后图片
> 已无法显示。文字内容仍然完整可读。2.0 的文档全部使用仓库内图片。

---

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




