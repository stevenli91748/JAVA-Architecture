

# @GeneratedValue

存在的意义主要就是为一个实体生成一个唯一标识的主键(JPA要求每一个实体Entity,必须有且只有一个主键),@GeneratedValue提供了主键的生成策略。@GeneratedValue注解有两个属性,分别是strategy和generator,其中generator属性的值是一个字符串,默认为"",其声明了主键生成器的名称(对应于同名的主键生成器@SequenceGenerator和@TableGenerator)。

```java

@Table(name="user")
@Data
public class User {
    @Id
    @GeneratedValue(strategy=GenerationType.IDENTITY)
    private Long id;
    private String userName;//用户名
    private String password;//密码
    private String name;//姓名
    private Date birthday;//生日
    private int gender;//性别： 0女性，1男性
    private Date created;//创建时间
    private Date updated;//更新时间
}

```
