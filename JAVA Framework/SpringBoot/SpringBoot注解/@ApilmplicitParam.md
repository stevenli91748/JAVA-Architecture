



用于方法上， 为单独的请求参数进行说明。

```java

@ApiimplicitParams({@ApiimplicitParam(name＝” id ”， value＝” 用尸ID ”， dataType="string", paramType =” query",required=true, defaultValue=”1 ”)
})
@ApiResponses({ @ApiResponse(code = 200, message = "OK ” , response = UserDto .class) } )
@GetMapping (”/user")
public UserDto getUser(@RequestParam (” id ”) String id) {
return new UserDto();

}

```
