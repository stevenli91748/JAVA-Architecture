
@ApiOperation 用在Controller 里的方法上，说明方法的作用， 每一个接口的定义

```java

@ApiOperation(value ＝” 新增用户”， notes ＝＂ 详细描述”）
public UserDto addUser( @ApiParam(value ＝” 新增用户参数”， required = true) @
RequestBody AddUserParam par缸n) {



}


```
