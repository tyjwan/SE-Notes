# Spring boot 启动详解
***

- SpringApplication.run(Application.class, args);
  - this.prepareContext(context, environment, listeners, applicationArguments, printedBanner);打印logo和线程
  - this.refreshContext(context);很多的初始化配置好像就在这了
  - this.afterRefresh(context, applicationArguments);
    - this.onRefresh();链接配置相关
    - this.finishBeanFactoryInitialization(beanFactory);restful接口相关