> **语言**：本文档为简体中文翻译，[点此查看英文原版 README.md](README.md)。

# Fisher

> [Fish](https://fishshell.com) 的插件管理器——一位亲切的交互式 shell。[快来淘最新插件！](https://github.com/jorgebucaran/awsm.fish#readme)

从命令行掌控你的函数、补全、按键绑定与配置片段。释放 shell 的真正潜力，打磨你的提示符，并在不同系统间轻松打造可复用的配置。Fisher 对 shell 启动零开销，让你的 shell 始终保持轻快灵敏。不玩花活，只管顺滑前行！

- Fisher 是 100% 纯 Fish 编写，贡献或修改都轻而易举
- 火力全开的并发插件下载，快到让你怀疑现实
- 零配置即可上手——我们没开玩笑！
- 同样兼容 Oh My Fish! 插件

> #### ☝️ [正在从 Fisher `3.x` 或更旧版本升级？系好安全带，读读这篇！](https://github.com/jorgebucaran/fisher/issues/652)

## 安装

```console
curl -sL https://raw.githubusercontent.com/jorgebucaran/fisher/main/functions/fisher.fish | source && fisher install jorgebucaran/fisher
```

## 快速上手

Fisher 让你像大佬一样安装、更新和移除插件，并全程享受 Fish 的[自动补全](https://fishshell.com/docs/current/index.html#completion)和丰富的语法高亮。

### 安装插件

安装插件请使用 `install` 命令，并指向对应的 GitHub 仓库。

```console
fisher install jorgebucaran/nvm.fish
```

> 想从 GitLab 安装？没问题——只要在插件路径前加上 `gitlab.com/` 即可。

你也可以通过"插件名 + `@` + 标签、分支或[提交](https://git-scm.com/docs/gitglossary#Documentation/gitglossary.txt-aiddefcommit-ishacommit-ishalsocommittish)"来安装插件的特定版本。

```console
fisher install IlanCosman/tide@v6
```

嘿，从本地目录安装插件也一样行！

```console
fisher install ~/path/to/plugin
```

> 注意！Fisher 默认会把插件展开到 Fish 配置目录中，覆盖已有文件。如果这不合你意，可以把 `$fisher_path` 设置到你偏好的位置，并将其加入函数路径（[#640](https://github.com/jorgebucaran/fisher/issues/640)）。

### 列出插件

使用 `list` 命令即可看到你所有光鲜亮丽的已安装插件。

```console
$ fisher list
jorgebucaran/fisher
ilancosman/tide@v5
jorgebucaran/nvm.fish
/home/jb/path/to/plugin
```

`list` 命令还支持用正则表达式过滤输出。

```console
$ fisher list \^/
/home/jb/path/to/plugin
```

### 更新插件

`update` 命令闪亮登场！它能把一个或多个插件更新到最新版本。

```console
fisher update jorgebucaran/fisher
```

> 只需输入 `fisher update`，即可一步更新所有内容。

### 移除插件

用 `remove` 命令和已安装插件说再见。

```console
fisher remove jorgebucaran/nvm.fish
```

想来点彻底的？一键清空所有插件，连 Fisher 自己也不例外。

```console
fisher list | fisher remove
```

## 使用 `fish_plugins` 文件

每当你在命令行安装或移除插件，Fisher 都会把所有已安装插件记录到 `$__fish_config_dir/fish_plugins`。把这个文件加入你的 dotfiles 或版本控制，就能在不同系统间轻松共享配置。

你也可以直接编辑这个文件，然后运行 `fisher update`，像专业选手一样提交变更：

```console
$EDITOR $__fish_config_dir/fish_plugins
```

```diff
jorgebucaran/fisher
ilancosman/tide@v5
jorgebucaran/nvm.fish
+ PatrickF1/fzf.fish
- /home/jb/path/to/plugin
```

```console
fisher update
```

这会安装 **PatrickF1**/**fzf.fish**，移除 /**home**/**jb**/**path**/**to**/**plugin**，并更新其余所有内容。

## 创建插件

插件可以在 `functions`、`conf.d` 和 `completions` 目录中包含任意数量的文件。大多数插件只是一个单独的函数或一段[配置片段](https://fishshell.com/docs/current/index.html#configuration)。来看看一个典型插件的完整构造：

<pre>
<b>flipper</b>
├── <b>completions</b>
│   └── flipper.fish
├── <b>conf.d</b>
│   └── flipper.fish
└── <b>functions</b>
    └── flipper.fish
</pre>

这些目录下的非 `.fish` 文件和子目录会被分别复制到 `$fisher_path` 下的 `functions`、`conf.d` 或 `completions` 中。

### 事件系统

Fish 的[事件](https://fishshell.com/docs/current/cmds/emit.html)会在插件被安装、更新或移除时通知插件。

> 请注意，`--on-event` 函数必须在其事件触发时已被加载。因此，请把事件处理函数放在 `conf.d` 目录中。

```fish
# Defined in flipper/conf.d/flipper.fish

function _flipper_install --on-event flipper_install
    # Set universal variables, create bindings, and other initialization logic.
end

function _flipper_update --on-event flipper_update
    # Migrate resources, print warnings, and other update logic.
end

function _flipper_uninstall --on-event flipper_uninstall
    # Erase "private" functions, variables, bindings, and other uninstall logic.
end
```

## 创建主题

主题与普通 Fish 插件无异，只是在 `themes` 目录中放入了 `.theme` 文件。主题自 [Fish `3.4`](https://github.com/fish-shell/fish-shell/releases/tag/3.4.0) 引入，可与 `fish_config` 内置命令配合使用。必要时，主题也可以在 `functions`、`conf.d` 或 `completions` 中放置文件。来看看一个典型主题插件长什么样：

<pre>
<b>gills</b>
├── <b>conf.d</b>
│   └── gills.fish
└── <b>themes</b>
    └── gills.theme
</pre>

### 在主题中使用 `$fisher_path`

如果你把 `$fisher_path` 自定义为 `$__fish_config_dir` 以外的目录，你的主题就无法通过 `fish_config` 使用。这是因为 Fish 期望主题位于 `$__fish_config_dir/themes`，而不是 `$fisher_path/themes`。Fish 暂时还不支持配置这一点，不过已经有人[提出了添加该功能的请求](https://github.com/fish-shell/fish-shell/issues/9456)。

别慌！只要把 Fisher 的 `themes` 目录软链接到你的 Fish 配置目录，就能轻松解决。首先，备份已存在的 themes 目录。

```console
mv $__fish_config_dir/themes $__fish_config_dir/themes.bak
```

接下来，为 Fisher 的 themes 目录创建软链接。

```console
ln -s $fisher_path/themes $__fish_config_dir/themes
```

既想用主题插件，又想保留自己的本地主题？这也完全没问题（[#708](https://github.com/jorgebucaran/fisher/issues/708)）。

## 插件发现

Fisher 虽然不依赖中心化插件仓库，但发现新插件也不该像闯入未知水域一样难。要提升你插件的曝光度、让用户更容易找到它，可以用 [`fish-plugin`](https://github.com/topics/fish-plugin) 为[你的仓库添加相应主题标签](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/classifying-your-repository-with-topics#adding-topics-to-your-repository)。这样一来，你不仅在为 Fisher 社区做贡献，也让更多用户能够探索新插件、提升他们的 Fish shell 体验。别让插件发现变成一场"摸鱼"冒险，现在就给你的插件打上标签吧！

## 致谢

Fisher 于 2016 年由 [@jorgebucaran](https://github.com/jorgebucaran) 开启旅程，最初是 Fish 的 shell 配置管理器。一路走来，许多人出手相助，把它塑造成了今天的样子。[Oh My Fish](https://github.com/oh-my-fish/oh-my-fish) 作为第一个广受欢迎的 Fish 框架开辟了道路。[@jethrokuan](https://github.com/jethrokuan) 在早期提供了关键支持。[@PatrickF1](https://github.com/PatrickF1) 坦率的反馈屡屡价值千金。让 Fisher 自举（bootstrap）最初是 [@IlanCosman](https://github.com/IlanCosman) 的绝妙点子。感谢所有贡献者！<3

## 许可

[MIT](LICENSE.md)
