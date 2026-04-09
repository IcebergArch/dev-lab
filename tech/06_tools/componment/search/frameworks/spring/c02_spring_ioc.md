# IOC

降低耦合、容器管理生命周期

Class  &  Class

=>

Bean(Class_Instance) + BeanRelaction


## 组件

- BeanFactory 是 Spring 框架的基础设施，面向 Spring 本身；
- ApplicationContext 面向使用Spring 框架的开发者，几乎所有的应用场合我们都直接使用 ApplicationContext 而非底层的BeanFactory。


BeanDefinitionRegistry：spring 配置文件每一个节点元素都通过 

BeanDefinition 记录Bean配置信息

BeanFactory：获取Bean

ListableBeanFactory：容器的暴漏接口

HierarchicalBeanFactory：分层级容器，子容器可以通过接口方法访问父容器

ConfigurableBeanFactory：增强了 IoC 容器的可定制性

AutowireCapableBeanFactory 自动装配

SingletonBeanRegistry 运行期间注册单例 Bean
依赖日志框架：必须配置一种日志

## ApplicationContext

ApplicationContext 面向开发应用

ApplicationContext 由 BeanFactory 派生而来，提供了更多面向实际应用的功能。
ApplicationContext 继承了 HierarchicalBeanFactory 和ListableBeanFactory 接口，在此基础上，还通过多个其他的接口扩展了 BeanFactory 的功能

## WebApplication
WebApplicationContext 是专门为 Web 应用准备的，它允许从相对于 Web 根目录的路径中装载配置文件完成初始化工作。从 WebApplicationContext 中可以获得ServletContext 的引用，整个 Web 应用上下文对象将作为属性放置到ServletContext 中，以便 Web 应用环境可以访问 Spring 应用上下文。

