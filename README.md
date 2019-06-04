# Tool For DBx1000
## 介绍
[DBx1000](https://github.com/yxymit/DBx1000)是一个单节点OLTP数据库管理系统(DBMS)。DBx1000的目标是使DBMS在未来的1000核处理器上可伸缩。DBx1000目前实现了所有7种经典的并发控制方案。它们在不同的工作负载下显示不同的可伸缩性属性。

本repo旨在为DBx1000编写自动化测试程序。  

**免责声明：本repo代码仅供参考，因使用本repo代码而造成的所有损失自负！**

- ```test_cc.py```  
  测试DBx1000提供的所有并发控制算法。具体测试参数为：
  - 仓库数量：1
  - 分区数量：1
  - 线程数量：1-8
  - 工作负载：TPCC
  - 分区最大事务数：1000,000  
  **注**：各项测试结果默认重定向至DBx1000/Result/目录下，文件名为"算法名_t线程数量.txt"。例：WAIT_DIE_t1.txt即为DBx1000在WAIT_DIE并发控制算法下使用1个线程的测试结果。

- ```test_analysis.py```  
  统计上述```test_cc.py```测试脚本的测试结果并计算吞吐率，其中吞吐率通过txn_cnt/SimTime计算得。

## 用法
1. 切换至DBx1000的目录下
```
cd /path/to/DBx1000
```
2. 从GitHub获取```test_cc.py```
```
wget https://raw.githubusercontent.com/AnonymousSFZ/Tool-For-DBx1000/master/test_cc.py
```
3. 运行自动测试程序
```
python test_cc.py
```
预期输出：
```
#define WORKLOAD TPCC
#define CC_ALG HEKATON
#define MAX_TXN_PER_PART 1000000
PASS Compile		alg=HEKATON,	workload=TPCC
PASS Run		alg=HEKATON,	thread=1
PASS Run		alg=HEKATON,	thread=2
PASS Run		alg=HEKATON,	thread=3
PASS Run		alg=HEKATON,	thread=4
PASS Run		alg=HEKATON,	thread=5
PASS Run		alg=HEKATON,	thread=6
PASS Run		alg=HEKATON,	thread=7
PASS Run		alg=HEKATON,	thread=8
#define WORKLOAD TPCC
#define CC_ALG WAIT_DIE
#define MAX_TXN_PER_PART 1000000
PASS Compile		alg=WAIT_DIE,	workload=TPCC
PASS Run		alg=WAIT_DIE,	thread=1
PASS Run		alg=WAIT_DIE,	thread=2
......
```
4. 运行测试分析程序
```python
python test_analysis.py
```
预期输出：
```
WAIT_DI	NO_WAIT	DL_DETE	TIMESTA	MVCC	HEKATON	HSTORE	OCC	TICTOC	SILO	BEST
1.07948 1.13242 0.83193 0.81642 0.90443 0.98980 1.51293 0.92433 1.02396 1.36134 HSTORE
1.11501 1.15171 1.06472 0.00000 1.50135 1.24159 1.96521 0.90358 1.68552 2.13487 SILO
......
```

## 建议
整个测试程序耗时较长，建议使用适用于Windows的Linux子系统（Windows Subsystem for Linux）运行该测试程序。WSL性能显著优于虚拟机，可节省测试时间。

## FAQ
1. 测试程序终止又不希望从头开始执行所有算法

可在```test_cc.py```第16行左右的```algs```列表中手动删去已测试过的算法
```python
algs = ["WAIT_DIE", "NO_WAIT", "DL_DETECT", "TIMESTAMP", "MVCC","HEKATON", "HSTORE", "OCC", "VLL", "TICTOC", "SILO"]
```

2. 某并发控制算法过慢，可否跳过该算法？

使用```Ctrl + C```即可终止当前测试程序，并自动继续执行下一条测试程序。但请注意，即使使用```Ctrl + C```终止，命令行仍会提示PASS，而测试程序实际上并未成功完成测试。

3. 整套测试耗时过长，我希望可以终止测试

使用```Ctrl + Z```即可停止当前测试程序，之后使用
```
kill %{job_id}
```
命令终止测试程序。但请注意，下一次执行测试程序时仍会从头开始重新测试，关于此问题请参考FAQ 1。
