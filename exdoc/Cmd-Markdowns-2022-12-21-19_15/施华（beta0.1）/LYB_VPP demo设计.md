# LYB_VPP demo设计

标签（空格分隔）： 施华（beta0.1）

---

[TOC]
# **1.总体思路**
+ 基于多代理框架Jade设计一个MAS(Multi Agent Systems)
+ LYB_VPP demo的编码采取硬编码，后期会引入DAG(Directed Acyclic Graph)动态编码。
+ LYB_VPP demo的编码不考虑数据结构和设计模式等工程化问题，以模拟结果为导向。

# **2.代理demo设计**
## **2.1 一般化代理节点**
demo版本的行为类型都统一成CyclicBehaviour类型方便快速模拟，后续版本再重新设计行为模式。每一个代理的行为抽象为四个：
+ 定位：从DAG中取得一个位置信息，目前demo用手动打印。
+ 读取输入：根据定位信息读取输入数据。
+ 处理输入：针对输入数据采取相应的动作，动作应该是一系列动作（根据业务逻辑设计)。
+ 处理输出：根据定位信息将处理输入返回的输出传递到对应代理节点上。

## **2.2 具象化代理节点**
### **2.2.1 agent_LYB**
+ 定位信息
+ 读取输入：读取实时功率、成本、价格数据
+ 处理输入：结合计划、成本、价格计算出LYB剩余电站功率，再结合需求额定功率计算出可制氢功率。
+ 处理输出：将上文计算出的可制氢功率发送到agent_VPP
+ 对应业务逻辑图
![7.PNG-30.1kB][1]
### **2.2.2 agent_power_grid**
+ 定位信息
+ 读取输入：接受各个agent_mg_i传输过来的可用功率。
+ 处理输入：将所有agent_mg_i的可用功率汇总。
+ 处理输出：将汇总数据传输给agent_VPP
+ 对应业务逻辑图
![8.PNG-52.4kB][2]
### **2.2.3 agent_VPP**
+ 定位信息
+ 读取输入：各种发电源的发电功率汇总及成本信息，还有电源切换和氢储能设备信息。
+ 处理输入：主要分为四个方面
（1）收集信息，该动作实际上读取输入已经做了，这一步可做进一步处理。比如根据包括不同的电源切换信息进行数据预处理（数组选择）。
（2）组合优化，该动作针对收集的发电功率，调用优化器求解最优组合。
（3）控制调度，该动作主要根据氢储能设备信息进行临时性控制。
+ 处理输出：将电解制氢的量发送到氢能发电端agent_hydrogen_power_generation
+ 对应业务逻辑图
![9.PNG-7.5kB][3]
![10.PNG-11.9kB][4]
![11.PNG-20.8kB][5]
### **2.2.4 其他代理**
+ agent_power_switch：供电源切换代理，向agent_VPP提供发电源接入信息。
+ agent_equipment_control：设备控制代理，向agent_VPP提供氢储能设备信息。这是一个起临时性调整作用的代理。
+ agent_hydrogen_power_generation：氢能发电代理。
+ agent_mg_i:这是电网中每一个发电源代理节点，目前主要由风电和光伏组成。
## **2.3 LYB_VPP demo结构图**
![12.png-28.4kB][6]


  [1]: http://static.zybuluo.com/tulip0216/k9vczb8h5swvuda0nicuo3so/7.PNG
  [2]: http://static.zybuluo.com/tulip0216/9gjm2rg3kxpd73tnejuj3p5i/8.PNG
  [3]: http://static.zybuluo.com/tulip0216/tj66pxmv83c542fb0i8eg5jf/9.PNG
  [4]: http://static.zybuluo.com/tulip0216/htu6zswq3x530le6j1d2dwsn/10.PNG
  [5]: http://static.zybuluo.com/tulip0216/ugzgcy91a1bra3ybq8hjs0hk/11.PNG
  [6]: http://static.zybuluo.com/tulip0216/l9lxqhmlpyzgg4sdvaqppnt5/12.png