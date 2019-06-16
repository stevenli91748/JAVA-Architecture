* [标准设备输入/输出](#标准设备输入输出)
* [文件基本处理](#文件基本处理)
* [输入输出流](#输入输出流)

# File类

<details>
<summary>File类型中定义了什么方法来创建一级目录</summary>
  mkdirs() //在指定位置创建目录，这会创建路径中所有不存在的目录。
  
</details>

<details>
<summary>File类型中定义了什么方法来判断一个文件是否存在</summary>
  
 exists()         //文件或文件夹是否存在。
 
</details>

<details>
<summary>如何用Java代码列出一个目录下所有的文件？</summary>
  
```java

 //只查询当前目录
public class Main {

    public static void main(String[] args) {
        File f = new File("d:");
        for(File temp : f.listFiles()) {
            if(temp.isFile()) {
                System.out.println(temp.getName());
            }
        }
    }
}

// 如果需要对文件夹继续展开
    public static void main(String[] args) {
        showDirectory(new File("d:\\file"));
    }

    public static void showDirectory(File f) {
        _walkDirectory(f, 0);
    }

    private static void _walkDirectory(File f, int level) {
        if(f.isDirectory()) {
            for(File temp : f.listFiles()) {
                _walkDirectory(temp, level + 1);
            }
        }
        else {
            for(int i = 0; i < level - 1; i++) {
                System.out.print("\t");
            }
            System.out.println(f.getName());
        }
    }
    
```

在Java 7中可以使用NIO.2的API来做同样的事情，代码如下所示：

```java

public static void main(String[] args) throws IOException {
       Path initPath = Paths.get("/Users/Hao/Downloads");
       Files.walkFileTree(initPath, new SimpleFileVisitor<Path>() {

           @Override
           public FileVisitResult visitFile(Path file, BasicFileAttributes attrs)
                   throws IOException {
               System.out.println(file.getFileName().toString());
               return FileVisitResult.CONTINUE;
           }

       });
   }
  
  
```

</details>




1.讲讲IO里面的常见类，字节流、字符流、接口、实现类、方法阻塞。

2.讲讲NIO。

3.String编码UTF-8和GBK的区别?

4.什么时候使用字节流、什么时候使用字符流?

5.递归读取文件夹下的文件，代码怎么实现

15.java 中 IO 流分为几种？

16.BIO、NIO、AIO 有什么区别？
