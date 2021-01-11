# 不断并自动检查是否存在易受攻击的依赖关系

### 一段解释

由于开发方便及开发效率高，大多数的Node.js应用严重依赖大量来着npm和Yarn（这两者都是非常流行的包注册管理工具）的第三方模块。但是, 便利的缺点是将未知漏洞包含到应用程序中的安全风险, 这是可以通过owasp最严重的web应用程序安全风险列表中的排名所认识到的风险。

有许多工具可用于帮助鉴别Node.js中的第三方包，及已被社区确定为易受攻击的js应用程序, 以降低将其引入项目的风险。这些工具可以定期通过cli工具使用, 也可以作为应用程序构建过程的一个环节包括在内。

### 内容列表

- [NPM audit](#npm-audit)
- [Snyk](#snyk)
- [Greenkeeper](#greenkeeper)

### NPM Audit

`npm audit`时随着NPM@6引入的一个新的cli工具。 

运行`npm audit`将生成安全漏洞的报告, 其中包含受影响的包名称、漏洞严重性和描述、路径和其他信息, 以及应用补丁程序来解决漏洞的命令(如果可用)。

![npm audit示例](/assets/images/npm-audit.png)

🔗 [Read on: NPM blog](https://docs.npmjs.com/getting-started/running-a-security-audit)

### Snyk

Snyk提供了功能丰富的cli, 并集成Github。除了通知漏洞外, Snyk更进一步, 针对已知漏洞发布了的修补程序, 还会自动创建新的pull requests, 以修复漏洞。

Snyk的网站功能丰富, 它还允许提供对github存储库或npm模块url的依赖关系进行临时评估。您还可以直接搜索有漏洞的npm包。

Synk的Github集成自动创建pull request的示例：
![synk GitHub example](/assets/images/snyk.png)

🔗 [Read on: Snyk website](https://snyk.io/)

🔗 [Read on: Synk online tool to check npm packages and GitHub modules](https://snyk.io/test)

### Greenkeeper

Greenkeeper是一种提供实时依赖关系更新的服务, 通过始终使用到目前为止更新最多的依赖版本来保持应用程序的安全性。

Greenkeeper监视存储库包中`package.json`文件指定的npm依赖项, 并对每个依赖项的更新，自动创建一个工作分支。然后运行仓库CI套件, 以揭示应用程序中更新的依赖项版本的任何重大更改。如果CI由于依赖项的更新而失败, 则会在相应的存储库中创建一个清晰简洁的问题, 概述当前的和更新的包版本，以及更新版本的提交历史信息和记录。

Greenkeeper的Github集成自动创建pull request的示例：

![synk github example](/assets/images/greenkeeper.png)
🔗 [Read on: Greenkeeper website](https://greenkeeper.io/)

### 额外资源

🔗 [Rising Stack Blog: Node.js dependency risks](https://blog.risingstack.com/controlling-node-js-security-risk-npm-dependencies/)

🔗 [NodeSource Blog: Improving npm security](https://nodesource.com/blog/how-to-reduce-risk-and-improve-security-around-npm)
