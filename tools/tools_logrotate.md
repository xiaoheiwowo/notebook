【Log-Rotate】app 日志 rotate 配置
==================================

1、配置rsyslog日志，将同一个app的日志导入同一个文件，不再根据日期进行命名日志文件。

将类似于 `$template IfgetlikesDailyPerHostLogs,"/mnt/log/ifgetlikes_%$YEAR%%$MONTH%%$DAY%_%$MYHOSTNAME%.log"`
的配置改为 `$template IfgetlikesDailyPerHostLogs,"/mnt/log/ifgetlikes.log"`

2、创建名称为 `/etc/logrotate.d/<appname>` 的文件，文件内容为

```
</path/to/log>
{
    rotate <count> # count为保留历史日志的数量
    daily                 # 每天rotate
    missingok
    notifempty
    compress        # 压缩
    postrotate
        reload rsyslog >/dev/null 2>&1 || true
    endscript
}
```
这样，每天 `/etc/cron.daily` 或 `/etc/cron.d` 就会每天调度该任务。
