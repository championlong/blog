# 命令

## set 命令
命令用来修改 Shell 环境的运行参数，也就是可以定制环境。一共有十几个参数可以定制，[官方手册](https://www.gnu.org/software/bash/manual/html_node/The-Set-Builtin.html)有完整清单，本文介绍其中最常用的四个。
```shell
set -euxo pipefail
```
### set -e
选项可以让你的脚本在出现异常时马上退出，后续命令不再执行。默认情况下Shell脚本不会因为错误而结束执行。

但是，某些命令的非零返回值可能不表示失败，或者开发者希望在命令失败的情况下，脚本继续执行下去。有一种方法是使用 `command || true`，使得该命令即使执行失败，脚本也不会终止执行。
```shell
command
if [ "$?"-ne 0]; then echo "command failed"; exit 1; fi
#或者
command || { echo "command failed"; exit 1; }
#或者
if ! command; then echo "command failed"; exit 1; fi
```
### set -o pipefail
默认情况下 Bash 会把最后一个子命令的返回值，作为整个命令的返回值。这个特别的选项表示在管道连接的命令中，只要有任何一个命令失败（返回值非0），则整个管道操作被视为失败。只有管道中所有命令都成功执行了这个管道才算成功执行。
### set -u
默认情况下Bash会将未定义的变量视为空，不会报错。增加此参数Bash会把所有未定义的变量视为错误
### set -x
让Bash把每个命令在执行前先打印出来，你可以认为这就是Bash的Debug开关。

## 环境变量
### IFS
它定义了用于分隔字段的字符。默认情况下，IFS 包含空格、制表符和换行符。
```shell
# 设置换行符和制表符为字段分隔符
IFS=$'\n\t'
```

## source 命令
通常用于将一些环境变量或其他脚本中的内容加载到当前脚本中。
```shell
source "${BASEDIR}/path
```

## touch 命令
创建一个空文件
```shell 
touch ${BASEDIR}/text
```

## shuf 命令
用于随机排列或选择输入的行
```shell 
shuf ${input_path} -o ${output_path}
```

## seq 命令
seq [起始值] [步进值] [结束值]生成指定序列
```shell
for shard in $(seq 0 10); do
done
```
## wait 命令
等待所有子进程完成。注意，如果在wait后使用`$?`获取的则是wait的执行结果。
```shell
for shard in $(seq 0 6); do
 command &
done
wait
```
# 常用
```shell
# $0 表示脚本本身的名称，dirname 函数获取其所在的目录
BASEDIR=$(dirname "$0")
# 使用 date 命令获取当前日期，并将其以指定的格式（年月日）
TODAY=`date +%Y%m%d`
YESTERDAY=$(date -d "-1 day" +"%Y%m%d")
```
