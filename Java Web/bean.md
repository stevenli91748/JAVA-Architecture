

* [bean 中的链式风格](#bean 中的链式风格)

# bean 中的链式风格

    什么是链式风格？我来举个例子，看下面这个 Student 的 bean：

public class Student {
    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public Student setName(String name) {
        this.name = name;
        return this;
    }

    public int getAge() {
        return age;
    }

    public Student setAge(int age) {
        return this;
    }
}
仔细看一下 set 方法，这样的设置便是 chain 的 style，调用的时候，可以这样使用：

Student student = new Student()
        .setAge(24)
        .setName("zs");

相信合理使用这样的链式代码，会更多的程序带来很好的可读性，那看一下如果使用 lombok 进行改善呢，请使用 @Accessors(chain = true)，看如下代码：

@Accessors(chain = true)
@Setter
@Getter
public class Student {
    private String name;
    private int age;
}

这样就完成了一个对于 bean 来讲很友好的链式操作。
