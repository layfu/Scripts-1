## 描述

一个使用 `Python` 语言写的薅羊毛脚本仓库，支持 `github action` 和 `linux/windows virtual private server` 

## 部署方式

**注意**：所有脚本任务都是未启用并且默认不发消息推送，请自行根据自身需求设置，配置文件采用 `yaml` 语法编写（注意空格），建议使用文本编辑器填写配置，以防格式错误！ 

最好写完配置文件后在[在线检验yaml语法](https://www.toolfk.com/tool-format-yaml)检验一下 `yaml` 语法是否规范，当然你可以百度 `Google` 其他的在线检验网站。

### 一、linux/windows virtual private server

* 如果你没有安装 `git` ，那么运行 `apt-get install git -y`(Ubuntu/Debian) 或者 `yum install git -y`(Centos) 根据你的系统选择对应的命令。

* `git clone https://github.com/TNanko/Scripts.git`。

* 确保你的 `vps` 里面安装 `python3.6` 及其以上版本，没有安装则运行 `apt-get install python3 -y`(Ubuntu/Debian) 或者 `yum install python3 -y`(Centos) 命令来安装 `python3`。

* 安装脚本所需要的包 `pip3 install -r requirements.txt`。

* 进入到 `config` 文件夹下（`cd config`） ，复制文件夹 `config` 下的 `config.yml.example` 文件并将文件命名为 `config.yml` （`cp config.yml.example config.yml`）。使用 `vi` 或者文本编辑器编辑文件 `config.yml` 填写配置。

* 在配置文件中填写对应的推送方式的 `key` 或者 `code`。

* 找到想要运行的脚本，设置对应的配置信息。

* 返回到 `Scripts` 目录下，运行脚本 `python3 ./scripts/xxx.py`。

* 最后就是自己添加定时任务，不会的百度 `Google`。

### 二、github action

* 添加一个 `secrets` - `CONFIG` ， `Value` 内容请复制目录下 `./config/config.yml.example` 所有内容。

* 填写对应的推送方式的 `key` 或者 `code`。

* 找到想要运行的脚本，设置对应的配置信息。

* 手动 `star` 一下仓库，看下 `Action` 是否正常运行。

* 定时同步仓库：添加一个 `secrets` - `PAT` ，[教程](https://www.jianshu.com/p/bb82b3ad1d11)。

### 三、Docker

1. 下载本仓库`config`文件夹下的`config.yml.example`文件到指定位置，并改名为`config.yml`，比如下载到`/appdata/tnanko`文件下，可如下操作：

    ```shell
    cd /appdata/tnanko
    wget --no-check-certificate https://raw.githubusercontent.com/TNanko/Scripts/master/config/config.yml.example -O config.yml
    ```

2. 修改刚刚下载好的`config.yml`，如何修改请见 [教程](docs/qq_read.md)，写完配置文件后在[在线检验yaml语法](https://www.toolfk.com/tool-format-yaml)检验一下 `yaml` 语法是否规范，当然你可以百度 `Google` 其他的在线检验网站。

3. 自行安装好Docker后部署容器，以刚刚修改的配置文件的路径`/appdata/tnanko`为例：

    ```shell
    docker run -dit \
    -v /appdata/tnanko/config.yml:/Scripts/config/config.yml `#配置文件保存目录，冒号左边是示例路径，以你实际路径为准` \
    -v /appdata/tnanko/log:/Scripts/log `#日志保存目录，冒号左边是示例路径，以你实际路径为准` \
    -e ENABLE_QQREAD_CRONTAB=true `#如需启用qq_read的定时任务，必须保留此行` \
    --name tnanko_scripts \
    --hostname tnanko_scripts \
    --restart always \
    evinedeng/tnanko_scripts
    ```

    总共有以下5个环境变量供选择，你不设置就使用默认值，如不想使用默认值，请参考上述命令中`-e ENABLE_QQREAD_CRONTAB=true`这一行的形式添加到部署容器的命令中（注意等号两边都没有空格）：

| 序号 | 变量名               | 可以设置的值 | 默认值    | 说明                                                                                    |
| ---- | ----------------------- | ---------------- | ------------ | ----------------------------------------------------------------------------------------- |
| 1    | ENABLE_QQREAD_CRONTAB   | true/false       | false        | 是否启用qq_read的定时任务，注意这里和config.yml中qq_read均设置为true，定时任务才会运行。 |
| 2    | QQREAD_CRONTAB          | 5位的crontab格式 | */10 * * * * | 没啥好说的，你想啥时候运行qq_read，ENABLE_QQREAD_CRONTAB设置为true时，本项设置才会生效。**按上述部署容器的命令时需要加双引号。** |
| 3    | ENABLE_BILIBILI_CRONTAB | true/false       | false        | 是否启用bilibili的定时任务，注意这里和config.yml中bilibili均设置为true，定时任务才会运行。 |
| 4    | BILIBILI_CRONTAB        | 5位的crontab格式 | 15 8 * * * | 没啥好说的，你想啥时候运行bilibili，ENABLE_BILIBILI_CRONTAB设置为true时，本项设置才会生效。**按上述部署容器的命令时需要加双引号。** |
| 5    | RM_LOG_DAYS_BEFORE      | 正整数        | 7            | 每周六凌晨4:25分自动删除指定时间以前的日志，默认为7天，如需要修改为其他天数，请自定义。 |

4. 完成，等着收钱吧。如果你想修改配置，直接改完`config.yml`就行了，无需重启容器什么的。

5. **如果提示`config.yml.example`文件有更新时，如何操作：**

    ```shell
    cd /appdata/tnanko  # 注意这只是示例目录
    docker cp tnanko_scripts:/Scripts/config/config.yml.example config.new.yml
    # 然后编辑/appdata/tnanko/config.new.yml这个文件，可以参考你原来的config.yml这个文件复制过来，再补充新的设置即可，修改好后再运行下面命令
    mv config.new.yml config.yml
    ```
6. 如果你想自动更新`tnanko_scripts`容器，请安装watchtower：

    ```shell
    docker run -d \
    --name watchtower \
    -v /var/run/docker.sock:/var/run/docker.sock \
    containrrr/watchtower
    ```

## 消息推送

目前支持 `ios bark app` ， `telegarm bot` ， `dingding bot` ， `serverChan` 四种方式推送消息。

打开推送方式：将 `config.yml` 里面 `notify` 选项中，参数 `enable` 设置为 `true`

## 支持的脚本任务

### 企鹅读书

* 使用脚本前务必看一遍教程，[脚本地址](https://raw.githubusercontent.com/TNanko/Scripts/master/scripts/qq_read.py)，[使用教程](https://github.com/TNanko/Scripts/blob/master/docs/qq_read.md)

* 此脚本使用 `Python` 语言根据[原js脚本](https://raw.githubusercontent.com/ziye12/JavaScript/master/Task/qqreads.js)重写，并在原有的基础上扩展了一些功能。

### `bilibili` 签到

* 脚本地址：https://raw.githubusercontent.com/TNanko/Scripts/master/scripts/bilibili.py

## 关于版本

### 配置文件的版本

x.y.z x每增加一个数字，表示代码框架发生重大变化；y每增加一个数字，表示增加一个新的脚本；z每增加一个数字，表示修复某个脚本的bug。

### 脚本的版本

x.y.z x每增加一个数字，大概率是脚本重写了；y每增加一个数字，表示增加一个新的功能；z每增加一个数字，表示增加修复该脚本的一个bug。
