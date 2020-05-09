
# @Table

常用的两个属性：

声明此对象映射到数据库的数据表，通过它可以为实体指定表(talbe)

1、name 用来命名 当前实体类 对应的数据库 表的名字

@Table(name = “tab_user”）

2、uniqueConstraints 用来批量命名唯一键

其作用等同于多个：@Column(unique = true)

@Table(name = “tab_user”,uniqueConstraints = {@UniqueConstraint(columnNames={“uid”,“email”})})
