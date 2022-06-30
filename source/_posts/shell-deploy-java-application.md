---
title: deploy-java-application
date: 2022-06-30 09:17:13
tags:
---
## 创建`deploy.sh`
```shell

#!/bin/sh

# 该脚本为Linux下启动java程序的脚本
#
# 特别注意：
# 该脚本使用系统kill命令来强制终止指定的java程序进程。
# 所以在杀死进程前，可能会造成数据丢失或数据不完整。如果必须要考虑到这类情况，则需要改写此脚本，
#
#
# 根据实际情况来修改以下配置信息 ##################################

# JAVA应用程序的名称
APP_NAME=$2
# jar包存放路径
JAR_PATH='.'
# jar包名称
JAR_NAME=$2
# 日志输出文件
LOG_FILE=/home/mayunfeng/application/logs/$JAR_NAME.log
active=prod

# java虚拟机启动参数
# JAVA_OPTS="-Xms512m -Xmx512m -XX:MetaspaceSize=512m -XX:MaxMetaspaceSize=1024m -XX:ParallelGCThreads=4 -XX:+PrintGCDateStamps -XX:+PrintGCDetails -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=utf-8"
JAVA_OPTS="-Xms64m -Xmx128m"
# 根据实际情况来修改以上配置信息 ##################################
# ######### Shell脚本中$0、$?、$!、$$、$*、$#、$@等的说明 #########

# $$ Shell本身的PID（ProcessID，即脚本运行的当前 进程ID号）
# $! Shell最后运行的后台Process的PID(后台运行的最后一个进程的 进程ID号)
# $? 最后运行的命令的结束代码（返回值）即执行上一个指令的返回值 (显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误)
# $- 显示shell使用的当前选项，与set命令功能相同
# $* 所有参数列表。如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数，此选项参数可超过9个。
# $@ 所有参数列表。如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数。
# $# 添加到Shell的参数个数
# $0 Shell本身的文件名
# $1～$n 添加到Shell的各参数值。$1是第1参数、$2是第2参数…。

pid=0
# 检查程序是否处于运行状态
checkpid() {
  # 查询出应用服务的进程id，grep -v 是反向查询的意思，查找除了grep操作的run.jar的进程之外的所有进程
#  pid=`ps -ef|grep $JAR_NAME|grep -v grep|awk '{print $2}'`
  app=`jps | grep $JAR_NAME`
  # [ ]表示条件测试。注意这里的空格很重要。要注意在'['后面和']'前面都必须要有空格
  # [ -z STRING ] 如果STRING的长度为零则返回为真，即空是真
  # 如果不存在返回0，存在返回1
  if [ -n "${app}" ]; then
    pid=`echo $app | awk '{print $1}'`
  else
    pid=0
  fi
}

# 服务启动方法
start() {
  checkpid
  if [ "$pid" -ne 0 ]; then
    echo "$APP_NAME is already running pid is ${pid}"
  else

    # jar服务启动脚本
    nohup java $JAVA_OPTS -Dspring.profiles.active=$active -jar $JAR_PATH/$JAR_NAME >$LOG_FILE 2>&1 &
    echo "start $APP_NAME successed pid is $!, check: tail -f $LOG_FILE"
   fi
  }

# 服务停止方法
stop() {
  checkpid
  if [ "$pid" -ne 0 ]; then
    echo "pid = $pid begin kill $pid"
    kill $pid
  fi
  # echo "$pidf"
  sleep 2
  # 判断服务进程是否存在
  checkpid
  if [ "$pid" -ne 0 ]; then
    echo "pid = $pid begin kill -9 $pid"
    kill -9  $pid
    sleep 2
    echo "$APP_NAME process stopped！"
  else
    echo "$APP_NAME is not running！"
  fi
}

# 服务运行状态查看方法
status() {
  checkpid
  if [ "$pid" -ne 0 ]; then
    echo "$APP_NAME is running，pid is ${pid}"
  else
    echo "$APP_NAME is not running！"
  fi
}

# 重启服务方法
restart() {
  # 调用服务停止命令
  stop
  # 调用服务启动命令
  start
}

# 帮助说明，用于提示输入参数信息
usage() {
    echo "Usage: sh deploy.sh [ start | stop | restart | status ]"
    exit 1
}

###################################
# 读取脚本的第一个参数($1)，进行判断
# 参数取值范围：{ start | stop | restart | status }
# 如参数不在指定范围之内，则打印帮助信息
###################################
#根据输入参数，选择执行对应方法，不输入则执行使用说明
case "$1" in
  'start')
    start
    ;;
  'stop')
    stop
    ;;
  'restart')
    restart
    ;;
  'status')
    status
    ;;
  *)
    usage
    ;;
esac
exit 0

```

## 使用
```shell
# 开启服务
sh deploy.sh start jar包名称

# 结束
sh deploy.sh stop jar包名称

# 重启
sh deploy.sh restart jar包名称
```