


@ApiParam 用于Controller 中方法的参数说明

```java

@PostMapping (”/user")
public UserDto addUser( @ApiParam(value ＝” 新增用户参数”， required = true) 
@RequestBody AddUserParam par缸n) {
System.err.println(par出L getN缸ne());
return new UserDto();


```
