```
1. 打包 make bl && mv bin mars, 
2. 上传服务器 scp mars mars_service_01: && scp mars mars_service_01:
3. elb 停 server01
4. ssh mars_service_01
5. 备份 cd /var/mars/ && sudo cp mars mars_20200724
6. 停服务 sudo supervisorctl stop mars
7. 更新程序 sudo cp $HOME/mars mars
8. 启动程序 sudo supervisorctl start mars
9. 查看日志看下是否启动
10. elb 启动 server01
```

```
rG7L$Q3#BV1O
M5#fkk&8j5xR$aJ aws
```

```shell
make bl && mv bin mars && scp mars bytepower-dev-08: && rm mars && ssh -t bytepower-dev-08 "sudo mv /var/mars-prize/mars /var/mars-prize/mars_bak && sudo mv mars /var/mars-prize && sudo supervisorctl restart mars-prize"
```

