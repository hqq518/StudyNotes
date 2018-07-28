# 03 _ spark _ java开发_统计单词出现次数

7/10/2018 11:55:51 PM 

	package com.hqq.spark;

	import java.util.Arrays;
	import java.util.Iterator;
	
	import org.apache.spark.SparkConf;
	import org.apache.spark.api.java.JavaPairRDD;
	import org.apache.spark.api.java.JavaRDD;
	import org.apache.spark.api.java.JavaSparkContext;
	import org.apache.spark.api.java.function.FlatMapFunction;
	import org.apache.spark.api.java.function.Function;
	import org.apache.spark.api.java.function.Function2;
	import org.apache.spark.api.java.function.PairFlatMapFunction;
	import org.apache.spark.api.java.function.PairFunction;
	import org.apache.spark.api.java.function.VoidFunction;
	
	import scala.Tuple2;
	
	/**
	 * 使用spark统计文件单词
	 * @author HUANGQIQIN
	 *
	 */
	public class WordCount {
	
		public static void main(String[] args) {
			//设置spark以本地模式运行
			SparkConf conf = new SparkConf().setMaster("local").setAppName("wc");
			JavaSparkContext sc = new JavaSparkContext(conf);
			
			//加载数据源文件
			JavaRDD<String> text = sc.textFile("F:\\eclipsework\\workspace\\Spark_demo01\\src\\test.txt");
			
			//添加过滤器
			JavaRDD<String> words = text.filter(new Function<String, Boolean>() {
				private static final long serialVersionUID = 1L;
				@Override
				public Boolean call(String arg0) throws Exception {
					// TODO 对文件中每一行内容进行过滤
					return true;
				}
			});
			
			//逐行读取文件内容，将空格分割，最后返回所有分割后的单词集合
			words = text.flatMap(new FlatMapFunction<String, String>() {
				private static final long serialVersionUID = 1L;
				@Override
				public Iterator<String> call(String line) throws Exception {
					return Arrays.asList(line.split(" ")).iterator();
				}
			});
			
			//将上一步计算出来的单词集合转成map的键值对:key=单词，value=该单词出现的次数
			 JavaPairRDD<String, Integer>  pairs = words.mapToPair(new PairFunction<String, String, Integer>() {
				private static final long serialVersionUID = 1L;
				@Override
				public Tuple2<String, Integer> call(String word) throws Exception {
					return new Tuple2<String, Integer>(word, 1);
				}
			});
			 
			 //将上一步计算出来的单词键值对，按key进行合并累加个数
			 JavaPairRDD<String, Integer> results = pairs.reduceByKey(new Function2<Integer, Integer, Integer>() {
				private static final long serialVersionUID = 1L;
				//这里传递进来的参数是键值对中的value(具有相同的key)
				@Override
				public Integer call(Integer value1, Integer value2) throws Exception {
					return value1 + value2;
				}
			});
			 
			 //将上一步按单词出现次数累加后，再转成另一个键值对集合：key=单词出现的次数，key=单词
			 //该操作主要是为了后面按key进行排序
			 JavaPairRDD<Integer, String> temp = results.mapToPair(new PairFunction<Tuple2<String,Integer>, Integer, String>() {
				private static final long serialVersionUID = 1L;
				@Override
				public Tuple2<Integer, String> call(Tuple2<String, Integer> tuple) throws Exception {
					return new Tuple2<Integer, String>(tuple._2, tuple._1);
				}
			});
			 
			 //按key进行倒序排序，之后将键值对又转回之前的键值对：key=单词，value=该单词出现的次数
			 JavaPairRDD<String, Integer> sorted = temp.sortByKey(false).mapToPair(new PairFunction<Tuple2<Integer,String>, String, Integer>() {
				private static final long serialVersionUID = 1L;
				@Override
				public Tuple2<String, Integer> call(Tuple2<Integer, String> tuple) throws Exception {
					return new Tuple2<String, Integer>(tuple._2, tuple._1);
				}
			});
			 
			 //打印结果
			 sorted.foreach(new VoidFunction<Tuple2<String,Integer>>() {
				private static final long serialVersionUID = 1L;
				@Override
				public void call(Tuple2<String, Integer> tuple) throws Exception {
					System.out.println("=====> " + tuple._1 + " = " + tuple._2);
				}
			});
			
			 //关闭上下文
			sc.close();
		}
	
	}


运行结果：
	
	=====> the = 4
	=====> is = 3
	=====> this = 2
	=====> collection = 2
	=====> iterator = 2
	=====> underlying = 2
	=====>  = 2
	=====> in = 2
	=====> by = 2
	=====> other = 1
	...

