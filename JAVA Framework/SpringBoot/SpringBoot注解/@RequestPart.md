**RequestPart：提供对“multipart/form-data”请求的全面支持，支持Servlet 3.0文件上传（javax.servlet.http.Part）、支持内容的HttpMessageConverter
（即根据请求头的Content-Type，来判断内容区数据是什么类型，如JSON、XML，能自动转换为命令对象），比@RequestParam更强大（只能对请求参数数据绑定，
key-alue格式），而@RequestPart支持如JSON、XML内容区数据的绑定；**
