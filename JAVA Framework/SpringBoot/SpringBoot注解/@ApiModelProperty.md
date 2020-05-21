
@ApiModelProperty（）用于字段，表示对model 属性的说明


```java

@Data
@ApiModel (value =”com.cxytiandi.auth.param.AddUserParam", description ＝” 新增用户参数”）
public class AddUserParam {
@Ap 工ModelPropert y(value ＝”工D")
private String id;
@ApiModelProperty(value ＝” 名称”）
private String name;
@ApiModelProperty(value ＝” 年龄”）
private int age;

}

```
