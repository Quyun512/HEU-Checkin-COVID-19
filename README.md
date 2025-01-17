<h1 align="center">HEU 平安行动自动打卡</h1>

<div align="center">HEU-Checkin-COVID-19</div></br>


为 HEU 宅家人士每日平安行动打卡提供便利的小项目，使用前请需要确认自己不会到处瞎跑、不会危害社会 ~~，会一点点使用浏览器的技巧~~ 。部署方式说明：Server 版用于部署在服务器或本地，需要自行设置定时任务， GitHub Actions 版完全依赖 GitHub 服务，只需要一个可操作 GitHub 的浏览器。

## 准备

> ~~Ruby 版本需要自行测试，我还没测试成功 >.<，也不知道会不会成功。别人都是在尝试复现错误，只有我在尝试复现成功。切换到 ruby-test 分支查看 Ruby 版测试代码~~ 喜报，Ruby 代码 GitHub Actions 测试通过。

签到脚本有两种代码实现：Python by [ZJW](https://zjw1.top/2020/03/10/auto_checkin_during_covid19_and_cas_sso_learning/) / Ruby by [XYenon](https://gist.github.com/XYenon/79317d63e7f769e5bdff5b595d709b65)，可能需要的数据包括：教务处学生账号、教务处密码、平安行动表单 `BoundFields` 、平安行动表单 `FormData` 。Python 版本四项数据都需要，而 Ruby 版本只需要账号密码。

关于后两项的获取，需要电脑端浏览器 + 一点点使用浏览器的技巧，具体操作可参考《[获取 form Data](https://monsterx.cn/tech/Auto-Checkin-COVID19.html#toc_1)》中的图片：

1. 在进入平安行动打卡界面后按下 `F12` 调出审查工具，选中 Network / 网络 项
2. **确认表单数据正确** 后提交表单，之后会弹出“办理成功！”的字样
3. 不要关闭页面，这时审查工具中最下方会多出一个 `doAction` 的项目，点击它
4. 在它的 `Header` 中查看 `Form Data` 即可找到本项目需要的 `BoundFields` 和 `FormData`

获取了需要的数据后即可开始部署自己的自动打卡任务，无论你选择下面的哪一种方式，**请在运行前务必修改并核实自己的登录用户、表单数据（、SMTP 发信邮箱）！** 实际这个项目部署起来并没有你想的那么麻烦，让我们开始吧！

## Server 版

经过我简单的测试和修改之后，该项目用于服务器部署的完整代码已存放于 Server 目录下。

<details><summary>Python 版部署参考</summary><br>

使用前确保本地安装好 Python3.x 。

1. 按照自己需要修改文件内容
   
   Server/checkin.py 修改 Line26-29 Line167-174 为自己的登录用户、表单数据、SMTP 发信邮箱

2. SMTP 发信邮箱设置并不是必须的，仅用于服务器打卡完成后发送邮件提醒
   
   如果不需要邮件提醒，删除 Server/checkin.py Line162-188 即可

3. 安装 pip 依赖 `lxml` `requests`
   
   命令行执行 `python -m pip install lxml requests`

4. **运行前务必修改并核实自己的登录用户、表单数据、SMTP 发信邮箱！**
5. 配置打卡定时任务
   
   Server 版本通过服务器或本地主机的定时任务实现自动打卡。请参考《[定时任务](https://monsterx.cn/tech/Auto-Checkin-COVID19.html#toc_7)》自行设置定时任务

</details>

<details><summary>Ruby 版部署参考</summary><br>

使用前确保本地安装好 Ruby 2.5+ 。

1. 按照自己需要修改文件内容
   
   Server/checkin.rb 修改 Line15-16 为自己的登录用户

2. 安装 gem 依赖 `watir` `webdrivers`
   
   命令行执行 `gem install watir webdrivers`

3. 部分场景可能需要另行安装 chromedriver、 google-chrome 等
4. **运行前务必修改并核实自己的登录用户、表单数据、SMTP 发信邮箱！**
5. 配置打卡定时任务
   
   Server 版本通过服务器或本地主机的定时任务实现自动打卡。请参考《[定时任务](https://monsterx.cn/tech/Auto-Checkin-COVID19.html#toc_7)》自行设置定时任务

</details>

## GitHub Actions 版

为提升广大 HEU 无服务器玩家假期体验，本仓库着手实现基于 GitHub Actions 的自动打卡。灵感源自一个使用 GitHub Actions 刷 API 调用次数帮助 Microsoft 365 E5 订阅自动续期的仓库 [wangziyingwen/AutoApiSecret](https://github.com/wangziyingwen/AutoApiSecret) 。

部署 GitHub Actions 前你需要确认自己选择的代码版本。具体部署步骤请参考：

1. Fork 本仓库
2. 在仓库的 Settings 中添加 Secrets（在 [这里](https://github.com/monsterxcn/HEU-Checkin-COVID-19/settings/secrets) 按版本需要添加，**注意不要加引号** ）
   
   | Name | Value | Version |
   |:----:|:------|:-------:|
   | SECRET_ID | 2020XXXXXX | py rb |
   | SECRET_PASS | PASSWORD | py rb |
   | SECRET_BOUND | fieldCXXXdqszdjtx,......,fieldMQJCRlxfs | py |
   | SECRET_DATA | {"_VAR_EXECUTE_INDEP_ORGANIZE_Name":"XXX学院",......,"_VAR_ENTRY_TAGS":"生活服务"} | py |

3. 禁用 .github/workerflows 文件夹下不需要的一个 .yml 文件（修改 yml 后缀或直接删除）
4. **运行前务必修改并核实自己的登录用户、表单数据！**
5. 给自己的仓库点个 Star 等待 1 分钟（激活 GitHub Actions）
6. 查看 GitHub Actions 状态（[这里](https://github.com/monsterxcn/HEU-Checkin-COVID-19/actions)）
7. 查看仓库内新生成的 checkin.log 文件检查打卡执行情况

检查成功后 GitHub Actions `on.schedule` 将使打卡任务在每天指定时刻运行（本项目设定时间为 7:00）。你可以根据自己需要在 .github/workerflows 文件夹下 .yml 文件 Line11 修改打卡执行时间，严格按照 POSIX cron 语法填写，具体说明请参考《[触发工作流程的事件 安排的事件：schedule](https://docs.github.com/cn/actions/reference/events-that-trigger-workflows#)》。务必留意这里使用 Coordinated Universal Time (UTC)，北京时间 2020-06-30 07:00 转换为 UTC 时间是 2020-06-29 23:00。

> Github Actions 打卡脚本在 Server 版基础上进行了部分修改，为便于部署删去了邮件提醒功能。你可以从 GitHub Settings 中开启工作流运行提醒邮件：点 [这里](https://github.com/settings/notifications) **取消勾选** Send notifications for failed workflows only 即可。值得注意的一点是 GitHub Actions 邮件仅显示工作流运行状态，并不体现打卡是否成功，选择哪种部署方式请自行权衡。

## 注意

 - 本项目使用的 Python 版本为 3.x。已知 Python 2.x 会出现错误，不予修复
 - 如需要停止 GitHub Action 每日自动打卡，请删除仓库或删除 .github/workerflows 文件夹下 .yml 文件
 - 使用本项目因操作不当导致的平安行动打卡错误责任自负（比如你不检查表单数据提交了别的同学的数据到自己的平安行动中）
 - 如果不善动手，那么我推荐使用 [腐败街](https://www.fubaijie.cn) 提供的定时打卡
 - Licence & Author: [ZJW](https://zjw1.top) | [XYenon](https://xyenon.bid) | MIT @ [Monst.x](https://monsterx.cn)