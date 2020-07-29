# 页面准备

1. 首页配置
   1. 所有页面的静态资源需要通过Thymeleaf接管；
   2. `@{}`;
2. 页面国际化
   1. 我们需要配置i18n文件;
   2. 我们如果要想在项目中进行按钮切换语言，还需要自定义一个组件`localeResolver` ;
   3. 记得将我们自己写的组件配置到spring容器 `bean` ;
   4. `#{}` 
3. 登录+拦截器
4. 员工列表展示
   1. 公共页面抽取
      1. `th:fragment="sidebar"` 
      2. `th:replace="~{commons/commons::sidebar(active='list.html')}">` 
      3. 传递参数在后面使用()
   2. 列表循环展示
      1. 用到thymeleaf的时间工具类 #datas
5. 添加员工/删改查
   1. 添加员工按钮
   2. 跳转添加页面
   3. 添加成功
   4. 返回首页
6. 404页面

[github地址](https://github.com/zsy0216/SpringBoot-Study)

