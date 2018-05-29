# SNMP协议
## 简介
    简单网络管理协议（SNMP），由一组网络管理的标准组成，包含一个应用层协议（application layer protocol）、数据库模型（database schema）和一组资源对象。该协议能够支持网络管理系统，用以监测连接到网络上的设备是否有任何引起管理上关注的情况。
## 工作原理
    基本上，SNMP代理者以变量呈现管理资料。管理系统透过GET，GETNEXT和GETBULK协定指令取回资讯，或是代理者在没有被询问的情况下，使用TRAP或INFORM传送资料。管理系统也可以传送配置更新或控制的请求，透过SET协定指令达到主动管理系统的目的。配置和控制指令只有当网络基本结构需要改变的时候使用，而监控指令则通常是常态性的工作。


https://www.cnblogs.com/shhnwangjian/p/6343469.html
------------------------------------------------------------------------------------------------------
from pysnmp.hlapi import *
# 获取多条信息
g = getCmd(SnmpEngine(),
            CommunityData("xi'an-ctc-5328-201408"),
            UdpTransportTarget(('220.194.76.252', 161)),
            ContextData(),
            ObjectType(ObjectIdentity('.1.3.6.1.2.1.31.1.1.1.1.1')),
            ObjectType(ObjectIdentity('.1.3.6.1.2.1.31.1.1.1.1.2')),
            ObjectType(ObjectIdentity('.1.3.6.1.2.1.31.1.1.1.1.3')),
            ObjectType(ObjectIdentity('.1.3.6.1.2.1.31.1.1.1.1.4')),
            ObjectType(ObjectIdentity('.1.3.6.1.2.1.31.1.1.1.1.5')),
            ObjectType(ObjectIdentity('.1.3.6.1.2.1.31.1.1.1.1.6')))

a = [str(i) for i in next(g)[3]]

-------------------------------------------------------------------------------------------------------
# 获取table表
g = nextCmd(SnmpEngine(),
            CommunityData("xi'an-ctc-5328-201408"),
            UdpTransportTarget(('220.194.76.252', 161)),
            ContextData(),
            ObjectType(ObjectIdentity('.1.3.6.1.2.1.31.1.1.1.1')),
            lexicographicMode=False)
a = [str(i[3][0]) for i in g]
---------------------------------------------------------------------------------
# 异步获取数据 
def callBack(snmpEngine, sendRequestHandle, errorIndication, errorStatus, errorIndex, varBinds, cbCtx):
    if errorIndex:
        print(errorIndex)
    if errorStatus:
        print(errorStatus)
    print(varBinds[0][0])
    # 返回None则停止获取，返回1或者True则继续获取
    return 1


engin = SnmpEngine()

nextCmd(engin, CommunityData("xi'an-ctc-5328-201408"),UdpTransportTarget(("220.194.76.252", 161)), ContextData(), ObjectType(ObjectIdentity(".1.3.6.1.2.1.31.1.1.1.1")), cbFun=callBack)
nextCmd(engin, CommunityData("xi'an-ctc-5328-201408"),UdpTransportTarget(("220.194.76.252", 161)), ContextData(), ObjectType(ObjectIdentity('.1.3.6.1.2.1.4.20.1.1')), cbFun=callBack)

engin.transportDispatcher.runDispatcher()

--------------------------------------------------------------------------------------------------------------------

 ifHCInOctets ：  .1.3.6.1.2.1.31.1.1.1.6 #  物理接口接受的字节总数
 
ifHCOutOctets：  .1.3.6.1.2.1.31.1.1.1.10 # 物理接口发送的字节总数

 ifAlias：  .1.3.6.1.2.1.31.1.1.1.18 # 接口描述信息

 ifName:  .1.3.6.1.2.1.31.1.1.1.1 # 接口名称  GigabitEthernet0/0/1

 ipAdEntAddr ：  .1.3.6.1.2.1.4.20.1.1 #交换机上的IP地址

 ipAdEntIfIndex：  .1.3.6.1.2.1.4.20.1.2 # 查询IP地址在那个物理接口上，返回的数值是ifName







