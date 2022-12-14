# 第七章：I/O
 **I/O模块的两大主要功能**  
- 通过系统总线或中央交换器和**处理器**、**存储器**连接
- 通过专用数据线与外设相连

### 一、外部设备  
> 连接到I/O的外部设备  
- **人可读**  
  > 显示器、打印机  
- **机器可读**  
  > 磁盘  
- **通信设备**  
  > 终端、其他计算机  

  ![外设框图](./%E5%A4%96%E8%AE%BE%E6%A1%86%E5%9B%BE.png)  
  
### 二、I/O模块  
**主要功能**  ：***控制***和***定时***   (以及处理器通信、设备通信、数据缓冲、检错等)  
- 控制  
  > 1）cpu查询I/O模块，检验该设备连接状态  
  > 2）I/O返回设备状态   
  > 3）若OK，cpu想I/O发出命令请求数据传送   
  > 4）I/O获得来自外设的一个数据单元  
  > 5）数据从I/O传到cpu  
  >
  > Tip：若系统采用总线，则每次cpu和io交互都涉及**总线仲裁**（一次或多次）。  

  以上为  **处理器通信**  方案  

**I/O模块必备功能**  
- **处理器通信**  
  - 命令译码  （CPU发来的）  
  - 数据  
  - 状态报告   （忙、就绪、出错等）  
  - 地址识别    （I/O必须能识别它所控制的每个外设的唯一地址）
- **设备通信**  
- **数据缓冲**  （因为主存和cpu远远比IO快）  
  > 由于**主存->IO**、**IO->主存**传输都需要匹配，因此：**I/O必须既能以设备速度又能以存储器速度传送**  
- **检错**  

**IO模块结构**  
![IO框图](./IO%E6%A8%A1%E5%9D%97%E6%A1%86%E5%9B%BE.png)  

I/O通道常用于大型计算机，相对高级；I/O控制器常用于微机，相对低级  
  
  **IO通道**  
   DMA概念的扩充，可以执行I/O指令来控制I/O操作（这些指令存储在主存中，有I/O通道本身的一个**专用处理器**执行
  - 选择通道  
   > 每次选择一个设备并传输数据  
  - 多路通道  
   > 同时处理多个设备的I/O
    ![IO通道结构](./IO%E9%80%9A%E9%81%93%E7%BB%93%E6%9E%84.png)  

### 二、I/O操作的三种技术  
  > - **编程式**  
  > - **中断驱动式**
  >> 这两种都由copu来负责在输出时从主存提取数据，在输入时把数据存入主存  
  > - **直接存储（DMA）**  
  >> I/O直接和主存交换数据，不需要cpu干涉   
  >
  > 见图![三种数据块输入技术](./%E6%95%B0%E6%8D%AE%E5%9D%97%E8%BE%93%E5%85%A5%E7%9A%84%E4%B8%89%E7%A7%8D%E6%8A%80%E6%9C%AF.png)  
  **指令不等于命令**，处理器从内存获得与I/O有关的指令，处理器向I/O模块发送相应命令----二者通常一一对应，指令形式取决于外设的寻址方式  

  处理器、主存、I/O公用一条公共总路时，有两种编址方式  
  - 存储器映射式IO  
   >  存储器将I/O模块的状态和数据寄存器看成存储单元一样对待，使用相同的机器指令来访问存储器和I/O设备（*一视同仁*）  
  - 分离式IO  
   >  命令线指定该地址是说明存储单元还是说明I/O设备，I/O的地址空间与存储器的地址空间是分离的（*特殊关照*）  
### 1、编程式I/O  
  - I/O模块收到cpu指令后工作，不采用进一步动作通知cpu，**特别是不去中断cpu** 
  - cpu要周期性检查I/O状态（不断询问I/O状态）
  > 存在问题：cpu必须为模块准备接收或传送数据等待很长一段时间，导致**cpu必须不断询问I/O状态**系统性能下降严重  
### 2、中断驱动式I/O  
  - I/O准备和cpu交换数据时，**中断cpu以请求服务**，cpu来执行数据传输，最后恢复原先处理的工作。  
  - 处理器在每个指令周期结束时，**检查的是中断**，终端出现时，保存现场，去处理中断。  
  
  以上两种方式均存在两点不足：
  > I/O传送速度受**处理器测试**和**服务设备速度**的限制  
  > 处理器负责挂历IO传送，对于每一次IO传送，处理器必须执行**很多**指令  
  >  
  > 总之还是很依赖cpu，对cpu利用率和IO传输率都有影响
  
  当需要传送**大量数据**时，DMA来了
### 3、DMA  
  - **突发式**：DMA模块可模仿处理器，接管系统控制工作
  - **周期窃取式**：在cpu不需要总线时占用总线，或者**强制cpu挂起**  
  
  常见的三种DMA配置模式  
  1、单总线分离  
  ![单总线](./%E5%8D%95%E6%80%BB%E7%BA%BF%E5%88%86%E7%A6%BBDMA.png)  
  DMA作为CPU的代理，采用编程式I/O，在存储器与I/O模块之间通过DMA交换数据  
  价格便宜，**效率低**
  如同cpu控制的编程式I/O，每传送一个字要消耗***两个***总线周期  

  2、单总线集合  
  ![单总线集合](./%E5%8D%95%E6%80%BB%E7%BA%BF%E9%9B%86%E5%90%88DMA.png)  
  通过集成DMA和I/O的功能，可以减少所需要总线周期数  

  3、通过I/O总线连接  
  ![I/O总线](./%E5%A4%9AIO%E6%8E%A5%E5%8F%A3DMA.png)  
  这样可以减少DMA连接的I/O接口数，容易**扩展**    

---
## 中断处理  
- 设备给cpu发送中断信号  
- cpu先不管，忙完手头的工作  
- cpu忙完了，确定中断源，并发送信号给中断设备，允许设备取消信号  
- cpu准备传送控制给中断例程  
  > 先要将当前程序信息压栈保存，至少要包括    
  > - PSW（程序状态字寄存器），即cpu状态  
  > - PC（程序计数器），即下一条指令位置  

- cpu将响应该中断的中断处理程序**入口地址**装入PC  
- cpu控制权移交给中断处理程序  
- 中断处理程序开始处理中断  
- 处理完了，恢复现场
  > 先被保存的寄存器值恢复，再PSW和PC被恢复  

### 设计问题  
如何确定哪个模块发生中断？？？
  - 多条中断线
  - 软件轮询
    > 终端服务程序轮询每一个IO模块来确定中断源（费时间）    
  - 菊花链  
    > 硬件轮询电路  
  - 总线仲裁  
    > IO模块发出中断请求前必须获得总线**控制权** 

以上技术用于识别IO中断请求模块，还提供分配优先级的方法。

