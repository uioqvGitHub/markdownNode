### ArchLinux

#### pacman -Syu 滚动升级失败

```bash
#报错
error: failed to commit transaction (conflicting files)
js52: /usr/lib/libmozjs-52.so.0 exists in filesystem
#解决  --强制将失败的包升级
Pacman -S js52 --force
```

