## 1. 再系统中查找某个文件
find / -name "filename.txt"  

### 2. 修改权限`
```sh
sudo chmod 777 filename.txt
```

### 3. 查看文件
```sh
ps -ef | grep "ssh"          # 查找所有与 SSH 相关的进程
ps -ef | grep -v "grep"     # 排除 grep 自身的结果
```
### 4. 实时监控资源使用情况（内存、cpu)
```sh
top
```

### 5. 压缩文件命令
解压缩到某个文件
```sh  
tar -zxvf filename.tar.gz  -C /path/to/extract
```