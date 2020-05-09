


# @GetMapping与 @PostMapping

 Spring4.3中引进了｛@GetMapping、@PostMapping、@PutMapping、@DeleteMapping、@PatchMapping｝，来帮助简化常用的HTTP方法的映射，并更好地表达被
 注解方法的语义。

  @GetMapping是一个组合注解，是@RequestMapping(method = RequestMethod.GET)的缩写。该注解将HTTP Get 映射到 特定的处理方法上。

  @PostMapping是一个组合注解，是@RequestMapping(method = RequestMethod.POST)的缩写。该注解将HTTP Post映射到 特定的处理方法上

  @PutMapping是一个组合注解，是@RequestMapping(method = RequestMethod.put)的缩写。该注解将HTTP Put映射到 特定的处理方法上
  
  @DeleteMapping是一个组合注解，是@RequestMapping(method = RequestMethod.Delete)的缩写。该注解将HTTP Delete映射到 特定的处理方法上
  
  @PatchMapping是一个组合注解，是@RequestMapping(method = RequestMethod.Patch)的缩写。该注解将HTTP Patch映射到 特定的处理方法上
  
  
