 

DBdeployer是一个易于部署MySQL数据库服务器的工具。这是MySQL-Sandbox的端口，最初是用Perl编写的，并在Go中从头开始进行了重新设计。有关更多详细信息，请参见功能比较。

https://github.com/datacharmer/dbdeployer

 

安装
手动安装
安装很简单，因为唯一需要的是操作系统的二进制可执行文件。

从dbdeployer发行版中为的操作系统获取一个，并将其放在$ PATH中的目录中。（Windows没有二进制文件。有关更多信息，请参阅功能列表。）

例如： （版本：https://github.com/datacharmer/dbdeployer/releases/）
$ VERSION=1.58.2
$ OS=linux
$ origin=https://github.com/datacharmer/dbdeployer/releases/download/v$VERSION
$ wget $origin/dbdeployer-$VERSION.$OS.tar.gz
$ tar -xzf dbdeployer-$VERSION.$OS.tar.gz
$ chmod +x dbdeployer-$VERSION.$OS
$ sudo mv dbdeployer-$VERSION.$OS /usr/local/bin/dbdeployer

通过脚本安装
可以下载安装脚本，然后在计算机上运行它。该脚本将找到最新版本，下载相应的二进制文件，检查SHA256校验和，如果有特权，则将该可执行文件复制到中的目录中$PATH。
$ curl -s https://raw.githubusercontent.com/datacharmer/dbdeployer/master/scripts/dbdeployer-install.sh | bash

[root@yang-03 ~]# bash dbdeployer-install.sh 
tool 'shasum' not found

解决方法：          # yum install perl-Digest-SHA -y

 

当然，有一些先决条件：机器必须能够运行MySQL服务器。请注意，版本5.5及更高版本要求某些库在所有Linux版本（libnuma，libaio）中均未默认安装。
从1.40.0版开始，dbdeployer尝试检测主机是否已安装必需的库。当检测到缺少的库时，部署将失败，并显示一个错误，显示丢失的库。例如：
# dbdeployer deploy single 5.7
# 5.7 => 5.7.27
error while filling the sandbox definition: missing libraries will prevent MySQL from deploying correctly
client (/root/opt/mysql/5.7.27/bin/mysql): [ libncurses.so.5 => not found libtinfo.so.5 => not found]
server (/root/opt/mysql/5.7.27/bin/mysqld): [ libaio.so.1 => not found libnuma.so.1 => not found]
global: [libaio libnuma]

Use --skip-library-check to skip this check    # 如果使用--skip-library-check，则不会执行上述检查，并且部署可能会失败，并使的沙箱不完整。


 

获取远程压缩包
注意：从1.31.0版开始，dbdeployer remote已弃用，其功能由代替dbdeployer downloads。从1.31.0版开始，dbdeployer可以从多个位置下载各种样式的远程tarball。列出了适用于Linux和MacOS的Tarball。

查看可用的tarball
$ dbdeployer downloads list 
Available tarballs 
                          name                              OS     version   flavor     size   minimal 
-------------------------------------------------------- -------- --------- -------- -------- --------- 
 tidb-master-darwin-amd64.tar.gz                          Darwin     3.0.0   tidb      26 MB 
 tidb-master-linux-amd64.tar.gz                           Linux      3.0.0   tidb      26 MB 
 mysql-5.7.26-macos10.14-x86_64.tar.gz                    Darwin    5.7.26   mysql    337 MB 
 mysql-8.0.16-macos10.14-x86_64.tar.gz                    Darwin    8.0.16   mysql    153 MB 
 mysql-8.0.16-linux-x86_64-minimal.tar.xz                 Linux     8.0.16   mysql     44 MB   Y 
[...] 
该列表由dbdeployer在内部保留，但可以导出，编辑和重新加载。

获取压缩包
我们可以通过两种方式下载列出的tarball：

使用dbdeployer downloads get file_name，我们从上面的列表中复制并粘贴文件名。例如：dbdeployer downloads get mysql-8.0.16-linux-glibc2.12-x86_64.tar.xz。
使用dbdeployer downloads get-by-version VERSION [options]我们使用几个条件来标识所需文件的位置。
例如：

$ dbdeployer downloads get-by-version 5.7 --newest --dry-run 
$ dbdeployer downloads get-by-version 8.0 --newest --dry-run 
上面的命令在MacOS上执行，寻找当前操作系统的tarball，并获得版本最高的命令。请注意，该选项--dry-run显示了将下载的内容，但并未实际执行。

如果有多个符合搜索条件的文件，则dbdeployer返回错误。

$ dbdeployer downloads get-by-version 8.0 --newest --OS=linux --dry-run 
tarballs mysql-8.0.16-linux-x86_64-minimal.tar.xz and mysql-8.0.16-linux-glibc2.12-x86_64.tar.xz have the same version - Get the one you want by name 
在这种情况下，我们可以通过添加另一个参数来修复错误：

$ dbdeployer downloads get-by-version 8.0 --newest --OS=linux  --minimal --dry-run 
# On Linux 
$ dbdeployer downloads get-by-version 8.0 --newest --OS=linux  --minimal 
....  44 MB 
File /home/gmax/tmp/mysql-8.0.16-linux-x86_64-minimal.tar.xz downloaded 
Checksum matches 
 

 

如果我们下载了不适用于当前操作系统的压缩包，则会收到警告：

# On MacOS 
$ dbdeployer downloads get-by-version 8.0 --newest --OS=linux  --minimal 
....  44 MB 
File /Users/gmax/go/src/github.com/datacharmer/dbd-ui/mysql-8.0.16-linux-x86_64-minimal.tar.xz downloaded 
Checksum matches 
################################################################################ 
WARNING: Current OS is darwin, but the tarball's OS is linux 
################################################################################ 
我们还可以添加tarball风味，从而获得与上述标准不同的结果：

$ dbdeployer downloads get-by-version 8.0 --newest   --flavor=ndb --dry-run  
$ dbdeployer downloads get --help 
Downloads a remote tarball 
 
Usage: 
  dbdeployer downloads get tarball_name [options] [flags] 
 
Flags: 
      --dry-run             Show what would be downloaded, but don't run it 
  -h, --help                help for get 
      --progress-step int   Progress interval (default 10485760) 
      --quiet               Do not show download progress 
 
$ dbdeployer downloads get-by-flavor --help 
Manages remote tarballs 
 
Usage: 
  dbdeployer downloads [command] 
 
Available Commands: 
  add            Adds a tarball to the list 
  export         Exports the list of tarballs to a file 
  get            Downloads a remote tarball 
  get-by-version Downloads a remote tarball 
  get-unpack     Downloads and unpacks a remote tarball 
  import         Imports the list of tarballs from a file or URL 
  list           list remote tarballs 
  reset          Reset the custom list of tarballs and resume the defaults 
  show           Downloads a remote tarball 
 
Flags: 
  -h, --help   help for downloads 
定制压缩包列表
tarball列表嵌入在dbdeployer中，但是可以通过以下步骤进行修改：

跑 dbdeployer downloads export mylist.json --add-empty-item
编辑mylist.json，方法是将字段填充为空：
        { 
            "name": "mysql-cluster-8.0.16-dmr-linux-glibc2.12-x86_64.tar.gz", 
            "checksum": "SHA512:a587a774cc7a8f6cbe295272f0e67869c5077b8fb56917e0dc2fa0ea1c91548c44bd406fcf900cc0e498f31bb7188197a3392aa0d7df8a08fa5e43901683e98a", 
            "OS": "Linux", 
            "url": "https://dev.mysql.com/get/Downloads/MySQL-Cluster-8.0/mysql-cluster-8.0.16-dmr-linux-glibc2.12-x86_64.tar.gz", 
            "flavor": "ndb", 
            "minimal": false, 
            "size": 1100516061, 
            "short_version": "8.0", 
            "version": "8.0.16" 
        }, 
        { 
            "name": "FillIt", 
            "OS": "", 
            "url": "", 
            "flavor": "", 
            "minimal": false, 
            "size": 0, 
            "short_version": "", 
            "version": "", 
            "updated_by": "Fill it", 
            "notes": "Fill it" 
        } 
跑 dbdeployer downloads import mylist.json
该文件将保存到dbdeployer定制目录（$HOME/.dbdeployer）中，但仅在该文件通过验证时才保存，但仅在该文件通过验证时才保存。如果没有，则错误消息将显示需要进行哪些更改。

如果不再需要自定义列表，则可以使用dbdeployer downloads reset以下方法将其删除：该自定义文件将从dbdeployer目录中删除，并且嵌入式文件将再次使用。

 

永久更改tarball列表
如果需要经常将tarball添加到个人列表中，可能会很耗时。更好的方法是克隆此存储库，然后修改原始列表，然后打开包含更改的请求请求。在构建dbdeployer时使用该列表，因为JSON文件的内容将转换为内部列表。

输入新的tarball时，务必填写识别下载所需的所有详细信息。校验和字段非常重要。因为这可以确保下载的文件确实是原始文件。

dbdeployer可以计算MD5（当前在MySQL下载页面中使用的），SHA512（在1.31.0版中列出的大多数下载中使用的）以及SHA1和的校验和SHA256。要传达使用的是哪个校验和，该校验和字符串必须以算法为前缀，例如MD5:7bac88f47e648bf9a38e7886e12d1ec5。:接受冒号（）前后的可选空格。

 

从远程压缩包到一步即可使用
dbdeployer 1.33.0添加了一个命令dbdeployer downloads get-unpack tarball_name，该命令结合了dbdeployer get tarball_name后面的效果dbdeployer unpack tarball_name。该命令接受为定义的所有选项unpack，因此可以有选择地指示tarball的风格和版本，是否覆盖它以及是否要在操作后删除tarball。

$ dbdeployer downloads get-unpack mysql-8.0.16-linux-x86_64-minimal.tar.xz --overwrite --delete-after-unpack 
Downloading mysql-8.0.16-linux-x86_64-minimal.tar.xz 
....  44 MB 
File mysql-8.0.16-linux-x86_64-minimal.tar.xz downloaded 
Checksum matches 
Unpacking tarball mysql-8.0.16-linux-x86_64-minimal.tar.xz to $HOME/opt/mysql/8.0.16 
.........100.........200.219 
Renaming directory $HOME/opt/mysql/mysql-8.0.16-linux-x86_64-minimal to $HOME/opt/mysql/8.0.16 
 
$ dbdeployer downloads get-unpack mysql-cluster-8.0.16-dmr-linux-glibc2.12-x86_64.tar.gz --flavor=ndb --prefix=ndb --overwrite --delete-after-unpack 
Downloading mysql-cluster-8.0.16-dmr-linux-glibc2.12-x86_64.tar.gz 
.........105 MB.........210 MB.........315 MB.........419 MB.........524 MB 
.........629 MB.........734 MB.........839 MB.........944 MB.........1.0 GB....  1.1 GB 
File mysql-cluster-8.0.16-dmr-linux-glibc2.12-x86_64.tar.gz downloaded 
Checksum matches 
Unpacking tarball mysql-cluster-8.0.16-dmr-linux-glibc2.12-x86_64.tar.gz to $HOME/opt/mysql/ndb8.0.16 
[...] 
Renaming directory $HOME/opt/mysql/mysql-cluster-8.0.16-dmr-linux-glibc2.12-x86_64 to $HOME/opt/mysql/ndb8.0.16 
 

猜测最新的MySQL版本
如果知道有新版本的MySQL，但在下载列表中没有该版本，则可以尝试使用命令的快捷方式dbdeployer downloads get-by-version 8.0 --guess-latest （在1.41.0版中可用）

使用时--guess-latest，dbdeployer会在列表中查找可用的最新下载，将版本增加1，然后尝试从MySQL下载页面获取压缩包。

例如，如果压缩包列表中的最新版本是8.0.21，并且知道8.0.22刚刚发布，则可以运行以下命令

$ dbdeployer downloads get-by-version --guess-latest 8.0 --dry-run 
Whithout --dry-run，它将尝试下载MySQL 8.0.22。如果下载不可用，则会出现错误：

$ dbdeployer downloads get-by-version --guess-latest 8.0 
Guessed mysql-8.0.22-macos10.14-x86_64.tar.gz file not ready for download 
当心：下载发生时，没有校验和要执行。请谨慎使用此功能。

$ dbdeployer downloads get-by-version --guess-latest 8.0 
Downloading mysql-8.0.22-macos10.14-x86_64.tar.gz 
.........105 MB.....  166 MB 
File $PWD/mysql-8.0.22-macos10.14-x86_64.tar.gz downloaded 
No checksum to compare
 

## 查看当前已安装的所有 MySQL 基本信息
## 包括架构类型、版本、端口等等（输出信息过长或过多，已省略输出结果）
shell> dbdeployer sandboxes --full-info
    
## 查看各组 MySQL 的运行情况
shell> dbdeployer global status
MULTIPLE  /Users/yuzhenxing/sandboxes/all_masters_msb_8_0_17
node1 : node1 on  -  port    23818(23818)
node2 : node2 on  -  port    23819(23819)
node3 : node3 on  -  port    23820(23820)
    
MULTIPLE  /Users/yuzhenxing/sandboxes/group_sp_msb_8_0_17
node1 : node1 off  -   (22718)
node2 : node2 off  -   (22719)
node3 : node3 off  -   (22720)
    
## 批量停止所有运行的 MySQL （输出信息过长或过多，已省略输出结果）
shell> dbdeployer global stop
    
## 删除已部署的 MySQL 实例
## 如正在运行则会先停止，后清除实例相关目录
shell> dbdeployer delete msb_8_0_17
    
## 可对实例加锁，防止误删除
## dbdeployer sandboxes --full-info 命令可查的到哪组实例已加锁
shell> dbdeployer admin lock group_sp_msb_8_0_17
shell> dbdeployer delete group_sp_msb_8_0_17
shell> dbdeployer admin unlock group_sp_msb_8_0_17
shell> dbdeployer sandboxes --full-info 
使用命令

## 查看该组所有实例状态
shell> cd ~/sandboxes/rsandbox_8_0_17
shell> ./status_all
REPLICATION  /Users/yuzhenxing/sandboxes/rsandbox_8_0_17
master : master on  -  port    20718(20718)
node1 : node1 on  -  port    20719(20719)
node2 : node2 on  -  port    20720(20720)
    
## 一键重启该组所有实例
shell> ./restart_all
stop /Users/yuzhenxing/sandboxes/rsandbox_8_0_17/master
stop /Users/yuzhenxing/sandboxes/rsandbox_8_0_17/node1
stop /Users/yuzhenxing/sandboxes/rsandbox_8_0_17/node2
executing 'start' on master
. sandbox server started
executing 'start' on slave 1
. sandbox server started
executing 'start' on slave 2
. sandbox server started
    
## 单独重启该组某一实例
shell> cd ~/sandboxes/rsandbox_8_0_17/master
shell> ./restart
    
## 登录指定实例
shell> ./use
 

 

 

 

 

 

 

 

 

 

 

 

初始化环境
安装dbdeployer后，可以使用以下命令立即为操作做好准备的环境
$ dbdeployer init                 # 此命令创建必要的目录，然后下载最新的MySQL二进制文件，然后在正确的位置展开它们。它还启用命令行完成功能。

大多数用户都需要运行不带选项的命令。高级人员可以查看文档以微调初始化。
$ dbdeployer init -h
Initializes dbdeployer environment:
* creates $SANDBOX_HOME and $SANDBOX_BINARY directories
* downloads and expands the latest MySQL tarball
* installs shell completion file

Usage:
dbdeployer init [flags]

Flags:
--dry-run Show operations but don't run them
-h, --help help for init
--skip-all-downloads Do not download any file (skip both MySQL tarball and shell completion file)
--skip-shell-completion Do not download shell completion file
--skip-tarball-download Do not download MySQL tarball

 

更新dbdeployer
从版本1.36.0开始，dbdeployer可以通过从GitHub获取最新版本来进行自我更新。

最快的方法是通过运行
$ dbdeployer update                   # 此命令将从GitHub下载dbdeployer的最新版本，如果该版本的版本高于本地版本，则将覆盖dbdeployer可执行文件。

可以使用该--verbose选项在操作过程中获取更多信息。其他选项适用于高级用户。
$ dbdeployer update -h
Updates dbdeployer in place using the latest version (or one of your choice)

Usage:
dbdeployer update [version] [flags]

Examples:

$ dbdeployer update
# gets the latest release, overwrites current dbdeployer binaries

$ dbdeployer update --dry-run
# shows what it will do, but does not do it

$ dbdeployer update --new-path=$PWD
# downloads the latest executable into the current directory

$ dbdeployer update v1.34.0 --force-old-version
# downloads dbdeployer 1.34.0 and replace the current one
# (WARNING: a version older than 1.36.0 won't support updating)


Flags:
--OS string Gets the executable for this Operating system
--docs Gets the docs version of the executable
--dry-run Show what would happen, but don't execute it
--force-old-version Force download of older version
-h, --help help for update
--new-path string Download updated dbdeployer into a different path
--verbose Gives more info


还可以使用查看发布的详细信息 dbdeployer info releases latest。

 

 

 

主要业务
总览
使用dbdeployer，无论是否进行复制，可以一次部署一个沙盒，也可以一次部署多个沙盒。

main命令deploy及其子命令single，replication和和multiple与已解压缩到sandbox-binary目录（默认为$ HOME /opt/mysql）中的MySQL tarball一起使用。

要使用tarball，必须首先运行unpack命令，该命令会将tarball解压到正确的目录中。

例如：

$ dbdeployer unpack mysql-8.0.4-rc-linux-glibc2.12-x86_64.tar.gz 
Unpacking tarball mysql-8.0.4-rc-linux-glibc2.12-x86_64.tar.gz to $HOME/opt/mysql/8.0.4 
.........100.........200.........292 
 
$ dbdeployer deploy single 8.0.4 
Database installed in $HOME/sandboxes/msb_8_0_4 
. sandbox server started 
该程序没有任何依赖关系。一切都包含在二进制文件中。不带参数或带有dbdeployer的调用--help将显示主帮助屏幕。

$ dbdeployer --version 
dbdeployer version 1.58.2 
 
 
$ dbdeployer -h 
dbdeployer makes MySQL server installation an easy task. 
Runs single, multiple, and replicated sandboxes. 
 
Usage: 
  dbdeployer [command] 
 
Available Commands: 
  admin           sandbox management tasks 
  cookbook        Shows dbdeployer samples 
  data-load       tasks related to dbdeployer data loading 
  defaults        tasks related to dbdeployer defaults 
  delete          delete an installed sandbox 
  delete-binaries delete an expanded tarball 
  deploy          deploy sandboxes 
  downloads       Manages remote tarballs 
  export          Exports the command structure in JSON format 
  global          Runs a given command in every sandbox 
  help            Help about any command 
  import          imports one or more MySQL servers into a sandbox 
  info            Shows information about dbdeployer environment samples 
  init            initializes dbdeployer environment 
  sandboxes       List installed sandboxes 
  unpack          unpack a tarball into the binary directory 
  update          Gets dbdeployer newest version 
  usage           Shows usage of installed sandboxes 
  use             uses a sandbox 
  versions        List available versions 
 
Flags: 
      --config string           configuration file (default "$HOME/.dbdeployer/config.json") 
  -h, --help                    help for dbdeployer 
      --sandbox-binary string   Binary repository (default "$HOME/opt/mysql") 
      --sandbox-home string     Sandbox deployment directory (default "$HOME/sandboxes") 
      --shell-path string       Which shell to use for generated scripts (default "/usr/local/bin/bash") 
      --skip-library-check      Skip check for needed libraries (may cause nasty errors) 
  -v, --version                 version for dbdeployer 
 
Use "dbdeployer [command] --help" for more information about a command. 
主屏幕中列出的标志可与任何命令一起使用。标志--my-cnf-options和--init-options可以使用多次。

 

开箱
如果的系统中没有安装任何tarball，则应首先安装unpack它（请参见上面的示例）。

$ dbdeployer unpack -h 
 
Usage: 
  dbdeployer unpack MySQL-tarball [flags] 
 
Aliases: 
  unpack, extract, untar, unzip, inflate, expand 
 
Examples: 
    $ dbdeployer unpack mysql-8.0.4-rc-linux-glibc2.12-x86_64.tar.gz 
    Unpacking tarball mysql-8.0.4-rc-linux-glibc2.12-x86_64.tar.gz to $HOME/opt/mysql/8.0.4 
 
    $ dbdeployer unpack --prefix=ps Percona-Server-5.7.21-linux.tar.gz 
    Unpacking tarball Percona-Server-5.7.21-linux.tar.gz to $HOME/opt/mysql/ps5.7.21 
 
    $ dbdeployer unpack --unpack-version=8.0.18 --prefix=bld mysql-mybuild.tar.gz 
    Unpacking tarball mysql-mybuild.tar.gz to $HOME/opt/mysql/bld8.0.18 
 
Flags: 
      --flavor string           Defines the tarball flavor (MySQL, NDB, Percona Server, etc) 
  -h, --help                    help for unpack 
      --overwrite               Overwrite the destination directory if already exists 
      --prefix string           Prefix for the final expanded directory 
      --shell                   Unpack a shell tarball into the corresponding server directory 
      --target-server string    Uses a different server to unpack a shell tarball 
      --unpack-version string   which version is contained in the tarball 
      --verbosity int           Level of verbosity during unpack (0=none, 2=maximum) (default 1) 
 

单一部署
最简单的命令是deploy single，它将安装一个沙箱。

$ dbdeployer deploy -h 
Deploys single, multiple, or replicated sandboxes 
 
Usage: 
  dbdeployer deploy [command] 
 
Available Commands: 
  multiple    create multiple sandbox 
  replication create replication sandbox 
  single      deploys a single sandbox 
 
Flags: 
      --base-port int                   Overrides default base-port (for multiple sandboxes) 
      --base-server-id int              Overrides default server_id (for multiple sandboxes) 
      --binary-version string           Specifies the version when the basedir directory name does not contain it (i.e. it is not x.x.xx) 
      --bind-address string             defines the database bind-address  (default "127.0.0.1") 
      --client-from string              Where to get the client binaries from 
      --concurrent                      Runs multiple sandbox deployments concurrently 
      --custom-mysqld string            Uses an alternative mysqld (must be in the same directory as regular mysqld) 
      --custom-role-extra string        Extra instructions for custom role (8.0+) (default "WITH GRANT OPTION") 
      --custom-role-name string         Name for custom role (8.0+) (default "R_CUSTOM") 
      --custom-role-privileges string   Privileges for custom role (8.0+) (default "ALL PRIVILEGES") 
      --custom-role-target string       Target for custom role (8.0+) (default "*.*") 
  -p, --db-password string              database password (default "msandbox") 
  -u, --db-user string                  database user (default "msandbox") 
      --default-role string             Which role to assign to default user (8.0+) (default "R_DO_IT_ALL") 
      --defaults stringArray            Change defaults on-the-fly (--defaults=label:value) 
      --disable-mysqlx                  Disable MySQLX plugin (8.0.11+) 
      --enable-admin-address            Enables admin address (8.0.14+) 
      --enable-general-log              Enables general log for the sandbox (MySQL 5.1+) 
      --enable-mysqlx                   Enables MySQLX plugin (5.7.12+) 
      --expose-dd-tables                In MySQL 8.0+ shows data dictionary tables 
      --flavor string                   Defines the tarball flavor (MySQL, NDB, Percona Server, etc) 
      --flavor-in-prompt                Add flavor values to prompt 
      --force                           If a destination sandbox already exists, it will be overwritten 
      --gtid                            enables GTID 
  -h, --help                            help for deploy 
      --history-dir string              Where to store mysql client history (default: in sandbox directory) 
      --init-general-log                uses general log during initialization (MySQL 5.1+) 
  -i, --init-options stringArray        mysqld options to run during initialization 
      --keep-server-uuid                Does not change the server UUID 
      --log-directory string            Where to store dbdeployer logs (default "$HOME/sandboxes/logs") 
      --log-sb-operations               Logs sandbox operations to a file 
      --my-cnf-file string              Alternative source file for my.sandbox.cnf 
  -c, --my-cnf-options stringArray      mysqld options to add to my.sandbox.cnf 
      --native-auth-plugin              in 8.0.4+, uses the native password auth plugin 
      --port int                        Overrides default port 
      --port-as-server-id               Use the port number as server ID 
      --post-grants-sql stringArray     SQL queries to run after loading grants 
      --post-grants-sql-file string     SQL file to run after loading grants 
      --pre-grants-sql stringArray      SQL queries to run before loading grants 
      --pre-grants-sql-file string      SQL file to run before loading grants 
      --remote-access string            defines the database access  (default "127.%") 
      --repl-crash-safe                 enables Replication crash safe 
      --rpl-password string             replication password (default "rsandbox") 
      --rpl-user string                 replication user (default "rsandbox") 
      --sandbox-directory string        Changes the default name of the sandbox directory 
      --skip-load-grants                Does not load the grants 
      --skip-report-host                Does not include report host in my.sandbox.cnf 
      --skip-report-port                Does not include report port in my.sandbox.cnf 
      --skip-start                      Does not start the database server 
      --socket-in-datadir               Create socket in datadir instead of $TMPDIR 
      --task-user string                Task user to be created (8.0+) 
      --task-user-role string           Role to be assigned to task user (8.0+) 
      --use-template stringArray        [template_name:file_name] Replace existing template with one from file 
 
$ dbdeployer deploy single -h 
For example: 
	dbdeployer deploy single 5.7     # deploys the latest release of 5.7.x 
	dbdeployer deploy single 5.7.21  # deploys a specific release 
	dbdeployer deploy single /path/to/5.7.21  # deploys a specific release in a given path 
 
For this command to work, there must be a directory $HOME/opt/mysql/5.7.21, containing 
the binary files from mysql-5.7.21-$YOUR_OS-x86_64.tar.gz 
Use the "unpack" command to get the tarball into the right directory. 
 
Usage: 
  dbdeployer deploy single MySQL-Version [flags] 
 
Flags: 
  -h, --help            help for single 
      --master          Make the server replication ready 
      --prompt string   Default prompt for the single client (default "mysql") 
      --server-id int   Overwrite default server-id 
 

部署多个
如果要使用多个相同版本的沙箱，且没有任何复制关系，请使用deploy multiple带有可选--nodes标志的命令（默认值：3）。

$ dbdeployer deploy multiple -h 
Creates several sandboxes of the same version,without any replication relationship. 
For this command to work, there must be a directory $HOME/opt/mysql/5.7.21, containing the binary files from mysql-5.7.21-$YOUR_OS-x86_64.tar.gz 
Use the "unpack" command to get the tarball into the right directory. 
 
Usage: 
  dbdeployer deploy multiple MySQL-Version [flags] 
 
Examples: 
 
	$ dbdeployer deploy multiple 5.7.21 
	 
Flags: 
  -h, --help        help for multiple 
  -n, --nodes int   How many nodes will be installed (default 3) 
 

部署复制
该deploy replication命令将安装一个主服务器和两个或多个从服务器，并开始复制。可以将拓扑更改为分组并在对等复制中获得三个节点，或者使用all-masters或fan-in组成多源拓扑。

$ dbdeployer deploy replication -h 
The replication command allows you to deploy several nodes in replication. 
Allowed topologies are "master-slave" for all versions, and  "group", "all-masters", "fan-in" for  5.7.17+. 
Topologies "pcx" and "ndb" are available for binaries of type Percona Xtradb Cluster and MySQL Cluster. 
For this command to work, there must be a directory $HOME/opt/mysql/5.7.21, containing the binary files from mysql-5.7.21-$YOUR_OS-x86_64.tar.gz 
Use the "unpack" command to get the tarball into the right directory. 
 
Usage: 
  dbdeployer deploy replication MySQL-Version [flags] 
 
Examples: 
		$ dbdeployer deploy replication 5.7    # deploys highest revision for 5.7 
		$ dbdeployer deploy replication 5.7.21 # deploys a specific revision 
		$ dbdeployer deploy replication /path/to/5.7.21 # deploys a specific revision in a given path 
		# (implies topology = master-slave) 
 
		$ dbdeployer deploy --topology=master-slave replication 5.7 
		# (explicitly setting topology) 
 
		$ dbdeployer deploy --topology=group replication 5.7 
		$ dbdeployer deploy --topology=group replication 8.0 --single-primary 
		$ dbdeployer deploy --topology=all-masters replication 5.7 
		$ dbdeployer deploy --topology=fan-in replication 5.7 
		$ dbdeployer deploy --topology=pxc replication pxc5.7.25 
		$ dbdeployer deploy --topology=ndb replication ndb8.0.14 

Flags: 
      --change-master-options stringArray   options to add to CHANGE MASTER TO 
  -h, --help                                help for replication 
      --master-ip string                    Which IP the slaves will connect to (default "127.0.0.1") 
      --master-list string                  Which nodes are masters in a multi-source deployment 
      --ndb-nodes int                       How many NDB nodes will be installed (default 3) 
  -n, --nodes int                           How many nodes will be installed (default 3) 
      --read-only-slaves                    Set read-only for slaves 
      --repl-history-dir                    uses the replication directory to store mysql client history 
      --semi-sync                           Use semi-synchronous plugin 
      --single-primary                      Using single primary for group replication 
      --slave-list string                   Which nodes are slaves in a multi-source deployment 
      --super-read-only-slaves              Set super-read-only for slaves 
  -t, --topology string                     Which topology will be installed (default "master-slave") 
从1.21.0版开始，可以使用Percona Xtradb Cluster压缩包来部署pxc类型的复制。此部署仅适用于Linux。

 

重新部署沙箱
如果第二次运行deploy语句，则该命令将失败，因为沙箱已经存在。要克服此限制，可以使用选项重复操作--force。

$ dbdeployer deploy  single 8.0 
# 8.0 => 8.0.22 
Database installed in $HOME/sandboxes/msb_8_0_22 
run 'dbdeployer usage single' for basic instructions' 
. sandbox server started 
 
$ dbdeployer deploy  single 8.0 
# 8.0 => 8.0.22 
error creating sandbox: 'check directory directory $HOME/sandboxes/msb_8_0_22 already exists. Use --force to override' 
 
$ dbdeployer deploy  single 8.0 --force 
# 8.0 => 8.0.22 
Overwriting directory $HOME/sandboxes/msb_8_0_22 
stop $HOME/sandboxes/msb_8_0_22 
Database installed in $HOME/sandboxes/msb_8_0_22 
run 'dbdeployer usage single' for basic instructions' 
. sandbox server started 
为了更快地重置，所有单个沙箱都具有一个command wipe_and_restart。复制沙箱（NDB和PXC除外）具有command wipe_and_restart_all。运行这样的命令将删除所有节点中的数据目录，然后重新创建它们。

$ ~/sandboxes/msb_8_0_22/wipe_and_restart 
Terminating the server immediately --- kill -9 72889 
[...] 
Database installed in /home/gmax/sandboxes/msb_8_0_22 
. sandbox server started 
 
$ ~/sandboxes/rsandbox_8_0_22/wipe_and_restart_all 
# executing 'send_kill' on /home/gmax/sandboxes/rsandbox_8_0_22 
[...] 
Database installed in /home/gmax/sandboxes/rsandbox_8_0_22/master 
[...] 
Database installed in /home/gmax/sandboxes/rsandbox_8_0_22/node1 
[...] 
Database installed in /home/gmax/sandboxes/rsandbox_8_0_22/node2 
[...] 
# executing 'start' on /home/gmax/sandboxes/rsandbox_8_0_22 
executing 'start' on master 
. sandbox server started 
executing 'start' on slave 1 
. sandbox server started 
executing 'start' on slave 2 
. sandbox server started 
initializing slave 1 
initializing slave 2




 

 

数据库用户
dbdeployer部署的每个服务器的默认用户是：

root，默认授权由安装的服务器版本提供。
msandbox，具有除GRANT选项以外的所有特权。
msandbox_rw，具有最小的读/写权限。
msandbox_ro，具有只读权限。
rsandbox，仅复制相关权限（密码：rsandbox）
可以使用和分别更改主要用户名（msandbox）和密码（msandbox）。--db-user db-password 

默认情况下，每个用户都被分配到一个有限的范围（127.%），以便他们只能与本地主机通信。范围可以使用选项--bind-address和更改--remote-access。

在MySQL 8.0中，上述用户是使用角色实例化的。还可以定义一个自定义角色，并将其分配给主要用户。

可以创建其他角色，并使用以下选项将其分配给默认用户：

dbdeployer deploy single 8.0.19 \ 
    --custom-role-name=R_POWERFUL \ 
    --custom-role-privileges='ALL PRIVILEGES' \ 
    --custom-role-target='*.*' \ 
    --custom-role-extra='WITH GRANT OPTION' \ 
    --default-role=R_POWERFUL \ 
    --bind-address=0.0.0.0 \ 
    --remote-access='%' \ 
    --db-user=differentuser \ 
    --db-password=somethingdifferent 
该操作的结果将是：

$ ~/sandboxes/msb_8_0_19/use 
Welcome to the MySQL monitor.  Commands end with ; or \g. 
Your MySQL connection id is 9 
Server version: 8.0.19 MySQL Community Server - GPL 
 
Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved. 
 
Oracle is a registered trademark of Oracle Corporation and/or its 
affiliates. Other names may be trademarks of their respective 
owners. 
 
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement. 
 
mysql [localhost:8019] {differentuser} ((none)) > show grants\G 
*************************** 1. row *************************** 
Grants for differentuser@localhost: GRANT SELECT, INSERT, UPDATE, DELETE, CREATE,  
DROP, RELOAD, SHUTDOWN, PROCESS, FILE, REFERENCES, INDEX, ALTER, SHOW DATABASES,  
SUPER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT,  
CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER,  
CREATE TABLESPACE, CREATE ROLE, DROP ROLE ON *.* TO `differentuser`@`localhost` WITH GRANT OPTION 
*************************** 2. row *************************** 
Grants for differentuser@localhost: GRANT APPLICATION_PASSWORD_ADMIN,AUDIT_ADMIN,BACKUP_ADMIN,BINLOG_ADMIN, 
BINLOG_ENCRYPTION_ADMIN,CLONE_ADMIN,CONNECTION_ADMIN,ENCRYPTION_KEY_ADMIN,GROUP_REPLICATION_ADMIN, 
INNODB_REDO_LOG_ARCHIVE,PERSIST_RO_VARIABLES_ADMIN,REPLICATION_APPLIER,REPLICATION_SLAVE_ADMIN, 
RESOURCE_GROUP_ADMIN,RESOURCE_GROUP_USER,ROLE_ADMIN,SERVICE_CONNECTION_ADMIN,SESSION_VARIABLES_ADMIN, 
SET_USER_ID,SYSTEM_USER,SYSTEM_VARIABLES_ADMIN,TABLE_ENCRYPTION_ADMIN,XA_RECOVER_ADMIN  
ON *.* TO `differentuser`@`localhost` WITH GRANT OPTION 
*************************** 3. row *************************** 
Grants for differentuser@localhost: GRANT `R_POWERFUL`@`%` TO `differentuser`@`localhost` 
3 rows in set (0.01 sec) 
除了将自定义角色分配给默认用户外，还可以创建任务用户。

$ dbdeployer deploy single 8.0 --task-user=task_user --custom-role-name=R_ADMIN --task-user-role=R_ADMIN  
本节中显示的选项仅适用于MySQL 8.0。

有一种方法可以在任何版本的部署期间创建用户：

创建一个包含要运行的CREATE USERandGRANT语句的SQL文件
使用该选项--post-grants-sql-file加载说明。
cat << EOF > orchestrator.sql 
CREATE DATABASE IF NOT EXISTS orchestrator; 
CREATE USER orchestrator IDENTIFIED BY 'msandbox'; 
GRANT ALL PRIVILEGES ON orchestrator.* TO orchestrator; 
GRANT SELECT ON mysql.slave_master_info TO orchestrator; 
EOF 
 
$ dbdeployer deploy single 5.7 --post-grants-sql-file=$PWD/orchestrator.sql
 

 

 

数据库服务器风格
在1.19.0版之前，dbdeployer假定它正在处理某个版本的MySQL，并使用该版本来确定它将支持的功能。在1.19.0版中，dbdeployer开始使用功能的概念，它是服务器风格+一个版本的组合。当前支持的某些风格

mysql ：经典的MySQL服务器
percona：Percona Server，任何版本。为了进行部署，它具有与MySQL相同的功能
mariadb：MariaDB服务器。与MySQL大致相同，但部署方法有所不同。
pxc：Percona Xtradb群集
ndb：MySQL丛集（NDB）
tidb：独立的TiDB服务器。
要查看每种风格可以做什么，可以使用命令dbdeployer admin capabilities。

要查看给定风格的特征：dbdeployer admin capabilities FLAVOR。

要查看给定版本的风格可以做什么，可以使用dbdeployer admin capabilities FLAVOR VERSION。

For example

$ dbdeployer admin capabilities
$ dbdeployer admin capabilities percona
$ dbdeployer admin capabilities mysql 5.7.11
 

 

 

 

 

 

多个沙箱，相同版本和类型
如果要部署具有相同版本和相同类型的多个实例（例如，两个8.0.4的沙盒，或两个具有不同设置的复制实例），则可以手动指定数据目录名称和端口。

$ dbdeployer deploy single 8.0.4 
# will deploy in msb_8_0_4 using port 8004 
 
$ dbdeployer deploy single 8.0.4 --sandbox-directory=msb2_8_0_4 
# will deploy in msb2_8_0_4 using port 8005 (which dbdeployer detects and uses) 
 
$ dbdeployer deploy replication 8.0.4 --concurrent 
# will deploy replication in rsandbox_8_0_4 using default calculated ports 19009, 19010, 19011 
 
$ dbdeployer deploy replication 8.0.4 --gtid --sandbox-directory=rsandbox2_8_0_4 --base-port=18600 --concurrent 
# will deploy replication in rsandbox2_8_0_4 using ports 18601, 18602, 18603
 

 

使用通往扩展的tarball的直接路径
如果具有扩展的tarball的自定义组织，则可能要使用二进制文件的直接路径，而不要使用--sandbox-binary和版本名的组合。

例如，假设的二进制文件组织如下：

$HOME/opt/ 
         /percona/ 
                 /5.7.21 
                 /5.7.22 
                 /8.0.11 
        /mysql/ 
              /5.7.21 
              /5.7.22 
              /8.0.11 
可以使用以下任何一种方法为Percona服务器5.7.22版部署单个沙箱：

#1  dbdeployer deploy single --sandbox-binary=$HOME/opt/percona 5.7.22 
 
#2  dbdeployer deploy single $HOME/opt/percona/5.7.22 
 
#3  dbdeployer defaults update sandbox-binary $HOME/opt/percona 
     dbdeployer deploy single 5.7.22 
 
#4  export SANDBOX_BINARY=$HOME/opt/percona 
     dbdeployer deploy single 5.7.22 
方法＃1和＃2是等效的。他们将沙箱二进制目录临时设置为一个新目录，并将其用于当前部署

方法＃3和＃4将永久设置沙箱二进制目录，不同之处在于，在设置＃4时，系统范围内对dbdeployer的所有调用都设置了＃3（在另一个终端窗口中，它将使用新值）。仅用于当前会话（在其他终端窗口中，它将仍然使用默认会话。）

请注意，使用这种类型的组织可能会在部署期间看到冲突。例如，在安装Percona Server 5.7.22之后，如果要安装MySQL 5.7.22，则需要--sandbox-directory明确指定。相反，如果使用“标准和非标准名称”中定义的前缀方法，则应避免冲突。

 

 

端口管理
dbdeployer会尽可能尝试为每个沙箱使用默认端口。对于单个沙箱，该端口将是不带点的版本号：5.7.22将部署在端口5722上。对于多个沙箱，该端口号是通过使用前缀号（在默认值中可见dbdeployer defaults list）+端口号+来定义的。版本号（对于某些拓扑乘以100。）例如，使用MySQL 8.0.11的单主组复制将计算如下端口：

base port = 8011 (version number) + 13000 (prefix) + 11 (revision) * 100  = 22111
node1 port = base port + 1 = 22112
node2 port = base port + 2 = 22113
node3 port = base port + 2 = 22114
对于组复制，我们需要计算组端口，并使用group-port-delta（= 125）从常规端口获取它：

node1 group port = 22112 + 125 = 22237
node2 group port = 22113 + 125 = 22238
node3 group port = 22114 + 125 = 22239
对于MySQL 8.0.11+，我们还需要为XPlugin分配一个端口，并使用常规端口+ mysqlx-port-delta（= 10000）进行计算。

因此，对于MySQL 8.0.11组复制部署，将看到以下清单：

$ dbdeployer sandboxes --header
name                   type                  version  ports
----------------       -------               -------  -----
group_msb_8_0_11     : group-multi-primary    8.0.11 [20023 20148 30023 20024 20149 30024 20025 20150 30025]
group_sp_msb_8_0_11  : group-single-primary   8.0.11 [22112 22237 32112 22113 22238 32113 22114 22239 32114]
当在不同的部署中使用相同版本时，此方法使端口冲突变得不太可能，但是在部署多个相邻版本的沙箱时，存在端口冲突的风险。此外，dbdeployer不允许发生冲突。借助其沙箱的中央目录，它可以知道已经使用了哪些端口，并且每当检测到潜在冲突时都将搜索可用的端口。请记住，“二手”的概念仅与沙箱有关。dbdeployer不知道其他应用程序是否可以使用端口。可以通过告诉dbdeployer哪些端口可能被占用来最大程度地降低风险。默认值包含一个字段reserved-ports，其中包含不应使用的端口。可以通过修改默认值添加到该列表。例如，如果要排除使用端口7001、10000和15000，

dbdeployer defaults update reserved-ports '7001,10000,15000'
或者，如果要保留默认情况下保留的内容，请执行以下操作：

dbdeployer defaults update reserved-ports '1186,3306,33060,7001,10000,15000'
 

 

 

并发部署和删除
从0.3.0版本开始，dbdeployer可以部署带有标志的沙箱组（deploy replication，deploy multiple）--concurrent。使用此标志时，dbdeployer将同时运行操作。该标志可以与delete命令一起使用。一次删除多个沙箱时，此功能很有用。并发操作的速度比顺序操作快2到5倍，具体取决于服务器的版本和节点数。

 

 

复制拓扑
可以使用具有多种拓扑的复制来部署多个沙箱（使用dbdeployer deploy replication --topology=xxxxx：

主从是默认拓扑。它将安装一个主机和两个从机。可以通过option添加更多的slave --nodes。
组将在组复制中部署三个对等节点。如果要使用单个主部署，请添加选项--single-primary。适用于MySQL 5.7和更高版本。
扇入是主从的对立面。在这里，我们有一个奴隶和几个主人。此拓扑要求MySQL 5.7或更高版本。
all-masters是扇入的一种特殊情况，其中所有节点都是主节点，也是所有节点的从属节点。
可以在多源拓扑中调整数据流。扇入的默认值为三个节点，其中1和2是主节点，2是从节点。可以通过提供组件列表来更改预定义设置：

$ dbdeployer deploy replication --topology=fan-in --nodes=5 --master-list="1,2 3" --slave-list="4,5" 8.0.4 --concurrent 
在上面的示例中，我们得到5个节点，而不是3个。前三个是master（--master-list="1,2,3"），后两个是slave（--slave-list="4,5"），它们将从所有master接收数据。自动生成一个测试来检查复制流程。在我们的例子中，它显示以下内容：

$ ~/sandboxes/fan_in_msb_8_0_4/test_replication 
# master 1 
# master 2 
# master 3 
# slave 4 
ok - '3' == '3' - Slaves received tables from all masters 
# slave 5 
ok - '3' == '3' - Slaves received tables from all masters 
# pass: 2 
# fail: 0 
前三行显示每个主控已完成某项操作。在我们的例子中，每个主数据库都创建了一个不同的表。然后，节点5和6中的从站会统计发现的表数量，如果它们是从所有主服务器获得的表，则测试成功。

另外两个拓扑，ndb和pxc需要专用格式的二进制文件，分别是MySQL Cluster和Percona Xtradb Cluster。dbdeployer会检测扩展的tarball是否满足风味要求，并仅在满足条件时才进行部署。

 

 

 

 

跳过服务器启动
默认情况下，部署沙箱时，服务器会启动，并且自动执行完成拓扑的其他操作。使用该--skip-start选项可以跳过服务器启动。使用此选项时，服务器将初始化，但不会启动。因此，不会创建默认用户，并且手动启动数据库时，只能使用root没有密码的用户访问。

如果使用进行部署--skip-start，则可以手动运行其余操作：

$ dbdeployer deploy single --skip-start 5.7.21 
$ $HOME/sandboxes/msb_5_7_21/start 
$ $HOME/sandboxes/msb_5_7_21/load_grants 
可以对复制进行相同的操作，但是还需要运行初始化从属服务器的附加步骤：

$ dbdeployer deploy replication --skip-start 5.7.21 --concurrent 
$ $HOME/sandboxes/rsandbox_5_7_21/start_all 
$ $HOME/sandboxes/rsandbox_5_7_21/master/load_grants 
# NOTE: only the master needs to load grants. The slaves receive the grants through replication 
$ $HOME/sandboxes/rsandbox_5_7_21/initialize_slaves 
同样，对于组复制

$ dbdeployer deploy replication --skip-start 5.7.21 --topology=group --concurrent 
$ $HOME/sandboxes/group_msb_5_7_21/start_all 
$ $HOME/sandboxes/group_msb_5_7_21/node1/load_grants 
$ $HOME/sandboxes/group_msb_5_7_21/node2/load_grants 
$ $HOME/sandboxes/group_msb_5_7_21/node3/load_grants 
$ $HOME/sandboxes/rsandbox_5_7_21/initialize_nodes 
警告：--skip-start高级用户提供了运行沙箱，不建议使用。如果跳过开始的目的是在沙盒授予操作之前检查服务器，则可以考虑使用--pre-grants-sql并--pre-grants-sql-file运行必要的SQL命令（请参阅下面的沙盒自定义）。

 

 

 

MySQL文档存储，mysqlsh和默认值。
MySQL 5.7.12+引入了XPlugin（又名mysqlx），它允许在特殊表上使用单独的端口（默认为33060）进行操作，这些特殊表可被视为NoSQL集合。在MySQL 8.0.11+中，默认情况下启用XPlugin，这使dbdeployer可以为该服务定义附加端口和套接字。部署MySQL 8.0.11或更高版本时，dbdeployer将设置mysqlx-port为常规端口+ mysqlx-delta-port（= 10000）的值。

如果要避免启用XPlugin，则可以使用选项部署沙箱--disable-mysqlx。

对于介于5.7.12和8.0.4之间的MySQL，方法是相反的。默认情况下，XPlugin是禁用的，如果要使用XPlugin，则将使用来运行部署--enable-mysqlx。在这两种情况下，端口和套接字都将由dbdeployer计算。

启用XPlugin后，使用MySQL Shell有意义，并且dbdeployer将为mysqlsh使用插件的沙箱创建脚本。不幸的是，截至今天（2018年4月下旬），MySQL shell尚未随服务器tarball一起发布，因此我们必须手动修复（参见下一部分。）dbdeployer将mysqlsh在其他客户端所在的目录中查找，因此如果手动合并mysql shell和服务器tarball，将获得适当版本的MySQL shell。如果没有，将使用中提供的外壳程序版本$PATH。如果没有可用的MySQL Shell，则会出现错误。

 

 

安装MySQL Shell
MySQL Shell作为tarball分发。可以使用dbdeployer（从1.9.0版开始）将其安装在服务器二进制文件目录中。

最简单的操作是：

$ dbdeployer unpack --shell mysql-shell-8.0.12-$YOUR_OS.tar.gz 
如果MySQL 8.0.12已经解压，则此命令将起作用$SANDBOX_BINARY/8.0.12。dbdeployer可以从tarball名称中识别版本，并查找相应的服务器。如果找到，则将临时扩展shell软件包，并将必要的文件移至服务器目录树中。

如果相应的服务器目录不存在，则可以指定所需的目标：

$ dbdeployer unpack --shell mysql-shell-8.0.12-$YOUR_OS.tar.gz --target-server=5.7.23 
由于MySQL团队建议即使对于旧版本的MySQL也使用最新的Shell，我们可以将8.0.12的Shell插入5.7服务器，并且它将按预期工作，因为Shell附带了所有需要的文件。使用该选项，--verbosity=2我们可以看到提取了哪些文件以及每个组件的复制或移动位置。请注意，该操作完成后，将删除解压缩的MySQL Shell目录。

 

 

 

数据库日志管理。
有时，在使用沙箱进行测试时，有必要在初始化过程中或常规操作中启用常规日志。从1.4.0版开始，可以使用--my-cnf-options=general-log=1或来实现此目的，但是--my-init-options=--general-log=1有两个简单的布尔快捷方式：--init-general-log并在--enable-general-log需要时启动常规日志。

此外，每个沙箱都有一个名为的便捷脚本show_log，可以轻松显示错误日志或常规日志。运行./show_log -h以获取使用情况信息。

对于复制，在每个沙箱中还具有show_binlog和show_relaylog作为快捷方式，可以轻松显示复制日志。

 

 

 

 

dbdeployer操作日志记录
除了启用数据库日志之外，还可以拥有dbdeployer在构建和激活沙箱时执行的操作的日志。默认情况下禁用日志。可以使用启用它们以进行给定的操作--log-sb-operations。启用日志后，dbdeployer将在目录下的目录中创建一个或多个日志文件$HOME/sandboxes/logs。对于单个沙箱，日志目录将命名为single_v_v_vv-xxxx，其中v_v_vv是版本号，并且xxxx是dbdeployer进程ID。在目录内，将有一个文件名single.log。

对于复制沙箱，将命名该目录，并且该目录将replication_v_v_vv-xxxx至少包含3个文件：master-slave-replication.log具有复制操作，以及两个名为的单个沙箱（一个用于主目录，一个用于从属目录）日志replication-node-x.log。如果有多个从站，则每个从站都有自己的日志。

dbdeployer日志将记录哪个函数运行了哪个操作，以及用于单个沙箱和复合沙箱的数据。

日志的名称sbdescription.json位于每个沙箱中的文件内。如果禁用了日志记录，则不会列出日志字段。

日志将保留，直到删除相应的沙箱为止。

可以使用默认值永久启用日志记录dbdeployer defaults update log-sb-operations true。同样，可以通过一次操作（--log-directory=...）或永久（dbdeployer defaults update log-directory /my/path/to/logs）更改日志目录

日志中包含哪些信息？在其中找到的最重要的内容是用于填充模板的数据。如果出了什么问题，数据应该为我们指明正确的方向。日志还记录dbdeployer做出的几种选择的结果，例如启用给定端口或将这样的选项添加到配置文件中。即使没有任何问题，日志也可以使好奇的用户了解我们部署的配置比平时少时会发生什么，以及可以使用哪些模板和选项来更改结果。

 

 

沙盒自定义
有几种更改沙箱默认行为的方法。

可以使用将选项添加到正在部署的沙箱中--my-cnf-options="some mysqld directive"。此选项可以多次使用。提供的选项已添加到my.sandbox.cnf
可以指定my.cnf模板（--my-cnf-file=filename）而不是逐行定义选项。dbdeployer将跳过沙箱功能所需的所有选项。
可以之前或补助加载后（运行SQL语句或SQL文件--pre-grants-sql，--pre-grants-sql-file等等）。还可以使用这些选项来查看沙盒的状态，并查看每个阶段发生的情况。
对于更高级的需求，可以查看用于部署的模板，并加载自己的模板，而不是原始的s（--use-template=TemplateName:FileName。）。
例如：

$ dbdeployer deploy single 5.6.33 --my-cnf-options="general_log=1" --pre-grants-sql="select host, user, password from mysql.user" --post-grants-sql="select @@general_log" 
$ dbdeployer defaults templates list 
$ dbdeployer defaults templates show templateName > mytemplate.txt 
# edit the template 
$ dbdeployer deploy single --use-template=templateName:mytemplate.txt 5.7.21 
dbdeployer将使用的模板而不是原始模板。

还可以导出模板，对其进行编辑，然后要求dbdeployer编辑的更改。
例：

$ dbdeployer defaults templates export single my_templates  # Will export all the templates for the "single" group to the direcory my_templates/single 
$ dbdeployer defaults templates export ALL my_templates  # exports all templates into my_templates, one directory for each group  # Edit the templates that you want to change. You can also remove the ones that you want to leave untouched. 
$ dbdeployer defaults templates import single my_templates  # Will import all templates from my_templates/single 
警告：修改模板可能会阻止沙箱的正常工作。请谨慎使用此功能！

最后，可以使用“默认值”命令来修改应用程序的默认值。可以导出默认值，从修改后的JSON文件导入它们，或者动态更新一个。
这是如何做：

$ dbdeployer defaults show 
# Internal values: 
{
     “ version ”：“ 1.5.0 ”，
     “ sandbox-home ”：“ $ HOME / sandboxes ”，
     “ sandbox-binary ”：“ $ HOME / opt / mysql ”，
     “ use-sandbox-catalog ”：true，
     “ master-slave-base-port “：11000，
     ” group-replication-base-port “：12000，
     ”组复制sp-base-port “：13000，
     “ fan-in-replication-base-port ”：14000，
     “ all-masters-replication-base-port ”：15000，
     “ multiple-base-port ”：16000，
     “ group-port-delta ”：125，
     “ mysqlx-port-delta ”：10000，
     “ master-name ”：“ master ”，
     “ master-abbr ”：“米“，
     ” node-prefix “： ” node “，
     ” slave-prefix “： ” slave “，
     ” slave-abbr “： ” s “，
     ” sandbox-prefix “： ” msb_ “，
     ” master-slave-prefix “： “ rsandbox_ ”，
     “组前缀”： “group_msb_ “，
     ”group-sp-prefix “：” group_sp_msb_ “，
     ”   multi -prefix “：” multi_msb_ “，
     ” fan-in-prefix “：” fan_in_msb_ “，
     ” all-masters-prefix “：” all_masters_msb_ “，
     ”保留端口“：[
         1186，
         3306，
         33060 
    ]， 
    “ timestamp ”： “ CEST 2018年5月12日星期六14:37:53 ” 
 }
$ dbdeployer defaults update master-slave-base-port 15000 
# Updated master-slave-base-port -> "15000" 
# Configuration file: $HOME/.dbdeployer/config.json 
{
     “ version ”：“ 1.5.0 ”，
     “ sandbox-home ”：“ $ HOME / sandboxes ”，
     “ sandbox-binary ”：“ $ HOME / opt / mysql ”，
     “ use-sandbox-catalog ”：true，
     “ master-slave-base-port “：15000，
     ” group-replication-base-port “：12000，
     ”组复制sp-base-port “：13000，
     “ fan-in-replication-base-port ”：14000，
     “ all-masters-replication-base-port ”：15000，
     “ multiple-base-port ”：16000，
     “ group-port-delta ”：125，
     “ mysqlx-port-delta ”：10000，
     “ master-name ”：“ master ”，
     “ master-abbr ”：“米“，
     ” node-prefix “： ” node “，
     ” slave-prefix “： ” slave “，
     ” slave-abbr “： ” s “，
     ” sandbox-prefix “： ” msb_ “，
     ” master-slave-prefix “： “ rsandbox_ ”，
     “组前缀”： “group_msb_ “，
     ”group-sp-prefix “：” group_sp_msb_ “，
     ”   multi -prefix “：” multi_msb_ “，
     ” fan-in-prefix “：” fan_in_msb_ “，
     ” all-masters-prefix “：” all_masters_msb_ “，
     ”保留端口“：[
         1186，
         3306，
         33060 
    ]， 
    “ timestamp ”： “ CEST 2018年5月12日星期六14:37:53 ” 
}
修改默认值（不将新值存储在dbdeployer的配置文件中）的另一种方法是通过该--defaults标志。上述更改可以这样完成：

$ dbdeployer --defaults=master-slave-base-port:15000 deploy replication 5.7.21 
不同之处在于，dbdeployer defaults update对于下一个命令或直到运行之前，使用该值会永久更改dbdeployer defaults reset。--defaults相反，使用该标志将仅修改活动命令的默认值。

 

 

 

 

 

 

沙箱管理
可以列出可用的MySQL版本

$ dbdeployer versions       # Alias: available
（可选）可以仅索取给定风味的版本（dndeployer versions --flavor=ndb），也可以显示风格不同的所有版本（dbdeployer versions --by-flavor）

可以列出已安装的沙箱

$ dbdeployer sandboxes       # Aliases: installed, deployed
命令“用法”显示了如何使用随每个沙箱安装的脚本。

$ dbdeployer usage 
 
	USING A SANDBOX 
 
Change directory to the newly created one (default: $SANDBOX_HOME/msb_VERSION for single sandboxes) 
[ $SANDBOX_HOME = $HOME/sandboxes unless modified with flag --sandbox-home ] 
 
刚才创建的实例的沙箱目录包含一些方便的脚本，可以方便地独立管理服务器。
 
"./start", "./status", "./restart", and "./stop" do what their name suggests.  
start and restart accept parameters that are eventually passed to the server.  
e.g.: 
  ./start --server-id=1001 
  ./restart --event-scheduler=disabled 
 
"./use" calls the command line client with the appropriate parameters, 
Example: 
 
    ./use -BN -e "select @@server_id" 
    ./use -u root 
 
"./clear" stops the server and removes everything from the data directory, letting you ready to start from scratch. (Warning! It's irreversible!) 
 
"./send_kill [destroy]" does almost the same as "./stop", as it sends a SIGTERM (-15) kill to shut down the server. Additionally, when the regular kill fails, it will send an unfriendly SIGKILL (-9) to the unresponsive server. 
The argument "destroy" will immediately kill the server with SIGKILL (-9). 
 
"./add_option" will add one or more options to my.sandbox.cnf, and restarts the server to apply the changes. 
 
"init_db" and "load_grants" are used during the server initialization, and should not be used in normal operations. They are nonetheless useful to see which operations were performed to set up the server. 
 
"./show_binlog" and "./show_relaylog" will show the latest binary log or relay-log. 
 
"./my" is a prefix script to invoke any command named "my*" from the MySQL /bin directory. It is important to use it rather than the corresponding globally installed tool, because this guarantees that you will be using the tool for the version you have deployed. 
Examples: 
 
    ./my sqldump db_name 
    ./my sqlbinlog somefile 
 
"./mysqlsh" invokes the mysql shell. Unlike other commands, this one only works if mysqlsh was installed, with preference to the binaries found in "basedir". 
This script is created only if the X plugin was enabled (5.7.12+ with --enable-mysqlx or 8.0.11+ without --disable-mysqlx) 
 
"./use_admin" is created when the sandbox is deployed with --enable-admin-address (8.0.14+) and allows using the database as administrator, with a dedicated port. 
 
 USING MULTIPLE SERVER SANDBOX 
On a replication sandbox, you have the same commands (run "dbdeployer usage single"), with an "_all" suffix, meaning that you propagate the command to all the members.  
Then you have "./m" as a shortcut to use the master, "./s1" and "./s2" to access the slaves (and "s3", "s4" ... if you define more). 
 
In group sandboxes without a master slave relationship (group replication and multiple sandboxes) the nodes can be accessed by ./n1, ./n2, ./n3, and so on. 
 
start_all    [options] > starts all nodes 
status_all             > get the status of all nodes 
restart_all  [options] > restarts all nodes 
stop_all               > stops all nodes 
use_all         "SQL"  > runs a SQL statement in all nodes 
use_all_masters "SQL"  > runs a SQL statement in all masters 
use_all_slaves "SQL"   > runs a SQL statement in all slaves 
clear_all              > stops all nodes and removes all data 
m                      > invokes MySQL client in the master 
s1, s2, n1, n2         > invokes MySQL client in slave 1, 2, node 1, 2 
 
The scripts "check_slaves" or "check_nodes" give the status of replication in the sandbox. 
When the sandbox is deployed with --enable-admin-address (8.0.14+) the following scripts are also created: 
 
ma                    > invokes the MySQL client in the master as admin 
sa1, sa2, na1, na2    > invokes MySQL client as admin in slave 1, 2, node 1, 2 
use_all_admin "SQL"   > runs a SQL statement in all nodes as admin 
 

每个沙盒都有一个名为的文件sbdescription.json，其中包含有关沙盒的重要信息。确定二进制文件的来源和安装条件是很有用的。

例如，单个沙箱的描述文件将显示：

{
     “ basedir ”：“ /home/dbuser/opt/mysql/5.7.22 ”，
     “ type ”：“ single ”，
     “ version ”：“ 5.7.22 ”，
     “ port ”：[
         5722 
    ]， 
    “节点”： 0，
     “ node_num ”： 0，
     “ dbdeployer-version ”： “ 1.5.0 ”，
     “时间戳”： “ SEST May 12 14:26:41 CEST 2018 ”，
     “命令行”： “ dbdeployer部署单5.7.22 “ 
}
对于复制：

{
     “ BASEDIR ”：“ /home/dbuser/opt/mysql/5.7.22 ”，
     “类型”：“主从”，
     “版本”：“ 22年5月7日”，
     “端口”：[
         16745，
         16746，
         16747 
    ]， 
    “节点”： 2，
     “ node_num ”： 0，
     “ dbdeployer-version ”： “ 1.5.0 ”，
     “时间戳”： “ CEST 2018年5月12日14:27:04 ”，
     “命令行”： “ dbdeployer部署复制5.7.22 --gtid --concurrent “ 
}
 

 

 

 

沙箱宏操作
可以使用命令同时在多个沙箱中运行命令，该命令会将global命令传播到所有已安装的沙箱。

$ dbdeployer global -h 
This command can propagate the given action through all sandboxes.

Usage:
  dbdeployer global [command]

Examples:

	$ dbdeployer global use "select version()"
	$ dbdeployer global status
	$ dbdeployer global stop --version=5.7.27
	$ dbdeployer global stop --short-version=8.0
	$ dbdeployer global stop --short-version='!8.0' # or --short-version=no-8.0
	$ dbdeployer global status --port-range=5000-8099
	$ dbdeployer global start --flavor=percona
	$ dbdeployer global start --flavor='!percona' --type=single
	$ dbdeployer global metadata version --flavor='!percona' --type=single
	
Available Commands:
  exec             Runs a command in all sandboxes
  metadata         Runs a metadata query in all sandboxes
  restart          Restarts all sandboxes
  start            Starts all sandboxes
  status           Shows the status in all sandboxes
  stop             Stops all sandboxes
  test             Tests all sandboxes
  test-replication Tests replication in all sandboxes
  use              Runs a query in all sandboxes

Flags:
      --dry-run                Show what would be executed, without doing it
      --flavor string          Runs command only in sandboxes of the given flavor
  -h, --help                   help for global
      --name string            Runs command only in sandboxes of the given name
      --port string            Runs commands only in sandboxes containing the given port
      --port-range string      Runs command only in sandboxes containing a port in the given range
      --short-version string   Runs command only in sandboxes of the given short version
      --type string            Runs command only in sandboxes of the given type
      --verbose                Show what is matched when filters are used
      --version string         Runs command only in sandboxes of the given version
使用global，可以查看状态，启动，停止，重新启动，测试所有沙箱或运行SQL和元数据查询。

该global命令接受过滤器（从1.44.0版开始）以限制受影响的沙箱。

接受以下子命令：

exec           在所有沙箱中运行命令。
metadata   在所有沙箱中运行元数据查询
restart      启动重新启动所有沙箱
start      启动所有沙箱
status    显示所有沙箱中的状态
stop     停止所有沙箱
test     测试所有沙箱
test-replication    测试所有沙箱中的复制
use      在所有沙箱中运行查询
 

dbdeployer全局执行程序
在所有沙箱中运行命令。该命令将在沙箱中执行。因此，可以引用一个知道应该在其中的文件。没有检查以确保Give命令是可行的。例如：cat filename如果沙箱目录中不存在文件名，该命令将导致错误。可以在复杂的shell命令前加上bash -- -c或来运行它们sh -- -c。该命令必须加引号。尽管可以如上所述使用shell命令来克服此限制，但是只能传递一个命令。重要说明：如果的命令参数包含标志，则必须--在任何标志之前使用双破折号（）。

可以将传递的exec命令与沙箱目录中的其他向下钻取命令（例如）结合使用./exec_all。在这种情况下，需要确保所有沙箱都包含命令，或者--type 仅exec用于在特定拓扑中运行。

dbdeployer全局使用
在所有沙箱中运行查询。它不会检查查询是否与所部署的每个版本兼容。例如，使用的查询@@port不会在MySQL 5.0.x中运行

用法： dbdeployer global use {query} [flags]

例： $ dbdeployer global use "select @@server_id, @@port"

 

 

 

 

沙箱删除
沙箱也可以一次或全部删除：

$ dbdeployer delete -h  
Halts the sandbox (and its depending sandboxes, if any), and removes it. 
Warning: this command is irreversible! 
 
Usage: 
  dbdeployer delete sandbox_name (or "ALL") [flags] 
 
Aliases: 
  delete, remove, destroy 
 
Examples: 
	$ dbdeployer delete msb_8_0_4 
	$ dbdeployer delete rsandbox_5_7_21 
 
Flags: 
      --concurrent     Runs multiple deletion tasks concurrently. 
      --confirm        Requires confirmation. 
  -h, --help           help for delete 
      --skip-confirm   Skips confirmation with multiple deletions. 
      --use-stop       Use 'stop' instead of 'send_kill destroy' to halt the database servers 
可以锁定一个或多个沙箱以防止删除。使用此命令使沙箱不可删除。

$ dbdeployer admin lock sandbox_name
即使运行，锁定的沙箱也不会被删除dbdeployer delete ALL。

锁也可以使用

$ dbdeployer admin unlock sandbox_name

 

 

 

 

克隆数据库
除了在沙箱之间复制外，如果数据库的版本为8.0.17+并且满足前提条件，我们还可以克隆该数据库。

每个使用8.0.17或更高版本的沙盒还将有一个名为的脚本clone_from，其工作方式与replicate_from。

例如，此命令将从主从沙箱克隆到单个沙箱中：

$ ~/sandboxes/msb_8_0_17/clone_from rsandbox_8_0_17 
 Installing clone plugin in recipient sandbox 
 Installing clone plugin in donor sandbox 
 Cloning from rsandbox_8_0_17/master 
 Giving time to cloned server to restart 
