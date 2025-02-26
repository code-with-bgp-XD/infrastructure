# infrastructure

| 工具         | 官网                            | 说明                                               |
| ------------ | ------------------------------- | -------------------------------------------------- |
| docker       | https://www.docker.com/         | 🐋                                                  |
| frr          | https://frrouting.org/          | 在linux/unix平台上的互联网路由协议套件             |
| containerlab | https://containerlab.dev/       | 为docker搭建网络拓扑的工具（yaml配置文件）         |
| containernet | https://containernet.github.io/ | 支持sdn/docker的网络拓扑搭建工具（python代码实现） |
| ryu          | https://ryu-sdn.org/            | python实现的sdn控制器，支持bgp                     |

<table style="text-align: center;">
<tr>
<th>研究目的</th>
<th>工具</th>
<th>说明</th>
</tr>
<tr>
<td rowspan="2">-</td>
<td>docker</td>
<td>必须</td>
</tr>
<tr>
<td>frr</td>
<td>docker容器必须，源码可选（如果你需要修改源码重新编译为容器）</td>
</tr>
<tr>
<td rowspan="2">sdn+ovs</td>
<td>containernet</td>
<td rowspan="2">可选</td>
</tr>
<tr>
<td>ryu</td>
</tr>
<td>单独研究frr</td>
<td>containerlab</td>
<td>可选</td>
</table>

# 安装教程

> 本教程是基于ubuntu22.04进行的，python版本为3.10.12
> python版本为3.10（很重要），因为部分代码需要做些修改
> containerlab/containernet/ryu 都是通过源码安装

<table style="text-align: center;">
<tr>
<th>工具</th>
<th>安装步骤</th>
<th>补充说明</th>
</tr>
<tr>
<td >Docker</td>
<td><a href="https://github.com/docker/docker-install" target="_blank">https://github.com/docker/docker-install</a></td>
<td>containernet 会帮你安装docker</td>
</tr>
<tr>
<td >ryu</td>
<td>docker pull quay.io/frrouting/frr:10.2.1</td>
<td><a href="https://quay.io/repository/frrouting/frr?tab=tags" target="_blank">docker镜像地址</a><br/>ryu源码修改教程：todo</td>
</tr>
<tr>
<td >containerlab</td>
<td><a href="https://containerlab.dev/install/" target="_blank">https://containerlab.dev/install/</a></td>
<td>-</td>
</tr>
</table>

## sdn+ovs

### 1. clone 本仓库

选择合适的地方创建虚拟环境并安装相关工具

```bash
mkdir -p ~/research && cd ~/research
```

```bash
git clone https://github.com/code-with-bgp-XD/infrastructure.git
cd ./infrastructure
git submodule init && git submodule update
cd ..
```

### 2. 创建python虚拟环境并激活

> 用于环境隔离，防止打乱本机python环境
> 
> 当前目录为

``` text
reseach
└── infrastructure
```

```bash
python3 -m venv .venv
或者是
pip install -U virtualenv
virtualenv .venv
```

```bash
# 假设你使用的是bash
source .venv/bin/activate
# fish
. .venv/bin/activate.fish
```

### 3.安装containernet和ryu


> 如果pip安装太慢可以考虑使用国内pip镜像源
> 
> 当前目录为

```text
reseach
├── .venv
└── infrastructure
```

**安装containernet**

```bash
sudo apt-get install ansible
# 如果你想修改代码立刻生效，通过此方法安装
pip install -e ./infrastructure/containernet --no-binary :all:
# 普通安装模式
pip install ./infrastructure/containernet
```

**测试containernet**

参考：https://github.com/containernet/containernet?tab=readme-ov-file#running-a-basic-example

如有遇到权限问题可以试试 `sudo -E env PATH=$PATH python3`

**安装ryu**

```bash
# 回退setuptools版本
pip install setuptools==67.6.1
pip install ./infrastructure/ryu-patch
```

**测试ryu**

```bash
ryu-manager --version
```

# 可能遇到的问题

1. 使用`ryu-manager`时提示dns/....py出现问题，`collections.Mapping`
- 将`collections.Mapping` 改为 `collections.abc.Mapping` （python3 >= 3.10）