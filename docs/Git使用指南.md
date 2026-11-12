# Git 使用指南（详细版）

> 面向：刚装好 Git、只想安稳地把代码和笔记传上 GitHub 的人。
> 环境：Windows 10/11 · Git 2.56 · GitHub Desktop · 仓库 `D:\代码\my-learning-repo`

这份文档比纸质版更长，因为把「为什么」也写进来了。看不懂命令时不用背，先看懂它在干什么，命令自然就记住了。

---

## 目录

- [0. 先搞清楚：Git 到底在干什么](#0-先搞清楚git-到底在干什么)
- [1. 四个位置，背下来](#1-四个位置背下来)
- [2. 装好之后先做一次体检](#2-装好之后先做一次体检)
- [3. 路径：新手最容易卡死的一关](#3-路径新手最容易卡死的一关)
- [4. 五条命令，撑起一个学期](#4-五条命令撑起一个学期)
- [5. 一次完整的提交流程](#5-一次完整的提交流程)
- [6. 提交信息：写给三个月后的自己](#6-提交信息写给三个月后的自己)
- [7. 报错急救手册](#7-报错急救手册)
- [8. 后悔药](#8-后悔药)
- [9. 分支：先知道是什么就行](#9-分支先知道是什么就行)
- [10. 用 GitHub Desktop 的话](#10-用-github-desktop-的话)
- [11. 把 Git 变成习惯](#11-把-git-变成习惯)
- [12. 速查表](#12-速查表)
- [13. 名词对照](#13-名词对照)
- [附录 A：换电脑、多设备怎么办](#附录-a换电脑多设备怎么办)
- [附录 B：哪些东西绝对不要提交](#附录-b哪些东西绝对不要提交)
- [附录 C：常见问题](#附录-c常见问题)

---

## 0. 先搞清楚：Git 到底在干什么

### 没有 Git 的时候，我们是怎么备份的

大概是这样的：

```
作业/
├── 作业_最终版.doc
├── 作业_最终版2.doc
├── 作业_最终版_真的最后一版.doc
└── 作业_最终版_老师改完.doc
```

问题有三个：

1. 分不清哪版是对的，磁盘上躺着一堆可能更差的版本；
2. 想知道「我昨天把哪句话删了」，做不到；
3. 换台电脑就得用 U 盘拷，拷来拷去总会覆盖错。

### Git 干的三件事

| 你想要的 | Git 的做法 |
|---|---|
| 每次改动都留个档，还能写一句说明 | `commit`（提交） |
| 随时回到任何一个存档点 | `git log` 看历史，`git checkout` 回去 |
| 换电脑、多人协作都能同步 | `push` / `pull`（推到 GitHub / 从 GitHub 拉） |

所以准确地说，**Git 不是备份工具，是「带时间机器的版本记录本」**。文件还是那些文件，只是每次你按下「提交」，它就替你在历史里记一笔：这一刻的代码长什么样，你当时想干什么。

### 一个必须先扭过来的观念

> **保存文件 ≠ 提交。**

在 Typora 里按 Ctrl+S，或者 VS Code 按 Ctrl+S，那只是「把内存里的内容写到硬盘上」。Git 完全不知道你干了什么，因为对 Git 来说文件只是变了，没有说明、没有记录点。

要让 Git 记下来，必须**显式地提交**：

```bash
git add .                    # 把改动装进箱子
git commit -m "改了说明"      # 封箱、贴标签
```

这两步都得做，少一步都不算提交。新手 90% 的「我明明改了怎么没上去」，都是只按了 Ctrl+S，没做这两步。

---

## 1. 四个位置，背下来

Git 让人发懵，是因为它在本地凭空多出了两个「中间层」。搞清这四个位置各在哪，后面的命令就都有归宿了。

| 位置 | 正式叫法 | 生活比喻 | 怎么进去 |
|---|---|---|---|
| 你正在编辑的文件夹 | 工作区（Working Tree） | 书桌，摊着正在改的稿子 | 打开文件夹就是 |
| `git add` 之后的地方 | 暂存区（Index / Staging） | 待寄的纸箱，先放进去还没封口 | `git add` |
| `git commit` 之后的地方 | 本地仓库（Local Repo） | 封好箱、贴了标签、垛在墙边 | `git commit` |
| `git push` 之后的地方 | 远程仓库（Remote） | 快递寄出去了，GitHub 上能看见 | `git push` |

### 一句话记住顺序

```
改文件 → git add → git commit → git push
工作区    暂存区      本地仓库     远程仓库
```

### 为什么非要有个「暂存区」

多这一层不是故意难为人。举一个真实的例子。

今天你干了两件事：

- 把 `README.md` 的错别字改了；
- 新写了一个 `code/02-c-exercises/` 的三道练习题。

理想情况下，这应该记成**两个**提交，因为它们是两件事。三个月后你想回看「当时那道成绩统计题怎么写的」，直接翻第二个提交就行，不用在「改错别字」的提交里翻。

暂存区就是给你这个机会的：

```bash
git add README.md                 # 只把改错别字这件事装箱
git commit -m "docs: 修正 README 错别字"

git add code/02-c-exercises/      # 再把练习题装箱
git commit -m "完成C语言作业1：学生成绩统计"
```

如果嫌麻烦，直接 `git add .` 一次性全提交也没问题——新手阶段完全可以这么干，知道有这个能力就行。

### 常见的三个误解

| 误解 | 实际 |
|---|---|
| 「commit 就是保存文件」 | 文件早就保存了。commit 是给这一刻拍快照并写说明 |
| 「push 是把文件传上去」 | 是把**提交**传上去。没 commit 就没有东西可 push |
| 「删掉本地文件，GitHub 上也没了」 | 不会。删了还得 add + commit + push 才会同步 |

---

## 2. 装好之后先做一次体检

### 确认 Git 活着

```bash
git --version
```

预期输出（版本号会是别的数字，正常）：

```
git version 2.56.0.windows.4
```

**如果报 `git: command not found`**：

1. 先关掉这个终端窗口，重新开一个（装完 Git 后老窗口不认 PATH）；
2. 还不行就是安装时没勾「把 Git 加进 PATH」，重装一次，或手动把 `D:\APP\Git\cmd` 加进系统环境变量 PATH。

### 确认身份配置

```bash
git config --global --list
```

预期能看到至少这两行：

```
user.name=bridgerzhou-max
user.email=bridgerzhou@gmail.com
```

**没有的话**，手动补上（把值换成你自己的）：

```bash
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的邮箱"
```

Git 每次提交都会把这两项写进历史。配错了不可怕，但如果配成一个连 GitHub 都不认识的邮箱，GitHub 就不知道这个提交属于你，你的贡献图会一直是空的。

### 一条建议顺手加上

```bash
git config --global core.quotepath false
```

不加这行，中文文件名在 `git status` 里会显示成 `"\344\273\243\347\240\201"` 这种乱码。加上之后正常显示中文。

---

## 3. 路径：新手最容易卡死的一关

Windows 用反斜杠 `\`，而 Git Bash 是 Linux 风格的 shell，两条规则完全不同。这一章吃透，后面省一半时间。

### 3.1 盘符对照

| 在资源管理器里看到的 | 在 Git Bash 里写成 |
|---|---|
| `C:\Users\mile chan` | `/c/Users/mile chan` |
| `D:\代码` | `/d/代码` |
| `D:\代码\my-learning-repo` | `/d/代码/my-learning-repo` |

规律只有两条：

1. **盘符换成 `/c`、`/d`**（小写，前面带斜杠，后面不带冒号）；
2. **所有分隔符一律用正斜杠 `/`**，不用反斜杠。

### 3.2 三种正确写法

```bash
cd /d/代码/my-learning-repo            # 最标准
cd "/d/代码/my-learning-repo"          # 路径里有空格时加引号
cd "D:/代码/my-learning-repo"          # 直接写盘符 + 正斜杠，也认
```

### 3.3 三个常见错法，逐条拆解

#### 错法一：`cd ~/D:\代码\my-learning-repo`

```
bash: cd: /c/Users/mile chan/D:代码my-learning-repo: No such file or directory
```

**错在哪**：`~` 在 bash 里是**已经代表**「你的用户目录」的缩写，等于 `C:\Users\mile chan`。你在它后面再接 `D:\...`，bash 就把两者粘成了一个根本不存在的位置。

**记住**：`~` 和盘符是平级的两种起点，不能叠在一起用。

#### 错法二：`cd D:\代码\my-learning-repo`

```
bash: cd: D:代码my-learning-repo: No such file or directory
```

**错在哪**：反斜杠在 bash 里是**转义符**，作用是「取消下一个字符的特殊含义」。所以 `\代` 被吃掉变成 `代`，`\m` 变成 `m`，路径就粘成了 `D:代码my-learning-repo`。

**记住**：在 bash 里写路径，反斜杠是禁忌。要么全用 `/`，要么用引号把整串包起来（`"D:\代码"` 在引号里也不会被转义，但为了统一，还是建议直接写 `/d/代码`）。

#### 错法三：`cd /d/代码/my-cleaning-repo`

```
bash: cd: /d/代码/my-cleaning-repo: No such file or directory
```

**错在哪**：路径格式完全正确，只是**名字错了**。仓库叫 `my-learning-repo`（learning，学习），不是 `my-cleaning-repo`（cleaning，清理）。

**记住**：`No such file or directory` 有两种可能——格式错，或者名字错。格式对的时候，就别怀疑规则了，去核对名字。

### 3.4 几个每天都会用的导航命令

| 命令 | 作用 |
|---|---|
| `pwd` | 我现在在哪（**迷路时第一反应敲它**） |
| `ls` | 这个目录里有什么 |
| `cd ..` | 上一层目录 |
| `cd ~` | 回用户目录 |
| `cd -` | 回到上一个待过的目录 |
| Tab 键 | 输入一半按 Tab 自动补全路径，**强烈建议常用** |

### 3.5 两个偷懒办法

1. **在资源管理器进到文件夹，右键选「Open Git Bash here」**，窗口一开就已经在这个目录，不用敲路径。
2. **输入 `cd ` 加一个空格，再把文件夹从资源管理器拖进窗口**，路径自动填好，而且格式是对的。

---

## 4. 五条命令，撑起一个学期

只有这五条也能过一学期。每条都讲清：干什么、什么时候敲、敲完看到什么、出问题怎么办。

### 4.1 `git status` —— 最该养成习惯的一条

**干什么**：告诉你现在有哪些改动、哪些已经装箱、哪些还没。

**什么时候敲**：每次开始干活前、每次提交前。**不确定状态就敲它。**

```bash
git status
```

正常会看到三种情况之一：

```bash
# 情况一：什么都没改
nothing to commit, working tree clean

# 情况二：改了但还没装箱
Changes not staged for commit:
        modified:   README.md

# 情况三：有新文件，Git 还不认识它
Untracked files:
        code/02-c-exercises/
```

**三种状态的含义**：

| 英文 | 中文 | 意思 |
|---|---|---|
| `Changes not staged for commit` | 改了，没装箱 | 需要 `git add` |
| `Changes to be committed` | 装了箱，没封箱 | 需要 `git commit` |
| `Untracked files` | 新文件，还没跟踪 | 需要 `git add` |
| `nothing to commit, working tree clean` | 干净 | 没东西可提交 |
| `nothing added to commit but untracked files present` | 只有新文件，没装 | 需要 `git add` |

### 4.2 `git add` —— 把改动装进箱子

**干什么**：把改动从「工作区」搬进「暂存区」。

```bash
git add .                    # 当前目录下所有改动，最常用
git add README.md            # 只加一个文件
git add code/                # 只加一个目录
git add *.md                 # 只加所有 md 文件
```

`git add .` 那个点表示「当前目录」，**注意你现在在哪个目录**——在仓库根目录敲，才是全部。

加完用 `git status --short` 确认，左边会多出一个字母：

```
M  README.md          # M = Modified，已修改并装箱
A  code/02/           # A = Added，新文件已装箱
?? notes/draft.md     # ?? = 还没装箱
```

`A` 和 `M` 在左边一列，就代表「已装箱」。

### 4.3 `git commit` —— 封箱、贴标签

**干什么**：把暂存区里的东西打成一个永久存档点，并记下你写的那句话。

```bash
git commit -m "完成C语言作业1：学生成绩统计"
```

`-m` 后面跟的就是提交信息，**必须写，而且要用引号包起来**。

成功输出：

```
[main 1a2b3c4] 完成C语言作业1：学生成绩统计
 2 files changed, 86 insertions(+)
```

拆开看：

- `main` 是分支名（新手永远在 main 上，不用管）；
- `1a2b3c4` 是这个提交的编号，前 7 位，全球唯一；
- `2 files changed, 86 insertions(+)` 表示改了 2 个文件、加了 86 行。

**如果报 `nothing to commit`**：说明没东西可提交。先 `git status` 看，八成是没 `add`，或者其实已经提交过了。

### 4.4 `git push` —— 寄出去

**干什么**：把本地仓库里的提交推到 GitHub。

```bash
git push
```

成功输出（最后一行是关键）：

```
Enumerating objects: 7, done.
Writing objects: 100% (7/7), 1.2 KiB
To https://github.com/用户名/仓库名.git
   0288714..1a2b3c4  main -> main
```

看到 `旧编号..新编号 main -> main` 就成功了。打开 GitHub 页面刷新即可。

**第一次推送**新仓库要加 `-u`，让 Git 记住对应关系：

```bash
git push -u origin main
```

之后就可以只敲 `git push`。

### 4.5 `git log` —— 看历史

**干什么**：列出所有提交，最新的在最上面。

```bash
git log --oneline
```

输出：

```
1a2b3c4 (HEAD -> main) 完成C语言作业1：学生成绩统计
0288714 docs: 上手清单里的用户名示例改为通用写法
dbb3791 docs: 新增 notes / code 目录索引
f2126a6 docs: 重写 README
4d8a670 初始化：建立学习仓库
```

`--oneline` 是「一行一个」，最常用。不加的话会显示作者、时间、完整编号，太啰嗦。

---

## 5. 一次完整的提交流程

假设今天写了三道 C 语言练习题。**照着敲，注意每一步的输出。**

### 第 0 步：确认自己在仓库里

```bash
cd /d/代码/my-learning-repo
pwd
```

```
/d/代码/my-learning-repo
```

`pwd` 输出的末尾必须是仓库目录名。不对就重新 `cd`。

### 第 1 步：看现在什么状态

```bash
git status
```

```
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
        modified:   README.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        code/02-c-exercises/
```

读到两个信息：`README.md` 改过了，`code/02-c-exercises/` 是新目录。

### 第 2 步：装进箱子

```bash
git add .
git status --short
```

```
M  README.md
A  code/02-c-exercises/
```

`M` 和 `A` 都在左边，说明都装箱了。

### 第 3 步：封箱，写清楚这一箱是什么

```bash
git commit -m "完成C语言作业1：学生成绩统计"
```

```
[main 1a2b3c4] 完成C语言作业1：学生成绩统计
 2 files changed, 86 insertions(+)
 create mode 100644 code/02-c-exercises/ex1.c
```

### 第 4 步：寄出去

```bash
git push
```

```
To https://github.com/bridgerzhou-max/my-learning-repo.git
   0288714..1a2b3c4  main -> main
```

### 第 5 步：验收

浏览器打开仓库页面，刷新。最新的提交应该出现在文件列表上方，点进去能看到你改了什么。

**没看到？** 按顺序排查：

1. `git log --oneline -1` 看本地最新提交是不是你刚写的那个；
2. `git status` 看是不是还有没提交的东西；
3. `git push` 再敲一次，看提示什么；
4. 浏览器 Ctrl+F5 强制刷新（GitHub 有缓存）。

---

## 6. 提交信息：写给三个月后的自己

### 判断标准只有一个

> 不看代码，只看这一行字，能不能想起当时在干嘛。

### 好的和坏的

| 这样写（好） | 不要这样写（差） | 差在哪 |
|---|---|---|
| 第46周笔记：弄懂了指针和数组的区别 | update | 更新了什么？不知道 |
| 完成C语言作业1：学生成绩统计 | 修改 | 改了哪个文件？哪部分？ |
| 修正上次笔记里对递归的错误理解 | 提交 | 这是动词，不是内容 |
| README 增加目录结构说明 | 123 | 三个月后你会恨自己 |
| 补上缺的 `#include <stdio.h>` | asdf | 同上 |

### 想更规范可以用前缀

团队协作时通用，个人仓库可选：

| 前缀 | 用于 | 例子 |
|---|---|---|
| `feat:` | 新增功能或新文件 | `feat: 加上成绩排序功能` |
| `fix:` | 修复错误 | `fix: 修正冒泡排序的边界判断` |
| `docs:` | 只改了文档、笔记、README | `docs: 补充环境配置说明` |
| `style:` | 只调格式，不影响逻辑 | `style: 统一缩进为 4 空格` |
| `refactor:` | 重写代码，功能不变 | `refactor: 把重复的输入逻辑抽成函数` |
| `chore:` | 杂事，比如改 .gitignore | `chore: 忽略 .exe 编译产物` |

### 一条更重要的建议：一次提交只说一件事

反面例子：

```
fix: 改错别字、加了三道题、更新了 README、删了旧文件
```

这种提交没法回退——你想撤销「加三道题」，会把改错别字也一起撤了。

拆成四个提交，每个一行字，以后每一条都能单独看懂。

### 一个真实的反例

刚上手时很容易写出这种说明：

```
uptodate
```

当时自己看得懂，三个月后再看只能猜——是文件 up to date 了？分支 up to date 了？还是整个仓库？

同一件事，换个写法就清楚了：

| 当时的写法 | 更好的写法 |
|---|---|
| `uptodate` | `更新技能现状表：C 语言 → C++` |
| `update` | `docs: 补充 Git 指南第 6 章` |
| `1` | `fix: 修正 hello world 的输出` |

规律只有一句：**写「改了什么」，不要写「我做完了」。**

### 已经 push 出去了，说明还能改吗

| 情况 | 能改吗 | 怎么做 |
|---|---|---|
| 刚 commit，还没 push | ✅ 能 | `git commit --amend -m "新说明"` |
| 已经 push 出去 | ❌ 别改 | 历史推上去后再改就需要 force push，风险大于收益 |

第二条要记住：**提交说明写得不好，不影响代码，看得懂就行**；为了一个措辞去 force push，反而可能把仓库搞坏。

---

## 7. 报错急救手册

### 通用原则

遇到不认识的报错：

1. **先把整段英文原样复制去搜**，不要只挑几个词。Git 的报错文字非常标准化，一搜一个准。
2. **第一行通常写原因，最后一行通常写下一步该干什么**。中间那些 `hint:` 是提示，`error:` 才是问题。
3. 不要凭感觉乱敲命令，尤其不要乱敲带 `--force` 的——那会真的丢数据。

下面按「新手最常遇到」排序。

---

### 报错 1：`fatal: not a git repository (or any of the parent directories): .git`

**它在说什么**：你现在不在一个 Git 仓库里。

**怎么办**：

```bash
pwd                       # 看自己在哪
cd /d/代码/my-learning-repo   # 进到仓库目录
git status                # 再试
```

如果确实是新文件夹，需要先 `git init`（但通常你不是要这个，而是进错了目录）。

---

### 报错 2：`git: command not found`

**它在说什么**：这个终端不认识 `git` 这个词。

**怎么办**：

1. 关掉终端重新开一个；
2. 还不行，检查 `D:\APP\Git\cmd` 是否在系统 PATH 里；
3. 临时应急可以直接用全路径：`"D:/APP/Git/cmd/git.exe" status`。

---

### 报错 3：`cd: ...: No such file or directory`

**它在说什么**：这个路径不存在。

**怎么办**：见第 3 章。九成是三种情况之一：用了 `~` 又接盘符、反斜杠被吃掉、名字打错。

---

### 报错 4：`remote: Support for password authentication was removed on August 13, 2021.` 或 `Authentication failed`

**它在说什么**：GitHub 早就不能用「账号 + 密码」推送代码了。

**怎么办**（三选一）：

1. **用 GitHub Desktop**（最省事）：在 Desktop 里操作一次推送，它会把凭证存进系统，之后命令行也能用了；
2. **配置 Personal Access Token**：GitHub 网页 → Settings → Developer settings → Personal access tokens → 生成一个带 `repo` 权限的 token，推送时密码位置粘贴 token；
3. 改用 SSH 密钥（更彻底，但要配一次）。

---

### 报错 5：`*** Please tell me who you are.` / `fatal: unable to auto-detect email address`

**它在说什么**：没配用户名邮箱，Git 不知道该把这个提交算在谁头上。

**怎么办**：

```bash
git config --global user.name "你的GitHub用户名"
git config --global user.email "你注册GitHub用的邮箱"
```

---

### 报错 6：`nothing to commit, working tree clean`

**它在说什么**：没有东西可以提交。

**这通常不是错误**，而是提醒。检查三件事：

1. 文件**真的保存了吗**（编辑器里按了 Ctrl+S 吗）；
2. `git status` 是不是显示有改动——如果没有，说明 Git 根本没看到变化；
3. 是不是其实已经提交过了（`git log --oneline -3` 看看）。

---

### 报错 7：`! [rejected] main -> main (fetch first)` / `failed to push some refs`

**它在说什么**：GitHub 上有你本地没有的提交，Git 不敢直接覆盖。

常见原因：你在网页上直接改了文件，或者换了台电脑提交过。

**怎么办**：

```bash
git pull --rebase      # 先把线上的拉下来，接到你本地提交的前面
git push               # 再推
```

`--rebase` 的意思是「把我的提交挪到最新的后面」，比直接 `git pull`（会产生一个难看的合并提交）干净。

如果 `pull` 时报冲突，说明同一个文件的同一处两边都改了，需要手动打开文件把 `<<<<<<<` 那几行改掉，然后 `git add` → `git commit`。

---

### 报错 8：`fatal: refusing to merge unrelated histories`

**它在说什么**：你本地和线上的历史毫无关系（比如本地 `git init` 过、线上又建了带 README 的仓库），Git 不敢合并。

**怎么办**：

```bash
git pull origin main --allow-unrelated-histories
```

如果两边都有 README.md，会冲突，手动留一个版本即可。

---

### 报错 9：`error: src refspec main does not match any`

**它在说什么**：你本地还没有任何提交，或者分支名不是 main。

**怎么办**：

```bash
git log --oneline          # 确认有没有提交
git commit -m "第一次提交"  # 没有就提交一次
git branch --show-current  # 看当前分支名，可能是 master
```

如果分支叫 `master` 而不是 `main`：

```bash
git branch -M main         # 改名成 main
```

---

### 报错 10：`warning: LF will be replaced by CRLF in xxx`

**它在说什么**：换行符转换的提示。Windows 用 CRLF，Linux 用 LF，Git 在两者之间自动换算。

**怎么办**：**这是警告不是错误，可以完全不管。** 提交照样成功。

---

### 报错 11：`Permission denied (publickey)`

**它在说什么**：你在用 SSH 方式连接，但没有可用的密钥。

**怎么办**：新手不要用 SSH，改用 HTTPS 地址：

```bash
git remote -v                              # 看现在是什么地址
git remote set-url origin https://github.com/用户名/仓库名.git
```

---

### 报错 12：弹出 `git-credential-manager.exe - 系统错误：字符串绑定无效`／push 时反复要求浏览器登录

**它在说什么**：`git-credential-manager.exe`（Git 的凭据管理器，简称 GCM）在把登录令牌交给 Git 的那一刻自己崩了一下。「字符串绑定无效」是 .NET 程序的底层报错（对应 Windows 的 "The parameter is incorrect"），和你的命令写得对不对没有关系。

**先别慌：大概率已经成功了。**

按这个顺序确认：

```bash
git status              # 出现 "Your branch is up to date with 'origin/main'" 就是推上去了
git log --oneline -1    # 看最新提交是不是你刚写的那条
```

本地和远程一致，就说明这个弹窗只是「报错噪音」，推送其实完成了。

**怎么办**：

1. 点「确定」关掉对话框；
2. 跑 `git status` 或 `git log` 看结果，不要立刻重复敲 `git push`；
3. 只有确认远程没更新时，才重新 `git push` 一次——GCM 通常已经把令牌存进 Windows 凭据管理器，第二次不会再弹窗。

**确认令牌到底存没存住**：

```bash
cmdkey /list | findstr github
```

看到 `LegacyGeneric:target=git:https://github.com` 就说明存好了，以后 `push` 不会再问。

**如果每次都弹**，说明令牌没存住，可以换成最简单的存储方式：

```bash
git config --global credential.helper store
```

登录一次后令牌会明文存在 `~/.git-credentials`。**只在自己的电脑上这么做**，公用电脑不要用。

**如果还不行**，最省事的退路是改用 GitHub Desktop 推送：它的登录和命令行是两套独立机制，桌面端能推，就说明账号和仓库权限都没问题。

---

## 8. 后悔药

按「错到哪一步」分类，别用错药。

| 你想撤销的事 | 命令 | 风险 |
|---|---|---|
| 文件改了，还没 `add` | `git restore 文件名` | **改动直接消失，不可恢复** |
| 已经 `add`，还没 `commit` | `git restore --staged 文件名` | 安全，只是退出暂存区 |
| 刚 `commit`，还没 `push` | `git commit --amend -m "新说明"` | 安全，改写最近一次提交 |
| 已经 `push` 出去了 | `git revert 提交编号` | 安全，生成一个「反向提交」把改动抵消 |
| 提交错了想彻底删掉 | `git reset --hard 编号` | **危险**，已 push 的绝不要用 |
| 误删了文件/提交，想找回 | `git reflog` | 救命用，能看到所有操作记录 |

### 一条红线

> **已经 push 出去的提交，不要用 `reset` 去改。**
>
> 你能 reset 本地，但 GitHub 上那份还在。想让两边一致就得 `push --force`，而强推会覆盖别人的工作、也会让历史断掉。正确做法是用 `revert` 往前走一步。

### `reflog` 怎么救命

哪怕你 `reset --hard` 删掉了提交，只要没被 Git 垃圾回收（默认 30 天），都还能找回来：

```bash
git reflog
```

```
1a2b3c4 HEAD@{0}: reset: moving to HEAD~1
9f8e7d6 HEAD@{1}: commit: 完成C语言作业1
```

找到你删掉的那个编号，`git reset --hard 9f8e7d6` 就回来了。

---

## 9. 分支：先知道是什么就行

### 打个比方

主线是「正式稿」，分支是「草稿纸」。你可以在草稿纸上随便涂改，改满意了再誊到正式稿上，正式稿始终是干净的。

### 你大概什么时候才需要

说实话，**一个人写学习笔记，一整个学期都用不到分支**。等到下面这些情况再回来查：

- 想试一个改动，但不确定好不好，怕把能跑的代码搞坏；
- 两个人同时改同一个文件。

### 真要用就这三条

```bash
git switch -c dev        # 新建并切到 dev 分支
git switch main          # 切回 main
git merge dev            # 把 dev 合并进当前分支
```

（老教程里的 `git checkout -b dev` 是同样效果，`switch` 是更新、更不容易误操作的写法。）

---

## 10. 用 GitHub Desktop 的话

GitHub Desktop 把上面这些命令变成了按钮。对照关系：

| 命令行 | GitHub Desktop 里 |
|---|---|
| `git status` | 左侧 **Changes** 面板，直接列出改动文件 |
| `git add` | 不用做，它会自动把改动列出来 |
| `git commit -m` | 左下角 **Summary** 填一句，点 **Commit to main** |
| `git push` | 右上角 **Push origin** |
| `git pull` | 右上角 **Pull origin** |
| `git log` | 上方 **History** 标签页 |

### 建议的用法

- **前两周只用 Desktop**，先把「改 → 提交 → 推送」的肌肉记忆建立起来；
- **之后再切命令行**，因为你迟早会遇到 Desktop 解决不了的报错，那时候看不懂命令会很难受；
- **两边混用完全没问题**，它们操作的是同一个仓库、同一份 `.git` 目录，不存在冲突。

---

## 11. 把 Git 变成习惯

工具再好，不用就是没用。真正让仓库「活起来」的不是命令，是节奏。

| 时间 | 做什么 | 大概耗时 |
|---|---|---|
| 每学完一节 | 代码进 `code/`，结论进 `notes/` | 2 分钟 |
| 每周日晚 | 补完整周笔记，提交一次 | 15 分钟 |
| 提交完 | `git push`，去网页看一眼 | 30 秒 |
| 每月底 | 翻旧笔记，把当时理解错的地方改掉 | 30 分钟 |

### 一条经验

> **提交小、提交勤。**

一次改一个函数、写一段笔记就提交，比攒一周再提交好得多。原因很简单：出问题时，改动越小越容易定位；三个月后回看，记录越细越有用。

「我今天没什么可提交的」——那就把今天的笔记也提交上去，笔记也是产出。

---

## 12. 速查表

### 12.1 命令

```bash
cd /d/代码/my-learning-repo    # 进仓库
pwd                            # 我在哪
ls                             # 这个目录有什么

git status                     # 有什么改动
git add .                      # 全部放进暂存区
git commit -m "说明"            # 封箱
git push                       # 推上 GitHub

git log --oneline              # 看历史
git diff                       # 看具体改了什么
git pull --rebase              # pull 被拒时用
git restore --staged 文件名     # 撤销暂存
```

### 12.2 路径

```
C:\Users\mile chan   →   /c/Users/mile chan
D:\代码               →   /d/代码
规则：盘符换 /c /d，反斜杠全部改正斜杠
```

### 12.3 提交信息模板

```
<类型>: <做了什么>

feat: 加上成绩排序功能
fix: 修正冒泡排序的边界判断
docs: 补充环境配置说明
```

---

## 13. 名词对照

| 英文 | 中文 | 一句话说明 |
|---|---|---|
| repository / repo | 仓库 | 一个被 Git 管理的文件夹 |
| working tree | 工作区 | 你正在编辑的那份文件 |
| index / staging area | 暂存区 | 已 `add` 但还没 `commit` 的中间层 |
| commit | 提交 | 给某一时刻的代码拍一张照片 |
| branch | 分支 | 并行的开发线，新手用 main 就够 |
| main | 主分支 | 默认那条线，以前叫 master |
| remote | 远程仓库 | GitHub 上那一份 |
| origin | 远程名 | 默认给远程起的名字 |
| clone | 克隆 | 把远程仓库整个拷到本地 |
| pull | 拉取 | 把远程的提交取回本地并合并 |
| push | 推送 | 把本地的提交送到远程 |
| fetch | 获取 | 只取回来看，不合并 |
| merge | 合并 | 把一条分支的内容并进另一条 |
| conflict | 冲突 | 两边改了同一处，Git 不敢替你决定 |
| HEAD | 当前位置 | 你现在站在哪个提交上 |
| .gitignore | 忽略清单 | 写明哪些文件永远不用提交 |

---

## 附录 A：换电脑、多设备怎么办

### 换一台电脑继续写

```bash
cd /d/代码
git clone https://github.com/用户名/仓库名.git
```

`clone` 会把整个历史一起拉下来，包括所有提交记录。

### 两台电脑交替用

养成习惯：**开始干活前先 `git pull`，干完活 `git push`。**

忘了 pull 就改，push 时会被拒（见报错 7），`git pull --rebase` 能救回来，但每次都要处理很烦。

---

## 附录 B：哪些东西绝对不要提交

### 三类东西

1. **密钥、密码、身份证、学号**——公开仓库全世界都能搜到，而且 Git 历史里删不干净；
2. **超大文件**——视频、安装包、虚拟机镜像，GitHub 单文件超过 100MB 直接拒收；
3. **别人的项目代码**——抄了要写清出处，否则算学术不端。

### `.gitignore` 怎么起作用

在仓库根目录建一个 `.gitignore` 文件，每行写一个规则，被匹配的文件 Git 就当作不存在：

```gitignore
# 编译产物
*.exe
*.o

# 编辑器
.vscode/
.idea/

# 系统
Thumbs.db
.DS_Store
```

注意：`.gitignore` **只对还没被跟踪的文件有效**。已经被提交过的文件，得先 `git rm --cached 文件名` 才能开始忽略。

---

## 附录 C：常见问题

**Q：我能用 Typora 写 README 吗？**
能。Typora 存的就是普通 `.md` 文件，GitHub 认的。但一定要在设置里打开「优先使用相对路径」，否则你粘的图片路径会变成 `C:\Users\...`，别人打开全是红叉。

**Q：写完 Typora 按了 Ctrl+S，为什么 `git status` 没变化？**
三种可能：Typora 保存到别的地方去了；文件确实没变；你不在仓库目录下。用 `pwd` 和 `git status` 逐个排除。

**Q：可以把整个文件夹拖进 GitHub 网页上传吗？**
可以，但会丢历史，而且大文件夹很容易失败。用 Git 更靠谱。

**Q：提交信息写错了能改吗？**
没 push 之前：`git commit --amend -m "新说明"`。已经 push 了就别改了。

**Q：仓库想改成私有的，行吗？**
行。GitHub 仓库页 Settings → 最下面 Danger Zone → Change visibility。注意改成私有后，没登录的人就看不到，简历上放链接要考虑这一点。

**Q：多久提交一次算正常？**
按「一件事一次」来。一天三次和一周一次都可以，关键是每一次都能说清做了什么。

---

> 最后一句：这份文档不用背。真正常用的就 [第 12 章速查表](#12-速查表) 那十几行，其余的都是「遇到了再查」。
