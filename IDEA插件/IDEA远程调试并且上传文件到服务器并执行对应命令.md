# IDEA远程调试并且上传文件到服务器并执行对应命令

## 使用原因

社区版本的IDEA阉割掉了原有的ssh链接上传功能，这里通过使用阿里云的插件来实现对应功能的使用

<img title="" src="img/b3d79279fe8a81b6445d07f196c1de4754aa2b20.png" alt="" data-align="center">

在安装完成后会在设置中多出一个tools/aliyun tools，在其中选择对应选项

![](img/e4e28fb79048f549809d1eb458c89a787efa0c4f.png)

在下边栏中点击add host![](img/840164866789c648e334e992051adcbf067ce3a8.png)

既可以对数据进行添加

![](img/6f8b25fb2aa861ff2163e668c39ec2face822402.png)

添加完成并且保证正常连接上之后，再回到进行添加路径

![](img/e548663a01ee4a6d57f1a8ad33bed059601ad4b6.png)

![](img/7aad071e20bfd71c5cdbfd6744933ceb6425d00f.png)

完成后既可以实现直接maven打包构建直接上传并且可以在上传完成之后直接执行命令

例：`nohup java -jar /root/proxy/shop/proxyshop-0.0.1-SNAPSHOT.jar > /root/proxy/shop/temp.txt &`在上传完成后直接启动程序非常之优雅


