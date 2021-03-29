# Git

## 基本设置

```bash
# 关于换行符的设置

git config --global core.autocrlf true  # 设置true后，提交时自动转为LF，签出时自动转为crlf
git config --global core.autocrlf input # 提交时自动转为LF，签出时不自动转为CRLF
git config --global core.autocrlf false # 提交和签出时保留crlf换行

# 关于密码的设置
git config --global credential.helper cache # 设置记住密码（默认15分钟）：
git config --global credential.helper 'cache --timeout=3600' # 如果想自己设置时间，以秒为单位：
git credential-cache exit # 守护程序提前退出，请在超时之前忘记所有缓存的凭据
git config --global credential.helper store # 长期存储密码：

# 取消存储密码
git config --unset-all credential.helper
git config --global --unset-all credential.helper
git config --system --unset-all credential.helper

# 关于代理的设置

# http和https协议设置socks5代理
git config --global http.proxy 'socks5h://127.0.0.1:1080'   # socks5, h表示包括域名解析
git config --global http.proxy http://127.0.0.1:108 # 设置http和https代理
git config --global --unset http.proxy  # 取消代理

git config --global http.https://github.com.proxy socks5://127.0.0.1:1080   # 只对github.com设置代理
git config --global --unset http.https://github.com.proxy  # 取消对github.com的代理

# ssh协议设置代理
# Linux修改~/.ssh/config文件，添加如下内容
Host github.com
User git
ProxyCommand nc -v -x 127.0.0.1:1080 %h %p
# Windows在用户目录下修改.ssh/config文件，注意要安装gitforwindows并设置环境变量
Host github.com
User git
ProxyCommand connect -S 127.0.0.1:1080 %h %p

git config --list   # 罗列当前配置
git config -e [--global]    # 编辑配置

```

## 基本使用流程

```bash
# 安装后设置用户名和邮箱：
git config --global user.name "Your Name"
git config --global user.email "email@example.com"

git init    # 初始化仓库

git clone --depth 1 # 克隆目录深度为1

git checkout master # 获取主干master代码
git checkout -b myfeature # 新建一个开发分支myfeature

# 提交分支
git add --all # 从git2.0后all参数是git add的默认参数
git add -p  # 增加每个变化到缓存区时都要求确认
git remore add origin [远程仓库路径]

git commit --vervose # 参数vervose会列出diff的结果,等同于-v
git commit -a --allow-empty-message -m "" # 以空消息commit，即不写commit message
git commit --amend -m message   # 重做上一次提交信息

# 与主干同步-分支开发过程应经常与主干保持同步
git fetch origin
git rebase origin/master

git rebase -i origin/master # 合并commit,分支开发后，可能很多commit,-i参数表示互动(interactive)
git push --force origin myfeature   # 推送分支至远程仓库
git remote set-url origin URL # 更换远程仓库地址，URL为新地址
git remote -v   # 查看远程仓库的地址
git rm -r --cached dirfile # 删除远程目录或文件
git branch  # 列出所有本地分支
git branch -r   # 列出所有远程分支

# 删除远程分支
git push origin --delete [branch-name]
git branch -dr [remote/branch]

git tag # 列出所有tag标签
git tag -d [tag]    # 删除本地tag
git show tag    # 显示tag信息


```
