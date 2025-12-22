
第一版：消费数据的服务使用的是Java的SpringBoot框架编写的，所以一开始首选使用SpringBoot相关包的kafka依赖kafka-spring-boot-starter


```java

@KafkaMessageListener(
        topic = "",
        consumerGroup = "",
        bootstrapServers = "")
public class UvMessageListener implements KafkaListener<String> {
    @Override
    public void onMessage(String message) {
      
    }
}
```

问题：将topic和group配置上到预发环境后测试发现消费不到任何数据，但是换成别的业务的topic则能正常消费到数据。

后来通过查询文档发现，UV活跃事件的数据源是数仓，消费数仓发送的Kafka数据需要接入Kerberos权限认证。然后就在研究如果将这套Kerberos权限认证参数集成在现有Kafka初始化的地方，通过查看@KafkaMessageListener注解中的参数，以及kafka-spring-boot-starter包中初始化方法initMomoKafkaConsumer均没有找到对应Kerberos权限属性所需初始化的参数，也没找到支持自定义传参数初始化的地方。最后找寻无果打算换一种kafka的初始化方式。

方案二：既然SpringBoot集成的Kafka包不支持Kerberos权限认证初始化，那就找Spring原生的
Kafka依赖包spring-kafka。
```java
import java.util.Properties;

import org.springframework.core.io.ClassPathResource;
import org.springframework.core.io.support.PropertiesLoaderUtils;
import org.springframework.kafka.config.ConcurrentKafkaListenerContainerFactory;
import org.springframework.kafka.config.KafkaListenerContainerFactory;
import org.springframework.kafka.core.DefaultKafkaConsumerFactory;
import org.springframework.kafka.listener.ConcurrentMessageListenerContainer;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.kafka.core.ConsumerFactory;

@Configuration
public class UvMessageListener {

    @Bean("uvKafkaConsumerFactory")
    public ConsumerFactory<String, String> consumerFactory() throws Exception {
        Properties properties = PropertiesLoaderUtils.loadProperties(new ClassPathResource("/uv-kafka-consumer.properties"));
        return new DefaultKafkaConsumerFactory(properties);
    }

    @Bean("uvKafkaListenerContainerFactory")
    public KafkaListenerContainerFactory<ConcurrentMessageListenerContainer<String, String>> uvKafkaListenerContainerFactory() throws Exception {
        ConcurrentKafkaListenerContainerFactory<String, String> factory = new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        return factory;
    }

    @KafkaListener(topics = {""}, containerFactory = "uvKafkaListenerContainerFactory")
    public void consumerListener(String message) {
  
    }
   
}
```

将配置文件中的Kerberos权限属性存储到一个Properties对象中，然后创建一个Kafka消费者工厂对象，并将Properties传递给它进行初始化。再创建一个Kafka监听器容器工厂对象，并使用刚才初始化的消费者工厂对象。最后，定义了一个consumerListener方法，并使用@KafkaListener注解将其标记为Kafka消息监听器。

```properties
bootstrap.servers=
group.id=
auto.offset.reset=earliest
enable.auto.commit=false
max.poll.records=500
fetch.min.bytes=1048576
max.partition.fetch.bytes=1048576
security.protocol=SASL_PLAINTEXT
#sasl.kerberos.min.time.before.relogin=86400000
sasl.kerberos.min.time.before.relogin=60000
sasl.kerberos.service.name=kafka
session.timeout.ms=240000
key.deserializer=org.apache.kafka.common.serialization.StringDeserializer
value.deserializer=org.apache.kafka.common.serialization.StringDeserializer

问题：启动报错

> java.lang.IllegalArgumentException: Could not find a 'KafkaClient' entry in the JAAS configuration. System property 'java.security.auth.login.config' is not set

原因：文档中说明，启动的时候需要把两个配置文件也加载到JVM中，但是我已经将命令添加到项目中start.sh中还是提示找不到这个文件

```shell
java -Djava.security.krb5.conf=/home/deploy/moaservice/platform_idfa-service/krb5.conf \
-Djava.security.auth.login.config=/home/deploy/moaservice/platform_idfa-service/crux_jaas.conf
```

解决：项目中的start.sh在部署的时候并不会真实调用，而是调用的陌陌部署平台的启动脚本，所以需要在陌陌部署平台上更新启动命令。

问题：服务启动发现初始化的时候报错。

> ERROR []  NextRefresh: 2023-03-14T15:13:55.669+0800 is in the past: exiting refresh thread. Check clock sync between this host and KDC - (KDC's clock is likely ahead of this host). Manual intervention will be required for this client to successfully authenticate. Exiting refresh thread.
> 
粗略阅读错误信息显示应该跟时钟相关的问题.

错误信息上来看就是时钟不同步导致，于是上服务所在容器使用date命令查看对应时间，并没有发现什么异常，于是找数仓相关Kafka同学进行反馈。最后数仓侧的同学通过服务认证的日志比对定位到问题原因。

原因：sasl.kerberos.min.time.before.relogin=86400000设置的值过大导致时钟出现不同步的问题。

解决：sasl.kerberos.min.time.before.relogin=60000设置为默认值
至此成功消费到对应topic的数据
