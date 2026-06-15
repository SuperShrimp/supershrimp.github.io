# Run Commands

本文件记录在 Windows 上调试这个 Jekyll / GitHub Pages 博客常用的命令。

## 1. 检查本机环境

```powershell
git --version
ruby --version
bundle --version
node --version
npm --version
docker --version
```

如果 PowerShell 执行 `npm --version` 时提示 `npm.ps1` 被 Execution Policy 阻止，先用这个命令确认 npm 本身是否可用：

```powershell
npm.cmd --version
```

只在当前 PowerShell 窗口临时放行脚本：

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
npm --version
```

对当前用户长期放行本地脚本和已签名远程脚本：

```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
npm --version
```

查看当前 PowerShell 执行策略：

```powershell
Get-ExecutionPolicy -List
```

当前仓库核心依赖：

- Ruby / Bundler: 运行 Jekyll 本地预览
- Node / npm: 构建压缩前端 JS
- Docker: 可选，用容器运行站点

## 2. 首次配置 Ruby 依赖

安装 RubyInstaller + DevKit 后，在仓库根目录运行：

```powershell
gem install bundler -v 2.4.20
bundle config set --local path vendor/bundle
bundle lock --add-platform x64-mingw-ucrt
bundle install
```

如果 `bundle install` 报平台或原生扩展相关错误，先确认 Ruby 是带 DevKit 的版本：

```powershell
ruby -v
ridk version
ridk install
```

然后重新运行：

```powershell
bundle install
```

## 3. 首次配置 Node 依赖

只有在需要重新生成 `assets/js/main.min.js` 时才需要：

```powershell
npm install
```

## 4. 本地启动博客

推荐用 Bundler 固定 Gem 版本：

```powershell
bundle exec jekyll serve -l -H localhost
```

浏览器打开：

```text
http://localhost:4000
```

如果需要看到更详细的错误堆栈：

```powershell
bundle exec jekyll serve -l -H localhost --trace
```

如果改了 `_config.yml`，需要停止后重新启动：

```powershell
Ctrl+C
bundle exec jekyll serve -l -H localhost
```

## 5. 只构建不启动服务

用于检查站点是否能完整生成：

```powershell
bundle exec jekyll build --trace
```

构建产物会输出到：

```text
_site/
```

## 6. 重新构建前端 JS

```powershell
npm run build:js
```

监听 JS 文件变化并自动构建：

```powershell
npm run watch:js
```

## 7. Docker 调试方式

如果安装了 Docker Desktop，可以不用本机 Ruby，直接运行：

```powershell
docker compose up
```

重新构建镜像：

```powershell
docker compose build --no-cache
docker compose up
```

停止容器：

```powershell
docker compose down
```

查看容器日志：

```powershell
docker compose logs -f
```

## 8. 常见排错命令

清理本地 Jekyll 构建缓存：

```powershell
bundle exec jekyll clean
```

删除 Bundler 本地安装目录后重装：

```powershell
Remove-Item -Recurse -Force vendor
Remove-Item -Recurse -Force .bundle
bundle config set --local path vendor/bundle
bundle install
```

检查 Gemfile 依赖状态：

```powershell
bundle check
bundle list
```

查看 Jekyll 版本：

```powershell
bundle exec jekyll --version
```

如果 `bundle install` 卡在 `rdiscount (2.1.7)`：

```powershell
ruby --version
bundle --version
gem --version
ridk version
```

如果失败日志里出现类似路径：

```text
vendor/bundle/ruby/4.0.0/gems/rdiscount-2.1.7/ext
```

并且编译错误包含：

```text
error: return type defaults to 'int'
error: implicit declaration of function
```

说明当前使用的是 Ruby 4.x，而这个 Jekyll / GitHub Pages 依赖栈里有旧的 C 扩展不兼容。推荐改用 RubyInstaller 3.2.x + DevKit，和仓库里的 Dockerfile 保持一致。

切换到 Ruby 3.2 后，重新安装依赖：

```powershell
ruby --version
where.exe ruby
where.exe bundle
gem install bundler -v 2.4.20
Remove-Item -Recurse -Force vendor
Remove-Item -Recurse -Force .bundle
bundle config set --local path vendor/bundle
bundle lock --add-platform x64-mingw-ucrt
bundle install
```

先确认本仓库没有主动依赖 `rdiscount`：

```powershell
Select-String -Path Gemfile,Gemfile.lock,_config.yml -Pattern "rdiscount|redcarpet|markdown"
```

如果 `Gemfile.lock` 只有 Linux 平台，给当前 Windows Ruby 平台补锁：

```powershell
bundle lock --add-platform x64-mingw-ucrt
bundle install
```

如果仍然失败，保存完整安装日志：

```powershell
bundle install --verbose *> bundle-install.log
Get-Content -LiteralPath bundle-install.log -Tail 120
```

如果运行 Jekyll 时出现：

```text
TZInfo::DataSources::ZoneinfoDirectoryNotFound
None of the paths included in TZInfo::DataSources::ZoneinfoDataSource.search_path are valid zoneinfo directories.
```

这是 Windows 缺少 Unix `zoneinfo` 数据库导致的。确认 `Gemfile` 包含：

```ruby
gem 'tzinfo-data'
```

然后重新安装依赖：

```powershell
bundle install
bundle exec jekyll serve -l -H localhost
```

查看端口 4000 是否被占用：

```powershell
netstat -ano | findstr :4000
```

如果端口被占用，换一个端口启动：

```powershell
bundle exec jekyll serve -l -H localhost -P 4001
```

## 9. GitHub Pages 相关检查

查看当前远程仓库：

```powershell
git remote -v
```

查看当前分支和工作区状态：

```powershell
git status
```

提交并推送修改：

```powershell
git add .
git commit -m "Update blog"
git push
```
