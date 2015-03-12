Title: FTP上传文件的Java代码
Url: /2009/04/22/ftp-upload-file-in-java
Tags: java,ftp

```java
FtpClient fc = new FtpClient(hostIP, 21);
fc.login(userName, pwd);
fc.binary();
TelnetOutputStream tos = fc.put(newfilename);// 服务器上保存的文件名
InputStream is = new FileInputStream("d:\\11.jpg");// 本地文件路径
byte[] bytes = new byte[1024];
while (true) {
	int len = is.read(bytes);
	if (len == -1) {
		 break;
	}
			
	tos.write(bytes);
}
tos.close();
fc.closeServer();
```