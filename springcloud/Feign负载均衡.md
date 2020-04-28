# Feign负载均衡

feign是声明式的web服务客户端，使的编写web服务客户端边的容易，只需要创建一个接口，然后在上面添加注解即可。

1. 新建工程
2. 修改pom文件，添加支持
3. 新建service接口添加@FeignClient("服务提供者 ")注解，修改api-pom,添加feign支持，服务提供者，
4. 消费者启动类@EnbaleFeignClients(basepackages)

![1566292733105](D:\Documents\Markdown文档\springcloud\1566292733105.png)

