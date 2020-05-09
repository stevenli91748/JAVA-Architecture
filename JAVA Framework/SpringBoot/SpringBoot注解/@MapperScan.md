# @MapperScan

要扫描mapper类包的路径，还可以扫描多个包。如：

@MapperScan({“com.kfit.demo”,“com.kfit.user”})

如果mapper类没有在Spring Boot主程序可以扫描的包或者子包下面，可以使用如下方式进行配置：

@MapperScan({“com.kfit…mapper”,“org.kfit…mapper”})
