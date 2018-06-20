### FastDFS工具类：  <br/>
> **包含方法**：<br/>
    |- 文件上传：public static String upload(MultipartFile file){}  <br/>
    |- 文件删除：public static Integer delete(String filePath){} <br/>
    |- 获取上传文件信息：public static FileInfo getInfo(String filePath){}  <br/>
    

```
import org.csource.common.NameValuePair;
import org.csource.fastdfs.*;
import org.springframework.core.io.ClassPathResource;
import org.springframework.web.multipart.MultipartFile;

public class FastDFS {
    /**
     * 文件上传
     * @param file
     * @return  上传文件路径
     */
    public static String upload(MultipartFile file){
        try {
            // 需要获得文件的后缀的名称信息，所有的上传文件都有mime类型；
            String ext = file.getOriginalFilename().substring(file.getOriginalFilename().lastIndexOf(".") + 1);
            // 通过ClassPath路径获取要使用的配置文件，实现了资源的定位
            ClassPathResource classPathResource = new ClassPathResource("config/fastdfs.properties");
            // 进行客户端访问的整体配置，需要知道配置文件的完整路径
            ClientGlobal.init(classPathResource.getClassLoader().getResource("config/fastdfs.properties").getPath());
            // FastDFS的核心操作在于tracker处理上，所以此时需要定义Tracker客户端
            TrackerClient trackerClient = new TrackerClient();
            // 定义TrackerServer的配置信息
            TrackerServer trackerServer = trackerClient.getConnection();
            // 在整个FastDFS之中真正负责干活的就是Storage
            StorageServer storageServer = null;
            StorageClient1 storageClient1 = new StorageClient1(trackerServer, storageServer);
            // 定义上传文件的元数据
            NameValuePair[] metaList = new NameValuePair[3];
            metaList[0] = new NameValuePair("fileName", file.getName());
            metaList[1] = new NameValuePair("fileExtName", ext);
            metaList[2] = new NameValuePair("fileLength", String.valueOf(file.getSize()));
            // 如果要上传则使用trackerClient对象完成
            String upload_file = storageClient1.upload_file1(file.getBytes(), ext, metaList);
            trackerServer.close();
            return upload_file;
        }catch(Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    /**
     * 文件删除
     * @param filePath
     * @return  删除的状态码  
     * 返回为0表示成功删除，返回为2表示文件不存在，返回非0表示删除失败
     */
    public static Integer delete(String filePath){
        try{
            ClassPathResource resource = new ClassPathResource("config/fastdfs.properties");
            ClientGlobal.init(resource.getClassLoader().getResource("config/fastdfs.properties").getPath());
            // 创建Tracker客户端类对象
            TrackerClient client = new TrackerClient() ;
            TrackerServer trackerServer = client.getConnection() ; // 获取tracker服务类对象
            StorageServer storageServer = null ;
            // 真正进行上传处理是通过StorageClient负责完成的
            StorageClient1 storageClient = new StorageClient1(trackerServer, storageServer) ;
            int code = storageClient.delete_file1(filePath) ;
            //System.out.println(code);
            trackerServer.close();
            return  code;  //返回为0表示成功删除，返回为2表示文件不存在，返回非0表示删除失败
        }catch (Exception e){
            e.printStackTrace();
            return  null;
        }
    }

    /**
     * 获取文件信息
     * @param filePath
     * @return 文件大小：fileInfo.getFileSize() 创建日期：fileInfo.getCreateTimestamp() 真实保存主机：fileInfo.getSourceIpAddr()
     */
    public static FileInfo getInfo(String filePath){
        try {
            ClassPathResource resource = new ClassPathResource("config/fastdfs.properties");
            ClientGlobal.init(resource.getClassLoader().getResource("config/fastdfs.properties").getPath());
            TrackerClient trackerClient = new TrackerClient();
            TrackerServer trackerServer = trackerClient.getConnection();
            StorageServer storageServer = null;
            StorageClient1 storageClient1 = new StorageClient1(trackerServer,storageServer);
            FileInfo fileInfo = storageClient1.get_file_info1(filePath);
            trackerServer.close();
            return fileInfo;  //文件大小：fileInfo.getFileSize() 创建日期：fileInfo.getCreateTimestamp() 真实保存主机：fileInfo.getSourceIpAddr()
        }catch (Exception e){
            e.printStackTrace();
            return null;
        }

    }
}
```
