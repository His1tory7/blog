---
title: Groovy 获取Spring Beans
tag: JAVA奇技淫巧
---
<!--more-->
## 一、前文
> Groovy 中获取 Spring Beans 注入
## 二、实践
> 引入 SpringUtils

```JAVA
@Component
public final class SpringUtils implements BeanFactoryPostProcessor
{
  /** Spring应用上下文环境 \*/
  private static ConfigurableListableBeanFactory beanFactory;

  @Override
  public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException
  {
    SpringUtils.beanFactory = beanFactory;
  }
  @SuppressWarnings("unchecked")
  public static <T> T getBean(String name) throws BeansException
  {
    return (T) beanFactory.getBean(name);
  }
  public static <T> T getBean(Class<T> clz) throws BeansException
  {
    T result = (T) beanFactory.getBean(clz);
    return result;
  }
}





```

> Groovy 中获取对应Bean
```JAVA
//示例
IOrderService orderService = SpringUtils.getBean(IOrderService.class);

```




