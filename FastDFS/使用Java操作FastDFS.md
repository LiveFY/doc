## 上传文件
**方式一**：

```
import java.io.File;
import java.util.Arrays;
import org.csource.common.NameValuePair;
import org.csource.fastdfs.ClientGlobal;
import org.csource.fastdfs.StorageClient;
import org.csource.fastdfs.StorageServer;
import org.csource.fastdfs.TrackerClient;
import org.csource.fastdfs.TrackerServer;
import org.springframework.core.io.ClassPathResource;
public class TestUpload {
	public static void main(String[] args) throws Exception {
		// 1、定义要上传的文件，如果此时不是文件利用InputStream类来处理
		File file = new File("D:" + File.separator + "nophoto.jpg") ;
		// 2、要想上传则需要明确的知道文件的后缀
		String ext = "jpg" ;	// 实际的开发之中一定要自己根据上传文件拆分出后缀
		// 3、加载要读取的资源配置文件（通过CLASSPATH加载）
		ClassPathResource classPathResource = new ClassPathResource("fastdfs.properties") ;
		// 4、进行上传初始化操作
		ClientGlobal.init(classPathResource.getClassLoader().getResource("fastdfs.properties").getPath()); 
		// 5、创建Tracker客户端类对象
		TrackerClient client = new TrackerClient() ;
		TrackerServer trackerServer = client.getConnection() ; // 获取tracker服务类对象
		StorageServer storageServer = null ;
		// 6、真正进行上传处理是通过StorageClient负责完成的
		StorageClient storageClient = new StorageClient(trackerServer, storageServer) ;
		// 7、上传文件需要定义相关的元数据信息
		NameValuePair [] metaList = new NameValuePair [3] ;
		metaList[0] = new NameValuePair("fileName", file.getName()) ;
		metaList[1] = new NameValuePair("fileExtName", ext) ;
		metaList[2] = new NameValuePair("fileLength", String.valueOf(file.length())) ;
		// 8、开始上传
		String[] upload_file = storageClient.upload_file(file.getPath(), ext, metaList) ;
		System.out.println(Arrays.toString(upload_file));
		trackerServer.close(); 
	}
}
```
> 返回结果：[group1, M00/00/00/wKiFllsqJlmARbdVAAEpgfM_70w114.jpg]

**方式二：**


```
import java.io.File;
import org.csource.common.NameValuePair;
import org.csource.fastdfs.ClientGlobal;
import org.csource.fastdfs.StorageClient1;
import org.csource.fastdfs.StorageServer;
import org.csource.fastdfs.TrackerClient;
import org.csource.fastdfs.TrackerServer;
import org.springframework.core.io.ClassPathResource;
public class TestUpload1 {
	public static void main(String[] args) throws Exception {
		// 1、定义要上传的文件，如果此时不是文件利用InputStream类来处理
		File file = new File("D:" + File.separator + "nophoto.jpg");
		// 2、要想上传则需要明确的知道文件的后缀
		String ext = "jpg"; // 实际的开发之中一定要自己根据上传文件拆分出后缀
		// 3、加载要读取的资源配置文件（通过CLASSPATH加载）
		ClassPathResource classPathResource = new ClassPathResource("fastdfs.properties");
		// 4、进行上传初始化操作
		ClientGlobal.init(classPathResource.getClassLoader().getResource("fastdfs.properties").getPath());
		// 5、创建Tracker客户端类对象
		TrackerClient client = new TrackerClient();
		TrackerServer trackerServer = client.getConnection(); // 获取tracker服务类对象
		StorageServer storageServer = null;
		// 6、真正进行上传处理是通过StorageClient负责完成的
		StorageClient1 storageClient = new StorageClient1(trackerServer, storageServer);
		// 7、上传文件需要定义相关的元数据信息
		NameValuePair[] metaList = new NameValuePair[3];
		metaList[0] = new NameValuePair("fileName", file.getName());
		metaList[1] = new NameValuePair("fileExtName", ext);
		metaList[2] = new NameValuePair("fileLength", String.valueOf(file.length()));
		// 8、开始上传
		String upload_file = storageClient.upload_file1(file.getPath(), ext, metaList);
		System.out.println(upload_file);
		trackerServer.close();
	}
}
```
> 返回结果：group1/M00/00/00/wKiFllsqJoSARbcLAAEpgfM_70w088.jpg

> 第一种结果返回一个数组，第二个返回相应字符串，可以更加方便的进行路径的拼凑。



## 取得文件信息

```
import org.csource.fastdfs.*;
import org.springframework.core.io.ClassPathResource;
public class TestFileInfo {
    public static void main(String[] args)throws Exception {
        ClassPathResource resource = new ClassPathResource("fastdfs.properties");
        ClientGlobal.init(resource.getClassLoader().getResource("fastdfs.properties").getPath());
        TrackerClient trackerClient = new TrackerClient();
        TrackerServer trackerServer = trackerClient.getConnection();
        StorageServer storageServer = null;
        StorageClient1 storageClient1 = new StorageClient1(trackerServer,storageServer);
        String fileName = "group1/M00/00/00/wKiFllsqPouAVD9BAAEpgfM_70w335.jpg";
        FileInfo fileInfo = storageClient1.get_file_info1(fileName);
        System.out.println("文件大小：" + fileInfo.getFileSize());
        System.out.println("创建日期：" + fileInfo.getCreateTimestamp());
        System.out.println("真实保存主机" + fileInfo.getSourceIpAddr());
        System.out.println(fileInfo);
        System.out.println(storageClient1.query_file_info1(fileName));
        trackerServer.close();
    }
}
```
> 返回结果：<br/>
文件大小：76161<br/>
创建日期：Wed Jun 20 19:46:19 CST 2018   <br/>
真实保存主机192.168.133.150 <br/>
source_ip_addr = 192.168.133.150, file_size = 76161, create_timestamp = 2018-06-20 19:46:19, crc32 = -213913780  <br/>
source_ip_addr = 192.168.133.150, file_size = 76161, create_timestamp = 2018-06-20 19:46:19, crc32 = -213913780 <br/>




## 文件删除

```
import java.io.File;
import java.util.Arrays;
import org.csource.common.NameValuePair;
import org.csource.fastdfs.ClientGlobal;
import org.csource.fastdfs.StorageClient1;
import org.csource.fastdfs.StorageServer;
import org.csource.fastdfs.TrackerClient;
import org.csource.fastdfs.TrackerServer;
import org.springframework.core.io.ClassPathResource;
public class TestDelete {
	public static void main(String[] args) throws Exception {
		String filePath = "group1/M00/00/00/wKiFllsqPouAVD9BAAEpgfM_70w335.jpg" ; // 要删除的文件
		// 加载要读取的资源配置文件（通过CLASSPATH加载）
		ClassPathResource classPathResource = new ClassPathResource("fastdfs.properties") ;
		// 进行上传初始化操作
		ClientGlobal.init(classPathResource.getClassLoader().getResource("fastdfs.properties").getPath()); 
		// 创建Tracker客户端类对象
		TrackerClient client = new TrackerClient() ;
		TrackerServer trackerServer = client.getConnection() ; // 获取tracker服务类对象
		StorageServer storageServer = null ;
		// 真正进行上传处理是通过StorageClient负责完成的
		StorageClient1 storageClient = new StorageClient1(trackerServer, storageServer) ;
		int code = storageClient.delete_file1(filePath) ;
		System.out.println(code); 
		trackerServer.close();  
	}
}
```

> 返回结果：0

> 返回结果是一个状态码，0表示删除成功，非0表示删除失败，2表示文件不存在。


## 盗链防范

为了防止盗链，可以加入一个token认证。所谓的防止盗链指的是生成一个随机的token，这个token在某一段时间内有效，超过了指定的时间将出现错误页面。防盗链的功能可以通过修改storage主机的配置实现。

```
import org.csource.fastdfs.ClientGlobal;
import org.csource.fastdfs.ProtoCommon;
import org.csource.fastdfs.TrackerClient;
import org.csource.fastdfs.TrackerServer;
import org.springframework.core.io.ClassPathResource;
public class TestToken {
	public static void main(String[] args) throws Exception {
		String filePath = "M00/00/00/wKiFllsqRg6AVKUrAAEpgfM_70w428_big.jpg"; // 要删除的文件
		//加载要读取的资源配置文件（通过CLASSPATH加载）
		ClassPathResource classPathResource = new ClassPathResource("fastdfs.properties");
		//进行上传初始化操作
		ClientGlobal.init(classPathResource.getClassLoader().getResource("fastdfs.properties").getPath());
		//创建Tracker客户端类对象
		TrackerClient client = new TrackerClient();
		TrackerServer trackerServer = client.getConnection(); // 获取tracker服务类对象
		int ts = (int) (System.currentTimeMillis() / 1000) ;
		StringBuffer accessUrl = new StringBuffer() ;
		accessUrl.append("http://") ;
		accessUrl.append(trackerServer.getInetSocketAddress().getHostString()) ;
		accessUrl.append("/group1/").append(filePath) ;
		accessUrl.append("?token=").append(ProtoCommon.getToken(filePath, ts, ClientGlobal.g_secret_key));
		accessUrl.append("&ts=").append(ts) ;
		System.out.println(accessUrl);
	}
}
```

> 返回结果： <br/>
http://192.168.133.132/group1/M00/00/00/wKiFllsqRg6AVKUrAAEpgfM_70w428_big.jpg?token=3f3329575f363b294b89ba405faaffec&ts=1529497646

