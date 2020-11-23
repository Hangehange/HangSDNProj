# Mininet 的基本使用（一）：启动
结合floodlight控制器使用的一些基本经验
首先要知道floodlight的兼听端口号是6653，所以要想在floodlight里看到，必须要指定端口号。
## 简单启动
最简单的启动方式就是这样，但是默认端口不是6653。

        cd mininet
        sudo mn 

这个启动方式，默认有以下设备：
- 交换机s1
- 两台主机h1/h2
- 一个控制器c0

如果需要设置其他的，就需要带参数启动。

------------
## 带参数启动
#### 1.指定网络设备
#####  最常见带的参数是指定控制器参数

    sudo mn --controller=remote --ip=[controller IP] --port=[controllerlistening port]

这里ip默认值是localhost，ip就得设置成6653了
#####  指定别的：主机和交换机

	--switch：
	
switch可以有三类openflow交换机：kernel内核状态、user用户态以及ovsk是Open vSwith状态。当然kerner和ovsk的性能和吞吐量会高一些，通过运行sudo mn --switch ovsk --test iperf进行iperf的测试得知。

	--host:
	 
host可以写成 h1,h2

#### 2.topo
拓扑控制也用的很多

	sudo mn --topo= single	  //星型拓扑
					linear	  //线型拓扑
					tree		//树形拓扑
					reversed	//反向拓扑
					minimal	 //最小拓扑

大部分参数设定因为太复杂（未来会继续补充关于简单启动的指令方式）
所以一般会选择用脚本启动

------------
## 用脚本启动
脚本放在myTopo/myTopo.py里下，可以用--custom参数使用一个Python 脚本。指定加载Python文件建立自定义拓扑必须在mytopo.py文件中以dictionary的形式定义：
    
    topos = { 'mytopo': ( lambda: MyTopo() ) }
启动指令如下：

	sudo mn --controller=remote,port=6653 --custom myTopo/myTopo.py --topo mytopo --mac

 –mac: 作用是让MAC地址易读
 
 
一个示例脚本如下
```python
#	myTopy.py
from mininet.topo import Topo
class MyTopo( Topo ):

    def __init__( self ):
       "Create custom topo."
       # Initialize topology
       Topo.__init__( self )
 
       h1 = self.addHost( 'h1' )
       h2 = self.addHost( 'h2' )
       h3 = self.addHost( 'h3' )
       h4 = self.addHost( 'h4' )
       h5 = self.addHost( 'h5' )
 
       sw1= self.addSwitch( 'sw1' )
       sw2= self.addSwitch( 'sw2' )
       sw3= self.addSwitch( 'sw3' )
       sw4= self.addSwitch( 'sw4' )
       sw5= self.addSwitch( 'sw5' )
       sw6= self.addSwitch( 'sw6' )

       self.addLink( sw1, sw2)
       self.addLink( sw1, sw3)
       self.addLink( sw2, sw4)
       self.addLink( sw2, h2 )
       self.addLink( sw3, sw5)
       self.addLink( sw3, sw6)
       self.addLink( sw4, h1)
       self.addLink( sw5, h3)
       self.addLink( sw5, h4)
       self.addLink( sw6, h5)
	   
topos = { 'mytopo': ( lambda: MyTopo() ) }
```

# 完整的启动命令参考文档

```
Options:
  -h, --help            显示帮助信息
 
  --switch=SWITCH       指定交换机类型
                        default|ivs|lxbr|ovs|ovsbr|ovsk|user[,param=value...]
                        ovs=OVSSwitch default=OVSSwitch ovsk=OVSSwitch
                        lxbr=LinuxBridge user=UserSwitch ivs=IVSSwitch
                        ovsbr=OVSBridge
 
 
  --host=HOST           #用于选择主机的类型
                        cfs|proc|rt[,param=value...]
                        rt=CPULimitedHost{'sched': 'rt'} proc=Host
                        cfs=CPULimitedHost{'sched': 'cfs'}
 
  --controller=CONTROLLER 控制器种类
                        default|none|nox|ovsc|ref|remote|ryu[,param=value...]
                        ovsc=OVSController none=NullController
                        remote=RemoteController default=DefaultController
                        nox=NOX ryu=Ryu ref=Controller
 
 
  --link=LINK           链路的类型
                        default|ovs|tc|tcu[,param=value...] default=Link
                        ovs=OVSLink tcu=TCULink tc=TCLink
 
  --topo=TOPO           指定网络拓扑的类型   
                        linear|minimal|reversed|single|torus|tree[,param=value
                        ...] linear=LinearTopo torus=TorusTopo tree=TreeTopo
                        single=SingleSwitchTopo
                        reversed=SingleSwitchReversedTopo minimal=MinimalTopo
 
  -c, --clean           清除和退出
                            
  --custom=CUSTOM       定制网络拓扑的py文件
 
  --test=TEST           测试
                        none|build|all|iperf|pingpair|iperfudp|pingall
 
  -x, --xterms          打开终端节点
 
  -i IPBASE, --ipbase=IPBASE 设置起始ip
                        
  --mac                 自动设置主机mac,保证mac简单、易读，一般都尽量小
 
  --arp                 设置所有对ARP入口
 
 
  --innamespace         sw and ctrl in namespace?
 
  --listenport=LISTENPORT 设置监听端口
                        
  --nolistenport        不监听端口
 
  --pin                 pin hosts to CPU cores (requires --host cfs or --host
                        rt)
  --nat                 [option=val...] adds a NAT to the topology that
                        connects Mininet hosts to the physical network.
                        Warning: This may route any traffic on the machine
                        that uses Mininet's IP subnet into the Mininet
                        network. If you need to change Mininet's IP subnet,
                        see the --ipbase option.
 
  --cluster=server1,server2...
                        run on multiple servers (experimental!)
  --placement=block|random
                        node placement for --cluster (experimental!)
```
------------
没了
