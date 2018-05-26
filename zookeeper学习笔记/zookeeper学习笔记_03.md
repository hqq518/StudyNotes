# zookeeper学习笔记

4/15/2018 2:11:12 PM 


------------

## 通过编程方式使用zookeeper提供的API

在eclipse或其他IDE中创建一个普通的java项目，并从zookeeper-3.4.11.tar.gz压缩包中获取zookeeper-3.4.11\zookeeper-3.4.11.jar以及zookeeper-3.4.11\lib\里的所有jar包放置到项目当中。  

以下是完整的测试demo：

	package com.hqq.zk.demo;

	import java.util.List;
	
	import org.apache.zookeeper.CreateMode;
	import org.apache.zookeeper.KeeperException;
	import org.apache.zookeeper.WatchedEvent;
	import org.apache.zookeeper.Watcher;
	import org.apache.zookeeper.ZooDefs.Ids;
	import org.apache.zookeeper.ZooKeeper;
	import org.apache.zookeeper.data.Stat;
	
	/**
	 * 通过 zookeeper客户端API连接zookeeper集群并获取zk各个信息
	 * @author HUANGQIQIN
	 *
	 */
	public class App {
		
		static String connectString = "hqq2:2181,hqq3:2181,hqq4:2181";
		static ZooKeeper zk = null;
	
		public static void main(String[] args) throws Exception {
			zk = new ZooKeeper(connectString, 2000, null);
			
			//获取目录数据及状态信息
			//getData("/root");
			
			//创建目录
			//createPath("/root/s3", "s3_data");
			
			//删除目录
			//deletePath("/root/s30000000002", 0);
			
			//获取目录的子节点
			//getChildren("/root");
			
			//测试zk回调监听事件
			testWatch();
		}
		
		/**
		 * 测试回调方法，当zookeeper节点数据或目录发生变化时，会回调通过客户端
		 * @throws InterruptedException 
		 * @throws KeeperException 
		 */
		public static void testWatch() throws Exception{
			//Watcher是一个接口，这里使用匿名内部类实现该接口的回调后执行的逻辑
			Watcher watcher = new Watcher() {
				@Override
				public void process(WatchedEvent event) {
					System.out.println("有事件发生了：" + event);
				}
			};
			
			//由于触发回调的方法是当前线程启动一个守护线程等待zk回调，在此使用死循环不让主线程退出
			while(true){
				Stat stat = new Stat();
				
				//zk回调Watcher的机制是一次性的，也就是说zk触发一次回调之后，就不会再次触发了，所以客户端
				//如果需要一直监听着zk事件的发生，则回调被触发后，客户端需要再次注册回调，以便下次被zk触发
				zk.getData("/root/s1", watcher, stat);
				
				//让当前线程休眠3秒
				Thread.sleep(3000);
				
				//修改节点数据，以便zk能够触发回调
				zk.setData("/root/s1", "this is s1 of data".getBytes(), stat.getVersion());
			}
			
		}
		
		/**
		 * 获取 指定目录的子节点（子目录）
		 * @param path
		 * @throws InterruptedException 
		 * @throws KeeperException 
		 */
		public static void getChildren(String path) throws Exception{
			List<String> children = zk.getChildren(path, null);
			for(String name : children){
				System.out.println(name);
			}
		}
		
		/**
		 * 删除指定目录
		 * @param path
		 * @param version
		 * @throws KeeperException 
		 * @throws InterruptedException 
		 */
		public static void deletePath(String path, int version) throws Exception{
			//根据指定的路径和数据版本号删除目录，若给定的版本不存在，则抛出异常
			zk.delete(path, version);
		}
		
		/**
		 * 创建目录
		 * @param path
		 * @throws InterruptedException 
		 * @throws KeeperException 
		 */
		public static void createPath(String path, String data) throws Exception{
			//创建一个任何客户端都可以操作的待久化顺序目录(自动拼接一个10位数字的顺序到目录名称后面，如：/root/s30000000002)
			String pathDir = zk.create(path, data.getBytes(), Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT_SEQUENTIAL);
			System.out.println("路径：" + pathDir);
		}
	
		/**
		 * 获取指定目录绑定的数据和该目录的状态信息
		 * @param path 指定目录路径
		 * @throws Exception
		 */
		public static void getData(String path) throws Exception{
			Stat stat = new Stat();
			
			//该方法返回绑定在目录上的数据，同时通过外部传入的Stat对象，将目录的状态信息保存到该对象中
			byte[] dirData = zk.getData(path, null, stat);	
			
			System.out.println("数据：" + new String(dirData).toString());
			System.out.println("状态：" + stat);
		}
		
	}
