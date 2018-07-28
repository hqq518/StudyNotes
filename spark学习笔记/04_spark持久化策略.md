# 04 _ spark持久化策略

7/14/2018 3:09:27 PM 

> 由于spark在做各个转换（算子）过程中，当前转换出结果后，上一个转换的结果将会消失，如果后续转换过程中会重复使用某个转换结果的话，建议将该结果进行缓存（持久化），否则spark将会重头再次计算。  
> spark内部预定义有各个持久化策略，也可以由开发者自定义持久化策略。

	package com.hqq.spark;
	
	import org.apache.spark.SparkConf;
	import org.apache.spark.api.java.JavaRDD;
	import org.apache.spark.api.java.JavaSparkContext;
	import org.apache.spark.storage.StorageLevel;
	
	/**
	 * 使用Spark缓存策略
	 * @author HUANGQIQIN
	 *
	 */
	public class TestStorageLevel {
	
		public static void main(String[] args) {
			
			SparkConf conf = new SparkConf().setMaster("local").setAppName("TestStorageLevel");
			JavaSparkContext sc = new JavaSparkContext(conf);
			
			long s1 = System.currentTimeMillis();
			JavaRDD<String> text =  sc.textFile("content.log");
			
			/*
			 * 当RDD会重复使用时通常使用持久化策略
			 * 1、持久化策略默认是MEMORY_ONLY，即只数据缓存到内存当中，如果内存存不下，则放弃部分数据。
			 * 2、如果内存有些吃紧，可以选择MYMORY_ONLY_SER，即压缩数据后再缓存到内存当中。
			 * 3、当缓存的数据需要有一定的容错时，可以选择带数字_2的策略，即缓存两份。
			 * 4、如果中间的结果RDD计算代价比较大，可以选择MEMORY_AND_DISK，即优先将数据缓存到内存，如果内存装不完，则部分缓存到本地磁盘中。
			 */
			
			//使用默认的缓存策略MEMORY_ONLY
			text = text.cache(); // 等价于 text = text.persist(StorageLevel.MEMORY_ONLY());
			
			//使用MEMORY_ONLY_SER持久化策略
			//text = text.persist(StorageLevel.MEMORY_ONLY_SER());
			
			//自定义待久化策略
			//text = text.persist(new StorageLevel(_useDisk, _useMemory, _useOffHeap, _deserialized, _replication));
			
			long count1 = text.count();
			long e1 = System.currentTimeMillis();
			System.out.println("count1 = " + count1 + ", 用时：" + (e1 - s1) + "ms");
			
			long s2 = System.currentTimeMillis();
			long count2 = text.count();
			long e2 = System.currentTimeMillis();
			System.out.println("count2 = " + count2 + ", 用时：" + (e2 - s2) + "ms"); 
			
			sc.close();
			
			//未使用缓存策略：
			//count1 = 1000000, 用时：3969ms
			//count2 = 1000000, 用时：826ms
			
			//使用缓存策略：
			//count1 = 1000000, 用时：5312ms
			//count2 = 1000000, 用时：119ms
		}
	
	}
