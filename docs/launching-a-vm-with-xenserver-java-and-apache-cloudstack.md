# 使用 XenServer、Java 和 Apache CloudStack 的虚拟机

> 原文：<https://www.sitepoint.com/launching-a-vm-with-xenserver-java-and-apache-cloudstack/>

[Apache CloudStack](http://www.cloudstack.org) 是一款开源管理服务器，用于运行私有云基础设施。由于有 Citrix 作为后盾，CloudStack 为 XenServer 主机上的虚拟机横向扩展提供了企业级支持。CloudStack 管理服务器使用 [XenServer 管理 API](http://docs.vmd.citrix.com/XenServer/6.0.0/1.0/en_gb/api/) 的 Java 绑定控制 XenServer 主机实例。由于 CloudStack 源代码在 [github](https://git-wip-us.apache.org/repos/asf/incubator-cloudstack.git) 上，所以它是如何使用 XenServer Java API 的一个指导性例子。在本文中，我们将对 CloudStack 进行代码检查，并了解如何使用 Xen 和 Java 横向扩展私有云的机制。

本文的目标读者包括对基于 Java 的云感兴趣的 Java 程序员，以及正在研究 CloudStack 和/或 XenServer 系统的虚拟化系统管理员。您不需要安装 CloudStack 或 XenServer 来执行这个代码检查，但是如果您想尝试使用 XenServer Java API 运行一个程序，那么您肯定需要安装 XenServer 和一个 VM 模板。设置 XenServer 和 VM 模板超出了本文的范围。另外，请注意:这里显示的代码摘录是对原始 CloudStack 代码的极大简化，而不是要直接编译的完整 Java 程序。真正的 CloudStack 代码相当长，所以我对原始代码进行了大量编辑，以产生足够短的代码来表达这一点。

有了这些介绍，我们来看一些 CloudStack 代码。

当 CloudStack 需要在 XenServer 虚拟机管理程序上横向扩展时，它会制定一个规范，说明它希望启动哪种类型的虚拟机，并将该规范嵌入到一个`StartCommand`中。所以我们的代码检查从方法`execute(StartCommand)`开始，在 Java 源文件【CitrixResourceBase.java】的[中找到。CloudStack 代码位于 Java 包 com.cloud 中，并使用 Java 包 com.xensource 中的 XenServer 管理库。](https://git-wip-us.apache.org/repos/asf?p=incubator-cloudstack.git;a=blob;f=plugins/hypervisors/xen/src/com/cloud/hypervisor/xen/resource/CitrixResourceBase.java;hb=9611a017e931750891c46c38883d919ce3e70cd4)

下面是 CloudStack 的`execute(StartCommand)`方法的要领。

```
package com.cloud.hypervisor.xen.resource;
                                                                     .
import com.xensource.xenapi.Connection;
import com.xensource.xenapi.Host;
import com.xensource.xenapi.VM;
                                                                     .
import com.cloud.agent.api.StartCommand;
import com.cloud.agent.api.StartAnswer;
import com.cloud.agent.api.to.NicTO;
import com.cloud.agent.api.to.VirtualMachineTO;
import com.cloud.agent.api.to.VolumeTO;
                                                                     .
public class CitrixResourceBase {
                                                                     .
   public StartAnswer execute(StartCommand cmd) {
      VirtualMachineTO vmSpec = cmd.getVirtualMachine();
      Connection conn = getConnection();
      Host host = Host.getByUuid(conn, _host.uuid);
      VM vm = createVmFromTemplate(conn, vmSpec, host);
      for (VolumeTO disk : vmSpec.getDisks())
          createVbd(conn, disk, vmName, vm, vmSpec.getBootloader());
      for (NicTO nic : vmSpec.getNics())
          createVif(conn, vmName, vm, nic);
      startVM(conn, host, vm, vmName);
      return new StartAnswer(cmd);
   }
                                                                     .
}
```

当上述方法完成后，您的 XenServer 将有一个全新的 VM guest 运行，并准备好处理您的云设计的任何任务。让我们深入研究该方法启动虚拟机的步骤。

## 获取 Xen 连接并登录

上面的第 17 行显示，第一步是连接到 XenServer。要准备一个安全、加密的 Xen 连接，只需使用 XenServer 实例的 IP 地址通过 HTTPS 构建一个新的连接对象:

```
import java.net.URL;
import com.xensource.xenapi.Connection;
...
Connection conn = new Connection(new URL("https://" + ipAddress),
                                 NUM_SECONDS_TO_WAIT);
```

有了连接，接下来您将继续登录，这是向 XenServer 的第一次 XML-RPC 传输。使用普通 XenServer 凭据，您可以像这样登录:

```
import com.xensource.xenapi.APIVersion;
import com.xensource.xenapi.Connection;
import com.xensource.xenapi.Session;
...
Session session = Session.loginWithPassword(conn, "username",
   "password", APIVersion.latest().toString());
```

当您在主/从资源池中配置了多个 XenServer 实例时，登录会稍微复杂一些。通常，您应该只连接到主服务器，使用方法`loginWithPassword`，这将产生一个在池中任何主机上都有效的会话。如果您需要连接到一个特定的从属实例，您可以使用`slaveLocalLoginWithPassword`，这将为您提供一个只能在该从属主机上使用的“紧急模式”会话。当呈现 XenServer 池时，CloudStack 的目的是永久连接到主服务器。为了安全起见，它假设它要连接的 IP 地址可能是一个从主机，因此它首先进行从主机本地登录，然后从该登录获得主主机 IP 地址，并与主主机重新进行认证。(有关 XenServer 主/从资源池的更多信息，请参见 XenServer [系统恢复指南](http://support.citrix.com/servlet/KbServlet/download/17140-102-671536/XenServer%20System%20Recovery%20Guide.pdf)。)

CloudStack 登录代码位于[XenServerConnectionPool.java](https://git-wip-us.apache.org/repos/asf?p=incubator-cloudstack.git;a=blob;f=plugins/hypervisors/xen/src/com/cloud/hypervisor/xen/resource/XenServerConnectionPool.java;hb=550b22576f1566248c1fc25d2566745ae4d6017c)。这是:

```
package com.cloud.hypervisor.xen.resource;
                                                                     .
import java.net.URL;
                                                                     .
import com.xensource.xenapi.APIVersion;
import com.xensource.xenapi.Connection;
import com.xensource.xenapi.Host;
import com.xensource.xenapi.Pool;
import com.xensource.xenapi.Session;
                                                                     .
public class XenServerConnectionPool {
                                                                     .
   protected Map<String, Connection> _conns;
                                                                     .
   public Connection connect(String hostUuid, String poolUuid,
         String ipAddress, String username, String password,
         int wait) {
      Host host = null;
      Connection sConn = null;
      Connection mConn = _conns.get(poolUuid); // Cached?
      if (mConn != null)  {
         try {
            host = Host.getByUuid(mConn, hostUuid);
         }
         catch (SessionInvalid e) {
            Session.loginWithPassword(mConn, mConn.getUsername(),
               mConn.getPassword(), APIVersion.latest().toString());
         }
      }
      else {
         Connection sConn = new Connection(
            new URL("https://" + ipAddress), wait);
         Session.slaveLocalLoginWithPassword(sConn, username,
            password);
         Pool.Record pr = Pool.getAllRecords(sConn)
            .values().iterator().next(); //Just 1 pool, 1 record
         String masterIp = pr.master.getAddress(sConn);
         mConn = new Connection(masterIp, wait);
         Session.loginWithPassword(mConn, username, password,
            APIVersion.latest().toString());
         _conns.put(poolUuid, mConn);
      }
      return mConn;
   }
                                                                     .
}
```

请注意，XenServer 主机和资源池由 UUID(universal Unique ID)标识，这是 XenServer 和 CloudStack 中都使用的对象命名约定。

## 创建一个 Xen 虚拟机

上面的`execute(StartCommand)`方法的第 19 行显示了下一步是从命令中指定的模板创建一个 VM。CloudStack `StartCommand`包含一个类型为`VirtualMachineTO`的特定于 CloudStack 的`vmSpec`。(“TO”表示传输对象，适用于传入和传出命令 api 的数据记录。传输对象不会持久保存到数据库中。)`vmSpec`是云管理服务器的一个规范，它请求具有某些特征的虚拟机，如 CPU 数量、时钟速度、最小/最大 RAM。CloudStack `createVmFromTemplate`方法应用了`vmSpec`规范并生成了一个 XenServer Java `VM`对象。Xen `VM`类表示可以在 XenServer 实例上运行的客户虚拟机。此方法返回的 VM 尚未启动。

下面是 CloudStack `createVmFromTemplate`方法的要点。

```
import com.xensource.xenapi.Connection;
import com.xensource.xenapi.Host;
import com.xensource.xenapi.Types;
import com.xensource.xenapi.VM;
                                                                     .
import com.cloud.agent.api.to.VirtualMachineTO;
                                                                     .
public class CitrixResourceBase {
                                                                     .
   protected VM createVmFromTemplate(Connection conn,
                        VirtualMachineTO vmSpec, Host host) {
      String guestOsTypeName = getGuestOsType(vmSpec.getOs());
      VM template = VM.getByNameLabel(conn, guestOsTypeName)
         .iterator().next(); //Just 1 template
      VM vm = template.createClone(conn, vmSpec.getName());
                                                                     .
      vm.setIsATemplate(conn, false);
      vm.setAffinity(conn, host); //Preferred host
      vm.removeFromOtherConfig(conn, "disks");
      vm.setNameLabel(conn, vmSpec.getName());
      vm.setMemoryStaticMin(conn, vmSpec.getMinRam());
      vm.setMemoryDynamicMin(conn, vmSpec.getMinRam());
      vm.setMemoryDynamicMax(conn, vmSpec.getMinRam());
      vm.setMemoryStaticMax(conn, vmSpec.getMinRam());
      vm.setVCPUsMax(conn, (long)vmSpec.getCpus());
      vm.setVCPUsAtStartup(conn, (long)vmSpec.getCpus());
                                                                     .
      Map<String, String> vcpuParams = new HashMap<String, String>();
      Integer speed = vmSpec.getSpeed();
      vcpuParams.put("weight", Integer.toString(
         (int)(speed*0.99 / _host.speed * _maxWeight)));
      vcpuParams.put("cap", Long.toString(
         !vmSpec.getLimitCpuUse()  ?  0
            :  ((long)speed * 100 * vmSpec.getCpus()) / _host.speed));
      vm.setVCPUsParams(conn, vcpuParams);
                                                                     .
      vm.setActionsAfterCrash(conn, Types.OnCrashBehaviour.DESTROY);
      vm.setActionsAfterShutdown(conn, Types.OnNormalExit.DESTROY);
      vm.setPVArgs(vmSpec.getBootArgs()); //if paravirtualized guest
                                                                     .
      if (!guestOsTypeName.startsWith("Windows")) {
         vm.setPVBootloader(conn, "pygrub");
      }
   }
                                                                     .
   // Current host, discovered with getHostInfo(Connection)
   protected final XsHost _host = ... ;
                                                                     .
}
```

请注意，每个 Xen `VM`方法都需要一个连接参数。因为 guest 虚拟机实际上是一台计算机，可以同时有许多连接到它，所以连接不是虚拟机的属性。另一方面，一个 VM 一次只能驻留在一个 XenServer 主机上，所以`VM.Record`类型有一个`Host`类型的字段`residentOn`。上面的 CloudStack 方法没有设置`residentOn`，但它设置了另一个类型为`Host`的字段，称为`affinity`，这是对 CloudStack 的一个暗示，即 VM“更喜欢”在特定主机上启动。`CitrixResourceBase`还有一个类型为`XsHost`的字段`_host`，一个 CloudStack 帮助器结构，它在一个名为`getHostInfo`的 CloudStack 方法中使用 XenServer 主机信息和 uuids 进行初始化，与代码检查中显示的内容分开。

对`removeFromOtherConfig`的调用指的是 Citrix `other-config` map 参数，它是一个 map 对象，为 XenServer 主机命令提供任意键/值参数。(参见 [xe 命令参考](http://docs.vmd.citrix.com/XenServer/6.0.0/1.0/en_gb/reference.html#cli-xe-commands_host)，了解一些使用`other-config`参数的主机命令。)在这种情况下，重点是去除任何关于与虚拟机相关联的磁盘的初始假设。

VCPUsParams `weight`和`cap`在 Citrix 文章 [CTX117960](http://support.citrix.com/article/CTX117960/) 中有定义。较高的`weight`会导致 XenServer 分配给该虚拟机的 cpu 比主机上的其他来宾更多。`cap`是虚拟机可以使用多少 cpu 的百分比限制。

## 为每个磁盘创建一个 VBD

`execute(StartCommand)`方法的第 21 行调用 CloudStack 方法`createVbd`在 XenServer guest 中为`vmSpec`中的每个磁盘创建一个 [VBD](http://docs.vmd.citrix.com/XenServer/6.0.0/1.0/en_gb/reference.html#id999215) (虚拟块设备)。VBD 是底层虚拟磁盘映像的装饰者。CloudStack 可以识别四种类型的磁盘卷:Root(主驱动器)、Swap、Datadisk(更多本地存储)、ISO(光盘)。`vmSpec`磁盘信息被传递给下面的`volume`方法参数。

下面是 CloudStack 方法`createVbd`。它做不了很多事情，主要依赖于预先存在的 VDI。如果您已经完成了 CloudStack 的基本安装，那么您就已经拥有了 VDI。这里感兴趣的关键调用是`VBD.create`，它在 XenServer 上分配资源。

```
import com.xensource.xenapi.Connection;
import com.xensource.xenapi.VBD;
import com.xensource.xenapi.VDI;
import com.xensource.xenapi.VM;
                                                                     .
import com.cloud.agent.api.to.VolumeTO;
import com.cloud.storage.Volume;
import com.cloud.template.VirtualMachineTemplate.BootloaderType;
                                                                     .
public class CitrixResourceBase {
                                                                     .
   protected VBD createVbd(Connection conn, VolumeTO volume,
         String vmName, VM vm, BootloaderType bootLoaderType) {
      VDI vdi = VDI.getByUuid(conn, volume.getPath());
      VBD.Record vbdr = new VBD.Record();
      vbdr.VM = vm;
      vbdr.VDI = vm;
      vbdr.userdevice = Long.toString(volume.getDeviceId());
      vbdr.mode = Types.VbdMode.RW;
      vbdr.type = Types.VbdType.DISK;
      if (volume.getType() == Volume.Type.ROOT
            && bootLoaderType == BootloaderType.PyGrub)
         vbdr.bootable = true;
      if (volume.getType() != Volume.Type.ROOT)
         vbdr.unpluggable = true;
      VBD vbd = VBD.create(conn, vbdr);
      return vbd;
   }
                                                                     .
}
```

尽管`createVbd`返回了一个 VBD，调用者`execute`并没有保存它。这大概是因为 CloudStack 可以稍后使用静态方法`VBD.getAllRecords(Connection)`从 XenServer 再次查找它。

为了简单起见，我删除了`createVbd`中涉及`Volume.Type.ISO`的逻辑，这是针对 CD-ROM 驱动器的。

## 为每个网络接口创建一个 VIF

`execute(StartCommand)`方法的第 23 行调用 CloudStack 方法`createVif`为来宾虚拟机上指定的 NIC(网络接口控制器，也称为网络适配器)创建一个新的 XenServer Java [VIF](http://docs.vmd.citrix.com/XenServer/6.0.0/1.0/en_gb/reference.html#networking-concepts-objects) (虚拟网络接口)。这个方法初看起来非常简单:

```
import com.xensource.xenapi.Connection;
import com.xensource.xenapi.Network;
import com.xensource.xenapi.VIF;
import com.xensource.xenapi.VM;
                                                                     .
import com.cloud.agent.api.to.NicTO;
                                                                     .
public class CitrixResourceBase {
                                                                     .
   protected VIF createVif(Connection conn, String vmName, VM vm,
         NicTO nic) {
      VIF.Record vifr = new VIF.Record();
      vifr.VM = vm;
      vifr.device = Integer.toString(nic.getDeviceId());
      vifr.MAC = nic.getMac();
      vifr.network = getNetwork(conn, nic);
      VIF vif = VIF.create(conn, vifr);
      return vif;
   }
                                                                     .
}
```

但是当涉及到网络时，没有什么是真正简单的。CloudStack 的方法`getNetwork`经历了不小的努力来拼凑一个 Xen `Network`对象。整个过程基本上如下:

```
import com.xensource.xenapi.Connection;
import com.xensource.xenapi.Network;
import com.xensource.xenapi.VLAN;
                                                                     .
import com.cloud.agent.api.to.NicTO;
import com.cloud.network.Networks.BroadcastDomainType;
                                                                     .
public class CitrixResourceBase {
                                                                     .
   protected Network getNetwork(Connection conn, NicTO nic) {
      BroadcastDomainType nicType = nic.getBroadcastType();
      Network network = null;
      Network.Record nwr = new Network.Record();
      if (nic.getBroadcastType() == BroadcastDomainType.Native
       || nic.getBroadcastType() == BroadcastDomainType.LinkLocal) {
         network = ...
      }
      else if (nicType == BroadcastDomainType.Vlan) {
         network = ...
      }
      else if (nicType == BroadcastDomainType.Vswitch) {
         network = ...
      }
      return network;
   }
                                                                     .
}
```

上面的基本网络场景是本地/本地链路、Vlan 和开放式虚拟交换机。

本地联网意味着来宾虚拟机将对其以太网适配器执行普通流量。在这种情况下，CloudStack 只是记录该虚拟机上访客流量的 XenServer uuid，并将其作为参数传递给 Xen `Network`构造函数。如上所述，`CitrixResourceBase`已经调用了`getHostInfo`来获取 XenServer uuids，并将它们保存在名为`_host`的字段中。

在 [RFC 3927](http://www.ietf.org/rfc/rfc3927.txt) 中正式定义的链路本地网络，是用于两台机器通过以太网电缆直接连接而没有中间交换机或路由器的网络设置。您通常不会以这种方式配置您的硬件，但事实上，CloudStack 确实使用链路本地寻址将 XenServer 主机连接到一个特殊的系统控制 VM，称为二级存储 VM。当该虚拟机启动时，它就属于这种情况，由于 CloudStack 已经将其网络适配器 uuid 存储在`_host`中，因此在与本地网络相同的代码块中处理链路本地情况是有意义的:

```
protected Network getNetwork(Connection conn, NicTO nic) {
      ...
      if (nic.getBroadcastType() == BroadcastDomainType.Native
       || nic.getBroadcastType() == BroadcastDomainType.LinkLocal) {
         String uuid = null;
         switch (nic.getType()) {
            case Guest: uuid = _host.guestNetwork; break;
            case Control: uuid = _host.linkLocalNetwork; break;
            case Management: uuid = _host.privateNetwork; break;
            //other cases not shown
         }
         network = Network.getByUuid(conn, uuid);
      }
      ...
```

对于 Vlan，CloudStack 通过添加标签来使网络名称唯一，从而允许虚拟局域网在单个网络上共存。CloudStack 采用了一个额外的技巧，即在集群 XenServer 主机并发尝试创建同一个 Vlan 的情况下，向 Vlan 名称添加时间戳；他们将被要求选择用最早的时间戳创建的 Vlan。

```
protected Network getNetwork(Connection conn, NicTO nic) {
      ...
      else if (nicType == BroadcastDomainType.Vlan) {
         long tag = Long.parseLong(nic.getBroadcastUri().getHost());
         nwr.nameLabel = "VLAN-" + network.getNetworkRecord(conn).uuid
            + "-" + tag;
         nwr.tags = new HashSet<String>();
         nwr.tags.add(generateTimeStamp());
         network = Network.create(conn, nwr);
         VLAN.create(conn, network.getPif(conn), tag, network);
      }
      ...
```

[Open vSwitch](http://openvswitch.org/) 和 CloudStack 本身一样，是 Citrix 支持的另一款开源虚拟化产品。当使用 Open vSwitch 路由来宾 NIC 时，CloudStack 会在 XenServer 控制域(“dom0”)上创建 VIF，并临时将其插入，这具有创建到底层 PIF(物理网络接口)的网桥的效果。Open vSwitch 支持自己的 Vlan，或者您可以选择隧道式网络连接到底层网络接口。

```
protected Network getNetwork(Connection conn, NicTO nic) {
      ...
      else if (nicType == BroadcastDomainType.Vswitch) {
         String nwName = null;
         if (nic.getBroadcastUri().getAuthority().startsWith("vlan")
            nwName = "vswitch";
         else {
            nwName = "OVSTunnel"
               + Long.parseLong(nic.getBroadcastUri().getHost());
            nwr.otherConfig = mapPair("ovs-host-setup", "");
         }
         nwr.nameLabel = nwName;
         network = Network.create(conn, nwr);
         VM dom0 = null;
         for (VM vm : Host.getByUuid(conn, _host.uuid)
                          .getResidentVMs(conn)) {
            if (vm.getIsControlDomain(conn)) {
               dom0 = vm;
               break;
            }
         }
         VIF.Record vifr = new VIF.Record();
         vifr.VM = dom0;
         vifr.device = getLowestAvailableVIFDeviceNum(conn, dom0);
         vifr.otherConfig = mapPair("nameLabel", nwName);
         vifr.MAC = "FE:FF:FF:FF:FF:FF";
         vifr.network = network;
         VIF dom0vif = VIF.create(conn, vifr);
         dom0vif.plug(conn); //XenServer creates a bridge
         dom0vif.unplug(conn);
      }
      ...
```

## 启动虚拟机

最后，在`execute(StartCommand)`的第 24 行，我们看到对 CloudStack 方法`startVM`的调用。`startVM`唯一的工作就是调用 Xen 方法`VM.startOnAsync`。这是一个潜在的长时间运行的操作，所以它返回一个 Xen `Task`对象，CloudStack 将监视这个对象，等待 VM 完成启动。`startOnAsync`还采用两个布尔标志，在这种情况下，它们被设置为在运行状态(非暂停)下启动 VM，并强制启动，而不管当前 VM 配置看起来是否与上次启动时不同。

如果忽略 try/catch 处理，启动 VM 的过程会很简单:

```
import com.xensource.xenapi.Connection;
import com.xensource.xenapi.Host;
import com.xensource.xenapi.Task;
import com.xensource.xenapi.Types;
import com.xensource.xenapi.VM;
                                                                     .
public class CitrixResourceBase {
                                                                     .
   void startVM(Connection conn, Host host, VM vm, String vmName) {
      boolean startPaused = false;
      boolean force = true;
      Task task = vm.startOnAsync(conn, host, startPaused, force);
      while (task.getStatus(conn) == Types.TaskStatusType.PENDING) {
         Thread.sleep(1000);
      }
      if (task.getStatus(conn) != Types.TaskStatusType.SUCCESS) {
         task.cancel(conn);
         throw new Types.BadAsyncResult();
      }
   }
                                                                     .
}
```

我们做到了！通过代码检查，我们已经浏览了 Java 代码，其中 CloudStack 连接到 XenServer 虚拟机管理程序，登录，创建来宾虚拟机，分配其 vbd 和 VIFs，并启动来宾虚拟机。一个全新的虚拟机启动并运行在私有云中。

## 分享这篇文章