### 问题描述

在 macOS 系统中打开一个应用时提示“已损坏，无法打开”

### 解决方法

```bash
# 1. 打开终端
# 2. 输入以下命令，回车
$ sudo xattr -d com.apple.quarantine /Applications/xxx.app
# 注意： /Applications/xxx.app 为有问题的APP的路径，推荐直接将app文件拖入终端会自动生成路径，以防止空格等转移字符手动复制或输入错误。
# 3. 重启应用即可。


# 小贴士： 在系统偏好设置中，需要开启任何来源安装
```

