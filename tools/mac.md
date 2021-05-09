# Mac 



## System

- dock 自动隐藏延迟

```shell
defaults write com.apple.Dock autohide-delay -float 0 && killall Dock
defaults delete com.apple.Dock autohide-delay && killall Dock
```

