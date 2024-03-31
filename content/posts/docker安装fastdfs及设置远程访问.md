---
share: "true"
title: docker安装fastdfs及设置远程访问
date: 2020-12-28 22:22:13
tags: 
---

## fastdfs说明

1: FastDFS架构包括 Tracker server和Storage server。客户端请求Tracker server进行文件上传、下载，通过Tracker server调度最终由Storage server完成文件上传和下载。
2: Tracker server作用是负载均衡和调度，通过Tracker server在文件上传时可以根据一些策略找到Storage server提供文件上传服务。可以将tracker称为追踪服务器或调度服务器。
3: Storage server作用是文件存储，客户端上传的文件最终存储在Storage服务器上，Storage server没有实现自己的文件系统而是利用操作系统 的文件系统来管理文件。可以将storage称为存储服务器。

<!--more-->

## docker启动命令

```
 docker run -dti -p 22122:22122 --name tracker -v /root/fastdfs/tracker:/var/fdfs -v /etc/localtime:/etc/localtime  delron/fastdfs tracker
 
 docker run -dti  -p 8888:8888 -p 23000:23000 --name storage -e TRACKER_SERVER=192.168.0.17:22122 -v /root/fastdfs/storage:/var/fdfs -v /etc/localtime:/etc/localtime   delron/fastdfs storage
```

此时部署成功之后，根据文章 [FastDFS基于Docker安装](https://blog.csdn.net/wo541075754/article/details/107528318) 中的验证是可以成功上传文件的，但是在本地通过java代码无法上传成功，需要修改 storage容器中的tracker_server的地址（见参考第二个链接）

## JAVA上传代码

fdfs_client.conf

```
connect_timeout = 2
network_timeout = 30
charSet = UTF-8
http.tracker_http_port = 8888
http.anti_steal_token = no
http.secret_key = FastDFS1234567890
tracker_server = xxxx.xxxx.xxxx.xxxx:22122   //此处修改为对应的公网ip
```

FastDfsUtil.java

```
public class FastDfsUtil {

	private static TrackerClient trackerClient = null;
	private static TrackerServer trackerServer = null;
	private static StorageServer storageServer = null;
	private static StorageClient storageClient = null;

	static {
		try {
			ClientGlobal.init("fdfs_client.conf");
			trackerClient = new TrackerClient();
			trackerServer = trackerClient.getConnection();
			storageClient = new StorageClient(trackerServer, storageServer);
		} catch (IOException | MyException e) {
			throw new RuntimeException("FastDfs工具类初始化失败!");
		}

	}

	public static void main(String[] args) throws IOException, MyException {
		fdfsUpload("/Users/weizhao/Downloads/10086.xml");
		fdfsDownload("/group1/M00/00/00/rBEABF_pcc2AUpr0AAYVqeK5m4w886.xml", "./2.xml");
	}

	/**
	 *
	 * @Title: fdfsUpload
	 * @Description: 通过文件流上传文件
	 * @param @param inputStream 文件流
	 * @param @param filename 文件名称
	 * @param @return
	 * @param @throws IOException
	 * @param @throws MyException
	 * @return String 返回文件在FastDfs的存储路径
	 * @throws
	 */
	public  String fdfsUpload(InputStream inputStream, String filename) throws IOException, MyException{
		String suffix = "";
		//后缀名
		try{
			suffix = filename.substring(filename.lastIndexOf(".")+1);
		}catch (Exception e) {
			throw new RuntimeException("参数filename不正确!格式例如：a.png");
		}
		String savepath = "";
		//FastDfs的存储路径
		ByteArrayOutputStream swapStream = new ByteArrayOutputStream();
		byte[] buff = new byte[1024];
		int len = 0;
		while ((len = inputStream.read(buff)) != -1) {
			swapStream.write(buff, 0, len);
		}
		byte[] in2b = swapStream.toByteArray();
		String[] strings = storageClient.upload_file(in2b, suffix, null);
		//上传文件
		for (String str : strings) {
			savepath += "/" + str;
			//拼接路径
		}
		return savepath;
	}

	/**
	 *
	 * @Title: fdfsUpload
	 * @Description: 本地文件上传
	 * @param @param filepath 本地文件路径
	 * @param @return
	 * @param @throws IOException
	 * @param @throws MyException
	 * @return String 返回文件在FastDfs的存储路径
	 * @throws
	 */
	public static String fdfsUpload(String filepath) throws IOException, MyException{
		String suffix = "";
		//后缀名
		try{
			suffix = filepath.substring(filepath.lastIndexOf(".")+1);
		}catch (Exception e) {
			throw new RuntimeException("上传的不是文件!");
		}
		String savepath = "";
		//FastDfs的存储路径
		//String[] strings = storageClient.upload_file(filepath.getBytes(), suffix, null);
		String[] strings = storageClient.upload_file(filepath, suffix, null);
		//上传文件
		for (String str : strings) {
			savepath += "/" + str;
			//拼接路径
		}
		System.out.println(savepath);
		return savepath;
	}


	/**
	 *
	 * @Title: fdfsDownload
	 * @Description: 下载文件到目录
	 * @param @param savepath 文件存储路径
	 * @param @param localPath 下载目录
	 * @param @return
	 * @param @throws IOException
	 * @param @throws MyException
	 * @return boolean 返回是否下载成功
	 * @throws
	 */
	public static boolean fdfsDownload(String savepath, String localPath) throws IOException, MyException{
		String group = ""; //存储组
		String path = ""; //存储路径
		try{
			int secondindex = savepath.indexOf("/", 2); //第二个"/"索引位置
			group = savepath.substring(1, secondindex); //类似：group1
			path = savepath.substring(secondindex + 1); //类似：M00/00/00/wKgBaFv9Ad-Abep_AAUtbU7xcws013.png
		}catch (Exception e) {
			throw new RuntimeException("传入文件存储路径不正确!格式例如：/group1/M00/00/00/wKgBaFv9Ad-Abep_AAUtbU7xcws013.png");
		}
		int result = storageClient.download_file(group, path, localPath);
		if(result != 0){
			throw new RuntimeException("下载文件失败：文件路径不对或者文件已删除!");
		}
		return true;
	}
	/**
	 *
	 * @Title: fdfsDownload
	 * @Description: 返回文件字符数组
	 * @param @param savepath 文件存储路径
	 * @param @return
	 * @param @throws IOException
	 * @param @throws MyException
	 * @return byte[] 字符数组
	 * @throws
	 */
	public static byte[] fdfsDownload(String savepath) throws IOException, MyException{
		byte[] bs = null;
		String group = ""; //存储组
		String path = ""; //存储路径
		try{
			int secondindex = savepath.indexOf("/", 2); //第二个"/"索引位置
			group = savepath.substring(1, secondindex); //类似：group1
			path = savepath.substring(secondindex + 1); //类似：M00/00/00/wKgBaFv9Ad-Abep_AAUtbU7xcws013.png
		}catch (Exception e) {
			throw new RuntimeException("传入文件存储路径不正确!格式例如：/group1/M00/00/00/wKgBaFv9Ad-Abep_AAUtbU7xcws013.png");
		}
		bs = storageClient.download_file(group, path); //返回byte数组
		return bs;
	}

```

## 参考

* [FastDFS基于Docker安装](https://blog.csdn.net/wo541075754/article/details/107528318)
* [FastDFS(fdfs)遇到的坑之一,本地fastdfs-client-java开发上传图片报错:java.net.SocketTimeoutException: connect timed out](https://blog.csdn.net/q258523454/article/details/82791334?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-2&spm=1001.2101.3001.4242)


