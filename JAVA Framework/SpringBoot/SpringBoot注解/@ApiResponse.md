



@ApiResponse 用于方法上，说明接口响应的一些信息

```java

@ApiResponses({ @ApiResponse(code = 200, message= "OK”, response = UserDto.class) } )
@PostMapping (”/user ”)
public UserDto addUser(@ApiParam(value ＝” 新增用户参数”， required = true) @
RequestBody AddUserParam par缸n) {



}

```
