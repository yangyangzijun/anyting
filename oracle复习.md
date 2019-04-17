

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
### 管理用户
```create user user1 identifited by 123456 default tablespace ***  quto 10M on *** temporay tablespace temp;--user1 123456 默认表空间  10M 对默认表空间可使用的最大空间， temp 临时表空间 ```
**创建用户后请注意以下事项：**
  * 初始用户没有任何权限，无法执行任何数据库操作;
  * 如果未指定临时表空间子句，则Oracle将默认临时表空间作为用户临时表空间;
* 如果没有指定默认的tablespace子句，Oracle会将Users表空间作为用户的默认表空间;
  *  如果未指定QUOTA子句，则用户将无法在相应的表空间中建立数据对象。
  ```
  alter user myuser quota 100M on users;
  ALTER USER scott IDENTIFIED BY tigerabc;
  ALTER USER myuser ACCOUNT UNLOCK;
  DROP USER myuser2 CASCADE;
  ```
  ### 管理权限
* 系统权限：允许用户在数据库中执行特定操作
   * 系统权限可以授予用户，角色，PUBLIC用户组;
   * 通常，系统特权被授予可信用户，以避免滥用系统特权，并直接危及特权安全性
   * 系统权限分类：
      * 鉴于模式对象：使用任何键
      * 鉴于非模式对象
   * Commonly used privileges as follows:
      * Create Session
      * Restricted Session
       * Create Table
        *  Alter System
        *  Create TableSpace
       *   Grant Any Object Privilege
        *  Create Any Table
        *   Select Any Table
* 对象权限：允许用户访问和操作特定对象
### 角色（角色是具有名称的一组相关权限的组合，即将不同的权限组合在一起就形成了角色）
**角色的用处**
* 更轻松的权限管理使用角色简化权限管理。 您可以将权限授予角色，然后将该角色授予每个用户，而不是向多个用户授予相同的权限集。
*  动态权限管理
* 如果修改了与角色关联的权限，则授予该角色的所有用户将自动立即获取已修改的权限。
*  特权的选择性可用性
* 可以启用和禁用角色以临时打开和关闭权限。 启用角色还可用于验证用户是否已被授予该角色。
*  通过操作系统授予
* 操作系统命令或实用程序可用于将角色分配给数据库中的用户。
####  Manage Role
```
CREATE ROLE role_name [IDENTIFIED BY password];
DROP ROLE account_role;
GRANT CREATE TRIGGER TO ACCOUNT_ROLE;
GRANT DELETE, INSERT, SELECT, UPDATE ON SCOTT.SALGRADE TO ACCOUNT_ROLE;
REVOKE UPDATE ON SCOTT. BONUS FROM ACCOUNT_ROLE;
```
### Manage Profile（概要文件）
* 资源限制参数
   * CPU_PER_SESSION：指定会话的CPU时间限制，以百分之一秒表示。
   * CPU_PER_CALL：指定呼叫的CPU时间限制（解析，执行或获取），以百分之一秒表示
   * CONNECT_TIME：指定会话的总运行时间限制，以分钟为单位。
   * IDLE_TIME：指定会话期间连续非活动时间的允许时间段，以分钟为单位。长时间运行的查询和其他操作不受此限制。
   * SESSIONS_PER_USER：指定要限制用户的并发会话数。
   * LOGICAL_READS_PER_SESSION：指定在会话中读取的允许数据块数，包括从内存和磁盘读取的块。
   * LOGICAL_READS_PER_CALL：指定为处理SQL语句（解析，执行或获取）的调用而读取的允许数据块数。
   * nPRIVATE_SGA：指定会话可在系统全局区域（SGA）的共享池中分配的专用空间量。
   * COMPOSITE_LIMIT：指定会话的总资源成本，以服务单位表示。 Oracle数据库将总服务单位计算CPU_PER_SESSIONCONNECT_TIME,LOGICAL_READS_PER_SESSION和PRIVATE_SGA的加权和。
* 密码管理参数
   * FAILED_LOGIN_ATTEMPTS：指定在锁定帐户之前登录用户帐户的连续失败尝试次数。 如果省略此子句，则默认值为10次。
   * PASSWORD_LOCK_TIME：指定在指定的连续失败登录尝试次数后帐户将被锁定的天数。 如果省略此子句，则默认值为1天。
   * PASSWORD_GRACE_TIME：指定宽限期（宽限期）开始之后的天数，在此期间发出警告并允许登录。 如果省略此子句，则默认值为7天。
   * PASSWORD_LIFE_TIME：指定可以使用相同密码进行身份验证的天数。 如果还为PASSWORD_GRACE_TIME设置了值，则如果在宽限期内未更改密码，则密码将过期，并且拒绝其他连接。 如果省略此子句，则默认值为180天。
   * PASSWORD_VERIFY_FUNCTION：允许将PL / SQL密码复杂性验证脚本作为参数传递给CREATE PROFILE语句。 Oracle数据库提供了一个默认脚本，但您可以创建自己的例程或使用第三方软件。
   * PASSWORD_REUSE_MAX PASSWORD_REUSE_TIME 这两个参数必须相互结合设置。 PASSWORD_REUSE_TIME指定无法重复使用密码的天数。 PASSWORD_REUSE_MAX指定在重用当前密码之前所需的密码更改次数。 要使这些参数生效，必须为它们指定一个值。 例如，如果将PASSWORD_REUSE_TIME指定为30并将PASSWORD_REUSE_MAX指定为10，则如果密码已更改10次，则用户可在30天后重复使用密码。
  ```
  ALTER PROFILE pwd_profile LIMIT PASSWORD_LIFE_TIME 10;
  DROP PROFILE pwd_profile CASCADE;
  ```
## 备份与恢复
#### 故障类别
* 语句失败：单个数据库操作（选择，插入，更新，删除）失败。
   * 尝试将无效数据输入表中
   * 权限不足
   * 尝试分配失败的空间
   * 应用中的逻辑错误
* 用户进程失败：单个数据库会话失败。
 * 用户执行了异常断开连接
 * 用户的会话异常终止
 * 用户遇到程序错误，终止会话。
* 网络故障：丢失与数据库的连接。
   * Listener fails
   * 网络接口卡（NIC）失败
   * Network connection fails
* 用户错误：用户成功完成操作，但操作是错误的不正确（丢弃表格，输入错误数据）。
   * 用户无意中删除或修改了数据。
   * User drops a table.
* 实例故障：数据库实例意外关闭。
   * Power outage
   * 硬件故障
   * 其中一个后台进程失败
   * 紧急关机程序
* 介质故障：一个或多个数据库文件丢失（已删除，磁盘故障）。
#### 实例恢复
* 1. Data files out-of-sync
* 2. Roll forward (redo)
* 3. Committed and noncommitted data in files
* 4. Roll back (undo)
* 5. Committed data in files
#### 调整实列恢复

* 在实例恢复期间，检查点位置和重做日志结束之间的事务必须应用于数据文件。
* 通过控制检查点位置和重做日志结束之间的差异来调整实例恢复。
#### 使用平均恢复时间参数
* 以秒或分钟指定所需的时间。
* 默认值为0（禁用）。
* 最大值为3600秒（一小时）。
#### media failure
* 磁盘驱动器故障
* 磁盘控制器故障
* Deletion or corruption of
* 删除或损坏数据库文件
#####  oracle备份方法概述
* 备份策略：
   * 整体：控制文件的备份和属于数据库的所有数据文件
   * 部分：数据库的一部分
* 备份类型：
   * 完整：非增量RMAN备份
   * 增量：仅包含自上次备份以来发生更改的信息
* 备份模式：
   * 离线：一致，也称为冷备份
   * 联机：不一致，也称为热备份
   ##### 执行oracle的备份和恢复操作
   #### 脱机备份和恢复
* 备份所有文件：
* 离线恢复只能恢复数据库数据和备份时间状态
 #### 联机备份和恢复
 * 完全恢复：回到失败的地步
* 不完全恢复：不完全恢复意味着丢失数据。 通过恢复所有数据文件及时收回整个数据库，然后它不会完全恢复。
* 执行不完全恢复只有两个原因：要么无法完全恢复，要么故意（故意地）决定丢失数据。
#### 数据泵的导入导出
* Full
* Schema
* Table
* Tablespace
* Transportatable tablespace
```
 create or replace directory dump_dir as 'E:\Oracle\dump';
  grant read, write on directory dump_dir to scott;
  expdp scott/Jsj123456 directory=dump_dir dumpfile=myusertab.dmp tables=dept,emp;
 impdp scott/Jsj123456 directory=dump_dir dumpfile=myusertab.dmp tables=dept, emp
 ```
 
 #### 恢复管理器
 Recovery Manager（RMAN）是一个Oracle数据库客户端，用于执行备份和恢复任务
您的数据库并自动管理备份策略。
RMAN提供灵活的方式：
* 备份数据库，表空间，数据文件，控制文件和归档重做日志
* 管理备份和恢复任务
* 执行增量块级备份和块级介质恢复
* 在备份期间检测损坏的块
* 创建备份时使用二进制压缩
###### 组件
* Enterprise Manager：基于浏览器的数据库接口，包括通过RMAN进行备份和恢复。
* Server Sessions:RMAN调用的服务器进程（UNIX）或线程（Windows NT / 2000）连接到目标数据库，以通过PL / SQL接口执行备份，还原和恢复功能。这些会话从磁盘，磁带或闪回恢复区读取或写入文件，闪存恢复区是指定为与数据库恢复相关的文件的默认存储区域的存储位置。
* Target Database:包含RMAN备份或恢复的控制文件，数据文件和可选归档重做日志的数据库。 RMAN使用目标数据库控制文件来收集有关目标数据库的元数据并存储有关其自身操作的信息。备份和恢复的工作由目标数据库上运行的服务器会话执行。
*  Auxiliary(standby, duplicate) Database（辅助）：在创建重复数据库或执行表空间时间点恢复（TSPITR）时使用辅助数据库。辅助数据库可以与其父服务器位于同一主机上，也可以位于不同的主机上
* Channel:要执行和记录备份和恢复操作，RMAN需要指向目标数据库的链接。此链接称为频道。您可以手动分配频道或使用自动频道分配预配置频道。
 * RMAN存储库：RMAN在RMAN存储库中维护有关目标数据库及其备份和恢复操作的元数据。除其他外，RMAN存储有关其自身配置设置，目标数据库架构，归档重做日志以及磁盘或磁带上所有备份文件的信息。 RMAN存储库数据始终存储在目标数据库的控制文件中。
* 恢复目录数据库：包含恢复目录的数据库，其中包含RMAN用于执行备份和恢复的元数据。您可以创建一个包含多个目标数据库的RMAN元数据的恢复目录。除非您将RMAN与物理备用数据库一起使用，否则在使用RMAN时，恢复目录是可选的，因为RMAN将其元数据存储在每个目标数据库的控制文件中。
 * 媒体管理库：RMAN在写入或读取磁带时使用媒体管理库（MML）。使用磁带介质所需的附加介质管理软件由介质和存储系统供应商提供。  
 ##### 闪回
 **分类**
* 闪回事务查询（Flashback Transaction Query）：查看某个事务或所有事务在过去一段时间对数据进行的修改；
* 闪回查询（Flashback Query）：查询过去某个时间点或某个SCN值时表中的数据信息；
* 闪回版本查询（Flashback Version Query）：查询过去某个时间段或某个SCN段内表中数据的变化情况；
* 闪回表（Flashback Table）：将表恢复到过去的某个时间点或某个SCN值时的状态；
* 闪回删除（Flashback Drop）：将已经删除的表及其关联对象恢复到删除前的状态；
* 闪回数据库（Flashback Database）：将数据库恢复到过去某个时间点或某个SCN值时的状态。
* 闪回数据归档：利用保存在一个或多个表空间中的数据变化信息查询过去某个时刻或某个SCN值时表中数据的快照。
```
 SELECT empno, sal FROM scott.emp AS OF TIMESTAMP TO_TIMESTAMP('2017-11-8 15:34:00','YYYY-MM-DD HH24:MI:SS') WHERE empno=7900;--基于时间戳的闪回查询
  SELECT empno,salary FROM scott.emp AS OF SCN 9465723 WHERE empno=7900;
 SELECT versions_xid XID,versions_startscn STARTSCN,versions_endscn ENDSCN,versions_operation OPERATION, sal FROM scott.emp VERSIONS BETWEEN SCN MINVALUE AND MAXVALUE WHERE empno=7900 ORDER BY STARTSCN;  闪回版本查询
  SELECT xid,start_scn,commit_scn,operation,table_name,table_owner FROM FLASHBACK_TRANSACTION_QUERY WHERE table_name= 'EMP'；闪回事务查询
flash table table_name to scn 696669+;闪回表
FLASHBACK TABLE example TO BEFORE DROP RENAME TO new_example;闪回删除
FLASHBACK [STANDBY] DATABASE [database] TO [SCN|TIMESTAMP expression]| [BEFORE SCN|TIMESTAMPexpression] 闪回数据库
参数说明
*  STANDBY：指定执行闪回的数据库为备用数据库；
* TO SCN：将数据库恢复到指定SCN的状态；
* TO TIMESTAMP：将数据库恢复到指定的时间点；
* TO BEFORE SCN：将数据库恢复到指定SCN的前一个SCN状态
* TO BEFORE TIMESTAMP：将数据库恢复到指定时间点前一秒的状态。


  
