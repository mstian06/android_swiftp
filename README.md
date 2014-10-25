
Swiftp
===


android swiftp server客户端。


注意事项：
===
1. 调试开关：

源码default.java中，public static final boolean release = true;//可以改成false打开log的开关，方便调试。

2. 通过USB进行ftp传输

大家知道，ftp需要两个端口，分别用于命令传输和文件传输。通过代码定制，可以达到usb ftp传输，简单实现类似豌豆荚pc客户端文件管理的功能。
	
定制代码NormalDataSocketFactory.java

	public int onPasv() {
		clearState();
		try {
			// Listen on any port (port parameter 0)
			server = new ServerSocket(0, Defaults.tcpConnectionBacklog);
			myLog.l(Log.DEBUG, "Data socket pasv() listen successful");
			return server.getLocalPort();
		} catch(IOException e) {
			myLog.l(Log.ERROR, "Data socket creation error");
			clearState();
			return 0;
		}
	}
在new ServerSocket(2221, Defaults.tcpConnectionBacklog)，选择可用的数据传输端口（例如2221）；除此之外，pc端还需要进行端口转换。

以windows为例，在手机上启动并开启swiftp server之后，pc ftp clent设置server的主机地址为环回地址（127.0.0.1），执行`adb forward tcp:2121 tcp:2121`和`adb forward tcp:2221 tcp:2221`进行端口转换，这样保证clent请求的命令发向android的swiftp server端。

之后就可以通过usb进行ftp传输文件了，另外可以同时进行wifi ftp传输。