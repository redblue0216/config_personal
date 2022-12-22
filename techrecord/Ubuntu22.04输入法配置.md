# 安装配置流程
- 首先，安装 Chinese 语言包
- 单击桌面右上角图标，点击 Settings，在弹出的窗口中，点击 Region & Language，然后点击 Manage Installed Languages
- 或者通过这种方式打开，打开 Show Applications，找到 Language Support 并打开，弹出如下窗口，如下图所示
- 如果第一次点击的话，应该会弹出如下窗口，如果没有弹出直接跳过接下来的步骤。我的认为是，此窗口是安装 中文 语言包，点击 Install，然后等待安装完成，弹出 Language Support 窗口
- 在 Language Support 窗口中，单击 Install/Remove Languages... 单击 
- 在弹出的 Installed Languages 窗口中，找到 Chinese(simplified)，然后将其勾选，最后单击 Apply，如果进行了第4步的话，会发现 Chinese(simplified) 已经安装了
- 至此，只是完成了中文语言包的安装
- 接下来对中文输入法进行安装。打开终端，输入下列命令
```bash
$ sudo apt install ibus-pinyin
```

- 安装完成之后，单击桌面右上角图标，点击 Settings，在弹出的窗口中，点击 Keyboard图标
- 在 Keyboard 窗口中，单击 Input Sources 下的 +，随后弹出新窗口
- 在 Add an Input Source 窗口中选择 Chinese，然后选择 Chinese(Intelligent Pinyin) 并单击 Add 按钮
- 至此，就可以使用中文输入法了，通过 shift 键或者 Windows + space 组合键就可以进行切换输入法
