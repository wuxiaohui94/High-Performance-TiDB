## 环境依赖

1. Go 1.13以上
2. Rust环境
3. Yarn
4. Cmake


## TiDB编译运行

1. git clone [git@github.com](mailto:git@github.com):pingcap/tidb.git
2. go build [github.com/pingcap/tidb/tidb-server](http://github.com/pingcap/tidb/tidb-server)
3. go run tidb-server/main.go

## TiKV编译运行

1. git clone [git@github.com](mailto:git@github.com):tikv/tikv.git
2. cargo build
3. cargo run --package cmd --bin tikv-server

## PD编译运行
![https://github.com/wuxiaohui94/High-Performance-TiDB/blob/master/img/PD%E8%BF%90%E8%A1%8C%E6%8A%A5%E9%94%99.png](https://github.com/wuxiaohui94/High-Performance-TiDB/blob/master/img/PD%E8%BF%90%E8%A1%8C%E6%8A%A5%E9%94%99.png)

1. git clone [git@github.com](mailto:git@github.com):pingcap/pd.git
2. PD直接编译运行是不行的，会报有些文件找不到定义
3. 可以选择忽略Dashboard编译，这里选择了包括Dashboard,查看了Makefile后，发现是./scripts/embed-dashboard-ui.sh脚本编译的Dashboard
4. 先执行./scripts/embed-dashboard-ui.sh，可以看到在pd目录下新建了.dashboard_asset_cache目录，由于命令下载过于慢，所以我去这里手动下载了包，[https://github.com/pingcap-incubator/tidb-dashboard/releases/download/v2020.08.07.1/embedded-assets-golang.zip](https://github.com/pingcap-incubator/tidb-dashboard/releases/download/v2020.08.07.1/embedded-assets-golang.zip)，然后重命名为embedded-assets-golang-2020.08.07.1.zip 放在.dashboard_asset_cache目录下(重命名后面的日期是版本号，在download连接中有) 
5. 然后export DASHBOARD=COMPILE,再执行./scripts/embed-dashboard-ui.sh，等构建完，会发现多了pd/pkg/dashboard/uiserver/embedded_assets_handler.go文件
6. go run cmd/pd-server/main.go 成功运行PD
7. [http://127.0.0.1:2379/dashboard](http://127.0.0.1:2379/dashboard/#/signin)进入Dashboard

## TiDB、TiKV、PD运行

1. 3个都编译完后，运行TiKV、PD、TiDB
2. 发现Pd 没有TiDB连接，使用Dashboard也是登录不了(账号是root，密码为空)
3. 到TiDB 的config目录修改config.go文件下的默认配置，把Store改为tikv，Path改为PD的地址，再次运行TiKV、PD、TiDB，3者成功可以连接
![https://github.com/wuxiaohui94/High-Performance-TiDB/blob/master/img/config文件修改.png](https://github.com/wuxiaohui94/High-Performance-TiDB/blob/master/img/config文件修改.png)

## 修改源码

在TiDB项目中，修改tidb/store/tikv/2pc.go的execute方法，重新编译运行，可以看到执行事务后，打印了这行日志
![https://github.com/wuxiaohui94/High-Performance-TiDB/blob/master/img/%E4%BF%AE%E6%94%B9%E6%BA%90%E7%A0%81.png](https://github.com/wuxiaohui94/High-Performance-TiDB/blob/master/img/%E4%BF%AE%E6%94%B9%E6%BA%90%E7%A0%81.png)