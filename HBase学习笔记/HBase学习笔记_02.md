# HBase学习笔记  

4/7/2018 10:41:05 PM 

----------------------

## HBase shell下常用操作

### 列出命名空间

	base(main):008:0> list_namespace
	NAMESPACE                                                                              
	default                                                                                
	hbase                                                                                  
	2 row(s) in 0.0510 seconds

### 查看某个命名空间下的所有表

	hbase(main):009:0> list_namespace_tables 'hbase'
	TABLE                                                                                  
	meta                                                                                   
	namespace                                                                              
	2 row(s) in 0.0460 seconds

### 查看某个表结构
> hbase中默认有两个命名空间：default和hbase，其中default存放的是用户创建的表，而hbase存放的是hbase系统自身的表。  
> 当查看hbase命名空间下的系统表时，表名前需要带上命名空间加上冒号。若default里的表则不需要。

	hbase(main):011:0> describe 'hbase:meta'	
	Table hbase:meta is ENABLED                                                            
	hbase:meta, {TABLE_ATTRIBUTES => {IS_META => 'true', REGION_REPLICATION => '1', coproce
	ssor$1 => '|org.apache.hadoop.hbase.coprocessor.MultiRowMutationEndpoint|536870911|'}  
	COLUMN FAMILIES DESCRIPTION                                                            
	{NAME => 'info', BLOOMFILTER => 'NONE', VERSIONS => '10', IN_MEMORY => 'true', KEEP_DEL
	ETED_CELLS => 'FALSE', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', COMPRESSION => 
	'NONE', CACHE_DATA_IN_L1 => 'true', MIN_VERSIONS => '0', BLOCKCACHE => 'true', BLOCKSIZ
	E => '8192', REPLICATION_SCOPE => '0'}                                                 
	1 row(s) in 0.0350 seconds

### 创建表
> create指令的第一个参数是表名，第二参数是列簇

	hbase(main):001:0> create 'app','hqq'
	0 row(s) in 1.4670 seconds

### 向表添加一条记录
> put指令的第一个参数是表名，第二参数是rowkey，第三个参数是列簇名，第四个参是值

	hbase(main):004:0> put 'app','row1', 'cf:name','weixin'
	0 row(s) in 0.1860 seconds

### 根据rowkey获取某一条记录

	hbase(main):005:0> get 'app','row1'
	COLUMN                 CELL                                                            
	cf:name               timestamp=1523113583768, value=weixin    

### 列出当前用户下的所有表

	hbase(main):012:0> list
	TABLE                                                                                  
	dome                                                                                   
	hqq                                                                                    
	2 row(s) in 0.0250 seconds

### 统计某张表的总记录数
> count指令默认是1000条记录输出一次，可添加 INTERVAL属性指定多少条记录输出一次。如设置每2000条记录输出一次：count 'dome', INTERVAL => 2000

	hbase(main):013:0> count 'dome'
	Current count: 1000, row: \x00\x00\x03\xE7                                             
	Current count: 2000, row: \x00\x00\x07\xCF                                             
	Current count: 3000, row: \x00\x00\x0B\xB7                                             
	Current count: 4000, row: \x00\x00\x0F\x9F                                             
	Current count: 5000, row: \x00\x00\x13\x87                                             
	Current count: 6000, row: \x00\x00\x17o                                                
	Current count: 7000, row: \x00\x00\x1BW                                                
	Current count: 8000, row: \x00\x00\x1F?                                                
	Current count: 9000, row: \x00\x00#'                                                   
	Current count: 10000, row: \x00\x00'\x0F                                               
	10000 row(s) in 1.2610 seconds

### 全表扫描某张表的所有记录

	hbase(main):014:0> scan 'hqq'
	ROW                    COLUMN+CELL                                                     
	 row1                  column=cf:name, timestamp=1523085166691, value=qiqin            
	 row2                  column=cf:addr, timestamp=1523085222214, value=GuangXi GuiGang  
	 row3                  column=cf:sex, timestamp=1523085256564, value=man               
	 row4                  column=cf:age, timestamp=1523085283375, value=28                
	4 row(s) in 0.0950 seconds

### 扫描某张表前10行记录

	hbase(main):015:0> scan 'dome', LIMIT => 10
	ROW                    COLUMN+CELL                                                     
	 \x00\x00\x00\x00      column=data:1, timestamp=1523092619954, value=hqq-0             
	 \x00\x00\x00\x01      column=data:1, timestamp=1523092619965, value=hqq-1             
	 \x00\x00\x00\x02      column=data:1, timestamp=1523092619967, value=hqq-2             
	 \x00\x00\x00\x03      column=data:1, timestamp=1523092619969, value=hqq-3             
	 \x00\x00\x00\x04      column=data:1, timestamp=1523092619970, value=hqq-4             
	 \x00\x00\x00\x05      column=data:1, timestamp=1523092619971, value=hqq-5             
	 \x00\x00\x00\x06      column=data:1, timestamp=1523092619973, value=hqq-6             
	 \x00\x00\x00\x07      column=data:1, timestamp=1523092619975, value=hqq-7             
	 \x00\x00\x00\x08      column=data:1, timestamp=1523092619979, value=hqq-8             
	 \x00\x00\x00\x09      column=data:1, timestamp=1523092619981, value=hqq-9             
	10 row(s) in 0.0490 seconds

### 禁用某张表

	hbase(main):003:0> disable 'app'
	0 row(s) in 2.3290 seconds

### 删除某张表
> 在删除表之前需要先将该表设置为禁用，才能删除，否则出错。

	hbase(main):004:0> drop 'app'
	0 row(s) in 1.3020 seconds






