
history 熔断器失效解决方案 https://github.com/alibaba/spring-cloud-alibaba/wiki/Sentinel
高版本需要替换组件为Sentinel
```
## 消息服务
/**
 * @author AoDeng
 * @describe
 * @date 2022/2/17
 */
@RestController
@RequestMapping("messageTest")
public class MessageControllerTest {

    @RequestMapping("getTestStr")
    public AjaxResult getTestStr(@RequestParam("a") String a){
        return AjaxResult.error(a+"<<<>>>"+a.length());
    }
}

## 定义提供外部服务的接口，配置熔断策略
@FeignClient(name = "remoteMessageService",fallback = RemoteMessageFallbackFactory.class,configuration = FeignConfiguration.class)
public interface RemoteMessageService {

    @RequestMapping("getTestStr")
    public AjaxResult getTestStr(@RequestParam("a") String a);
}

/**
 * @author AoDeng
 * @describe
 * @date 2022/2/17
 */
public class RemoteMessageFallbackFactory implements RemoteMessageService {
    @Override
    public AjaxResult getTestStr(String a) {
        return AjaxResult.error("echo fallback");
    }
}

public class FeignConfiguration {
    @Bean
    public RemoteMessageFallbackFactory remoteMessageFallbackFactory(){
        return new RemoteMessageFallbackFactory();
    }
}

## job服务调用remote的服务
/**
 * @author AoDeng
 * @describe
 * @date 2022/2/17
 */
@RestController
@RequestMapping("jobTest")
public class MessageControllerTest {

    private final RemoteMessageService remoteMessageService;

    public MessageControllerTest(
        RemoteMessageService remoteMessageService){
        this.remoteMessageService=remoteMessageService;
    }

    @RequestMapping(name = "test",value = "test")
    public AjaxResult test(@RequestParam("a") String a){
        return remoteMessageService.getTestStr(a);
        //return remoteMessageCenterService.test(a);
    }
}

#job服务添加依赖
        <!-- SpringCloud Alibaba Sentinel -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
        </dependency>

        <!-- SpringCloud Openfeign -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
#job服务消费端配置开启，注意需要配置 sentinel 不是history
feign:
  sentinel:
    enabled: true
```
