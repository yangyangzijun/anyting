##  第三章 管理oracle数据库
### 管理初始化参数
   初始化参数用于设置实例和数据库特征。在oracle中，初始化参数存储在参数文件中，启动实列，打开数据库是都必须提供相应的参数文件。
  #### 常用初始化参数
  * DB_NAME  指定数据库名称
  * DB_BLOCK_SIZE  数据库标准块的大小
  * UNDO_MANAGEMENT undo管理模式，value=auto||manual auto 使用撤销表空间管理回退数据 manual 回滚段管理数据
  * Db_domain 数据库在分布式网络环境中的逻辑位置。设置为网络域名。和db_name共同构成了全局数据库名
  * compatible 编译版本
  * Instance_name  实列的唯一名称，rac环境中标识一个数据库的不同例程。在单实例数据库系统中，与da_name same
  * db_files 
  * undo_tablespace 启动实例是使用撤销表空间名
  * log_buffer
  * processes 
  * db_cache_size
  * db_cache_size = 0 (big integer)
   * shared_pool_size
### 服务器参数文件（spfile.ora）
* 由Oracle服务器维护
* 始终驻留在服务器端
* 能够在关闭和启动期间持续进行更改
* 可以自我调整参数值
* 可以让Recovery Manager支持备份到初始化参数文件
* 默认位置是$ ORACLE_HOME / database
### 一般参数文件（Init<SID>.ora (PFILE)）
* 文本文件
* 使用操作系统编辑器进行修改
* 手动修改
* 更改将在下次启动时生效
* 仅在实例启动期间打开
* 默认位置为$ ORACLE_HOME / dbs

### 实例可以由
 *  指定的spfile
*  指定的init.ora文件
*  默认spfile  - > spfile <SID> .ora
* 默认的init.ora文件 - > init <SID> .ora
### 启动实例包括以下内容任务：

 * 按以下顺序从$ ORACLE_HOME / dbs读取初始化文件。
* 使用STARTUP指定PFILE参数会覆盖默认行为。

* 分配SGA。
 * 启动后台进程。
* 打开alertSID.log文件和跟踪文件。
### 三个步骤
* **NOMOUNT** 实例已启动，但尚未与数据库关联   ->创建并启动与数据库对应的实例
* **MOUNT**  该实例已启动并与数据库关联读取其控制文件。 数据库对用户不开放  ->为实例加载数据库
* **open**  该实例已启动并与打开的数据库相关联。授权用户可以访问数据文件中包含的数据。  ->将数据库设置为打开状态
Oracle 参数变更生效范围AlterSystem Set Parameter_Name = Value Scope＝（Spfile、Menory、Both）使用BOTH选项实际上等同于不带参数的ALTER SYSTEM语句

|取值|结果|动态参数|静态参数|
|------|----|---|--|
|spfile|表示该修改只对服务器参数有效|数据库重启时有效，永久有效|同动态参数，静态参数只适于这种情况|
|Menory|表示该修改只对内存有效|立即有效，但不产生永久效果，因为没有修改服务器参数|不允许使用|
|Both|表示该修改对上述两种都有效|立即有效，永久有效|不允许使用|
#### To start up an instance, use the following command:
  **startup [nomount|mount|open|force] [resetrict] [pfile=filename]**
* OPEN：允许用户访问数据库。
* MOUNT：为某些DBA活动安装数据库，但不提供用户对数据库的访问权限。
* NOMOUNT：创建SGA并启动后台进程但不提供对数据库的访问。
* PFILE = parfile：启用非默认初始化参数文件以用于配置实例。
* FORCE：在执行normalstartup之前中止正在运行的实例。
* RESTRICT：仅允许具有RESTRICTED SESSIONPrivilege的用户访问数据库。
* RECOVER：数据库启动时开始恢复介质。

ALTER DATABASE MOUNT;
ALTER DATABASE OPEN READ ONLY;
### Shutting Down an Oracle DatabaseInstance(四个状态(open,close,nomount,shut down)，三个步骤)
#### 步骤
* **closed：** The database is mounted, but online data files andredo log files are closed
* **Database unmounted:** 实例已启动，但不再与数据库的控制文件关联。
* **shutdown:** The database instance is no longer started.

|Shutdown Mode|A|I|T|N|
|----|---|---|--|---|
|Allows new connections|no|no|no|no|
|Waits until current sessions end|no|no|no|yes|
|Waits until current transactions end|no|no|yes|yes|
|Forces a checkpoint and closes files|no|no|yes|yes|

A = ABORT(终止关闭方式)  I=IMMEDIATE(立即关闭方式)  T=transaction(事务关闭方式) n=normal(正常关闭)
##### normal 
* 无法建立新的连接。
* 在完成关闭之前，Oracle服务器会等待所有用户断开连接。
* 数据库和重做缓冲区写入磁盘。
* 后台进程终止，SGA结束
* 从内存中删除。
* 在关闭实例之前，Oracle服务器会关闭并卸载数据库。
* 下次启动不需要实例恢复。
#### Transactional
* 没有客户端可以在此特定实例上启动新事务。
* 当客户端结束正在进行的事务时，客户端将断开连接。
* 当所有交易完成后，立即进行停药。
* 下次启动不需要实例恢复。
#### Immediate
* Oracle数据库正在处理的当前SQL语句未完成。
* Oracle服务器不会等待当前连接到数据库的用户断开连接。
* Oracle服务器回滚活动事务并断开所有连接的用户。
* 在关闭实例之前，Oracle服务器会关闭并卸载数据库。
* 下次启动不需要实例恢复。
#### abort
* Oracle服务器正在处理的当前SQL语句将立即终止。
* Oracle服务器不会等待当前连接到数据库的用户断开连接。
* 数据库和重做缓冲区不会写入磁盘。
* 未回滚未提交的事务在不关闭文件的情况下终止实例。
* 数据库未关闭或卸除。
* 下次启动需要实例恢复，这将自动发生。
#### 数据库的特殊状态
##### **Quiesced（静默状态）** Only dba（sysdba or sysoper）can execute this database operation
* **Quiesced From** normal to quiesce state: alter system quiesce restricted;  **From quiesce state to normal：**alter system unquiesce;

Through V$INSTANCE ACTIVE_STATUS to see if the current database is in quiesce state .**select instance_name,active_state  from V$INSTANCE; **
#### Suspend(挂起状态) 暂停物理文件（控制文件，数据文件和重做日志文件）的所有I / O操作。 它适用于物理备份

* alter system suspend;//挂起数据库

* alter system resume;//数据库从挂起状态恢复。
**select database_state  from V$INSTANCE; **

## 第六章（用户权限与安全）
### 用户和模式
* 用户拥有的对象集合是模式。
* 用户与模式一一对应，并其名称相同。
* 用户名和模式通常可互换使用。


  
