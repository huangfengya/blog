## ssh 免密登录

1. ssh-keygen -t rsa 生成秘钥，文件在 ~/.ssh/ 文件夹下，id_rsa 为私钥，id_rsa.pub 为公钥
2. 将 id_rsa.pub 中的复制到服务器 ~/.ssh/authorized_keys 文件中
3. 如果服务器中不存在此文件，可以使用 scp ~/.ssh/id_rsa.pub user@host:~/.ssh/authorized_keys -P port 命令复制过去

## awk 行处理

```
awk
    -F 指定分割符
    '{print $1, print $2}' $1 为输出第一个字段
                $0 输出整行
    '{printf "%s %d \n", $1, $2}' 格式化输出第一个和第二个字段
```

## 去重

```
首先使用 sort 排序
然后使用 uniq -c 进行去重
如果要对某一字段去重，使用 awk 进行过滤
```