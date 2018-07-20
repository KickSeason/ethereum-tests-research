# ethereum-tests-research
 to record my research of ethereum tests
## 1.背景
针对现在的neo共识节点的不同实现需要有一个共同的测试方法来保证实现的质量和测试的便利性，保证实现能够有效遵守neo协议并且方便开发者对自己neo实现的测试。
## 2.方法
1.调研ethereum-tests, 查看以太坊是如何保证不同语言实现的一致性
## 3.tests简介
### 结构简介
* src文件夹： 保存的是testfiller, 一些由json定义的测试实例，或者说测试用例。
* 其他文件夹： filler文件夹中的json文件通过testeth 的--filetes命令会生成一些.json保存在其他文件中，比如GeneralStateTestsFiller中的生成到GeneralStateTests中，所以官方文档特别说明不要更改StateTests， BlockchainTests，TransactionTests， VMTests这些文件夹。
### 使用
* 文档中介绍使用make命令来操作，所以查看**Makefile**
    * makefile中.run中使用的是testeth所以，需要安装testeth
## 4.tests测试环境
### 4.1 安装testeth
* github上有testeth项目和cpp-ethereum项目，其实他们的代码结构是一样的，所以拉取哪个都可以， 我拉取的cpp-ethereum
* 按照文档步骤， 先安装依赖，cmake leveldb-dev等，然后：
```
$ cd cpp-ethereum
$ mkdir build
$ cmake ..
```
这时候在build目录下就会有Makefile，官方用
``` 
cmake --build .
```
但是，如果要跑tests的知道要安装testeth.
所以，有两个方法：
1.按照官方的编译方法，把build/test/testeth放到/usr/local/bin，或者加入环境变量
2. 在build中使用make help命令会看到一系列指令，可以使用make testeth install
### 4.2 安装lllc
同testeth一样，按照官方的文档，在build目录下面会有lllc目录，里面会有lllc程序，需要复制到/usr/local/bin或者添加到环境变量，或者直接使用make lllc install(同样使用make help查看命令)
### 4.3 测试用例
安装完testeth和lllc就可以使用tests了
#### 4.3.1 testeth的使用
* testeth --help查看帮助， 示例：
```
testeth -t GeneralStateTests/stExample -- --single add11 --verbosity 5
```
#### 4.3.2 tests的使用
可以查看**Makefile**使用
```
$ make GeneralStateTests/stExample.run
```
## 5.结构分析
分析testeth代码结构和使用  

*  testeth都在cpp-ethereum代码的test中，并且单独编译成testeth
* test/tools中*.cpp代码为源代码， bootstest.cpp中有main
* 代码中**种**测试实例都继承了testsuite.cpp实现了相应的*testsuite.cpp
* test的执行在importtest.cpp中耦合了cpp-ethereum中libeth*的代码
## 6.结论
tests是一个标准。正如它的文档[http://ethereum-tests.readthedocs.io/en/latest/](http://ethereum-tests.readthedocs.io/en/latest/)以及testeth的实现，如果你用go实现了ethereum你也需要用go实现一个类似testeth类似的组件，来符合tests中定义的json用例。 