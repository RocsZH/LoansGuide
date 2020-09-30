基于Spring Cloud Discovery服务注册发现和Ribbon负载均衡，程序入口只需加上@EnableDiscoveryClient注解；
基于Feign和RestTemplate调用，可以通过Rest方式进行灰度发布。
spring.application.strategy.feign.core.header.transmission.enabled=true
spring.application.strategy.rest.template.core.header.transmission.enabled=true

注册中心(服务注册发现中心)：Nacos;
配置中心(远程配置中心)：Nacos;
网关：Spring Cloud Gateway;
熔断限流降级(权限)组件(防护组件)：Sentinel;
符合OpenTracing规范的调用链(追踪)中间件：Skywalking Java Agent;
指标(监控)中间件：Prometheus Micrometer Grafana;
灰度蓝绿(发布)组件|灰度发布和路由功能组件：Nepxion Discovery；
Java Agent组件封装：解决异步跨线程调用的ThreadLocal上下文传递的异步跨线程Agent的解决方案。
FF4j特性开关开发框架|功能开关组件。

实现网关和服务的灰度蓝绿发布、路由过滤、流量权重、服务实例隔离|屏蔽(不下线)、流量调拨、同城双活多机房(双云双活跨机房)切换、数据库和消息队列切换、A/B测试 ...
灰度发布和路由，
流量无损发布之灰度发布(金丝雀发布，权重发布)、蓝绿发布(匹配发布)和有损发布之滚动发布。
Nepxion Discovery 6.1.0, 维护中
Spring Cloud Hoxton,
Spring Boot 2.3.x.RELEASE,
Spring Cloud Alibaba 2.2.x.RELEASE.
“策略下内置Header来决策蓝绿和灰度，可以代替外部传入Header”，这块功能Spring Cloud Gateway在Finchley版不支持。


依赖包(端到端的策略插件包)：
配置文件|规则文件：
(发布)规则(路由)策略订阅与推送：

版本，区域，IP地址和端口，通过业务参数在过滤器或策略类中自定义：
以Spring Cloud Gateway为例，继承DefaultGatewayStrategyRouteFilter ，通过外界传入的Header(用户名user)做策略，
继承DefaultDiscoveryEnabledStrategy，通过外界传入的Header(手机号 mobile)做策略。
基于(全局)订阅式和网关为触点的(全链路)传递Header方式的全链路灰度发布和路由。
基于组(Group)和黑白名单的全链路服务隔离。
方式：Header全链路传递，动态变更元数据，全局订阅。
基于网关为触点的Header传递的全链路灰度路由，适用于网关前置部署方式的企业。域网关部署模式下，最适用于该方式；
非域网关部署模式下，开启并行灰度路由下的版本优选策略。

非域网关模式，即不同部门的服务直接调用，不同部门的服务必须通过网关调用转发。
非域网关下并行灰度蓝绿发布。

元数据：
微服务的名称(服务名)、所属组名(可以基于服务名前缀自动创建灰度组名)、
版本号(可以基于Git插件自动创建灰度版本号)、服务的所属区域region、环境env、
可用区(机房)zone。
通过前端传入灰度路由策略：
1. n-d-version 版本匹配策略
2. n-d-region 区域匹配策略
3. n-d-address IP地址和端口匹配路由
4. n-d-version-weight 版本权重策略
5. n-d-region-weight 区域权重策略
6. n-d-id-blacklist 服务下线实时性的流量绝对无损，全局唯一ID屏蔽策略
7. n-d-address-blacklist 服务下线实时性的流量绝对无损，IP地址和端口屏蔽策略
8. n-d-env (不属于灰度蓝绿范畴的Header，只要外部传入就会全程传递) 环境隔离下动态环境匹配策略
使用者可以自行定义要传递的调用链参数，例如：trace-id, span-id等；也可以自行定义要传递的业务调用链参数，例如：mobile, user等。


使用Discovery框架的新服务和原生框架的旧服务兼容。
自动化测试利用控制台，实现配置的自动更新和清除。
使用Nepxion Polaris集成式脚手架，极大降低Nepxion Discovery接入成本。
DiscoveryContrib是基于Nepxion Discovery的第三方非微服务范畴中间件的灰度发布，包括数据库和消息队列等的贡献项目。
discovery-contrib-starter-shardingsphere
discovery-contrib-starter-rocketmq
Discovery project 灰度发布和路由治理界面 DiscoveryUI。
跨云的服务注册和配置读取通过DevOps修改相关配置驱动即可，一套SDK体系可以同时适配和运行在不同的云上。

统一的Parent定义；当接入Polaris Parent的时候，版本号以Polaris Parent定义为优先，否则以Polaris SDK定义为准。
框架3层：【框架】核心分层组装与聚合，同类型的核心【组件】切换以及聚合和组装，具体基础【组件】的封装；
5个业务|应用层：API层|微服务二方包(api)，微服务层(service)，Spring Cloud Gateway网关层(gateway)，控制台(console)，(自动化)测试端(test)。
零Spring Cloud经验的业务开发人员也可以快速上手研发基于Spring Cloud微服务技术栈的业务服务，最大程度上减轻业务人员的压力。
如果该组件不希望被绑死在框架层，也可以暴露给业务层，由业务开发自行引入。


com/nepxion/polaris/component/env/constant/PolarisEnvConstant.java:9
 public static final String DOMAIN_VALUE = "nepxion.com";
 
Pom GroupId和src/main/resources/META-INF/spring.factories，以及换src/main/java/下的Java包目录名：
    com.nepxion.polaris

指指标标组组件件切切换换(未激活)
You can select one of following polaris metrics components, such as Micrometer

当配置组件切换到Apollo的时候，需要激活Apollo注解@EnableApolloConfig；非Apollo配置组件需要注释掉该注解，否则无法编译通过。

根据企业的实际情况，把组件的四个环境域名或者IP地址一一做更改，
所有的组件都支持四个环境（DEV | FAT | UAT | PRO），分别对应开发环境、测试环境、准生产环境、生产环境。
Swagger功能需要在生产环境关闭，需要在PRO配置文件里关闭。多环境的域名支持：
src/main/resources/META-INF
无需配置 组件名 -common .properties 无需配置
组件名-pro-可选的区域名.根域 组件名-pro.properties spring.cloud.nacos.discovery.server-addr=nacos-pro${region}.${domain}
除了服务名和端口号之外，业务层原则上不需要再添加Spring Cloud和中间件其它配置，当然也可以覆盖掉默认内置的配置。为了避免引起业务层的配置无法覆盖掉默认内置的配置，请在bootstrap.properties定义想要覆盖的配置。
-Dmetadata.version=20200818-001 -Dmetadata.region=region1 -Dmetadata.env=env1 -Dmetadata.zone=zone1 -D nepxion.banner.shown.ansi.mode=true

执行“Polaris网关全链路侦测测试用例”.


属于全链路蓝绿部署的范畴。既适用于Zuul和Spring Cloud Gateway网关，也适用于Service微服务，一般来说，网关已经加了，服务上就不需要加，当不存在的网关的时候，服务就可以考虑加上.
增加组合式的灰度策略，支持版本匹配、区域匹配、IP地址和端口匹配、版本权重匹配、区域权重匹配。
当前端（例如：APP）和后端微服务同时存在多个版本时，可以执行“前端灰度&网关灰度路由组合式策略”。
在代码里根据不同的Header选择不同的路由路径。
优选的方式，对版本号进行排序，取第一个版本号，所以此方案的前置条件是必须版本号是规律的有次序，例如，以时间戳的方式。
新上线的服务实例版本为gray，即默认是灰度版本。等灰度成功后，通过注册中心的Open API接口变更服务版本为stable，或者在注 册中心界面手工修改。

<service service-name="discovery-guide-service-d" tag-key="zone" tag-value="zone1" key="Rocket MQ" value="queue1"/>
<service service-name="discovery-guide-service-a" tag-key="version" tag-value="1.1" key="Shard ingSphere" value="db2"/>
控制台接口 : Console Endpoint：
http://localhost:6001/swagger-ui.html#/2551121046214882550921475





