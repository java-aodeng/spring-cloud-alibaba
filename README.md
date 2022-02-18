
history 熔断器失效解决方案 https://github.com/alibaba/spring-cloud-alibaba/wiki/Sentinel
```
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

#服务消费端配置开启
feign:
  sentinel:
    enabled: true
```
