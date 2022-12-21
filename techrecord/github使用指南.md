# 1.简单流程
- 在github上根据提示创建库
- 在本地使用以下命令进行提交
```bash
$ git init
$ git add .
$ git commit -m "first commit"
$ git remote add xxxxxx_github https://github.com/redblue0216/xxxxx.git
$ git pull xxxxxx_github remote_branch:local_branch
$ (git fetch------git merge)
$ gti push -u xxxxxx_github local_branch:remote_branch
```

- 项目结构保持Doc，Package，static，LICENSE，README五个基础方面。
   - Doc包括设计UML，API文档和官方说明文档
   - Package为项目源代码打包
   - static为项目标识，如项目Logo和Shield生成的标识块
   - LICENSE为各种开源协议
   - README为官方公开文档，内容和官方说明文档类似
# 2.Assets使用

- 使用release创建并编辑一个tag，并以此tag作为新建资源的tag，attach一个附件，并以此作为一个版本的资产，用于相关打包管理。
