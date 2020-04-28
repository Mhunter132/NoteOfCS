---
title:  testNG-整合springboot
date: 2019-11-21
categories: 
- TestNG
tags: 
- TestNG
---

昨天就是redisUtils不能实例化，显示

```java
@Value｛$redis.server｝
```

注入失败，无法读取配置文件，但是我的web程序却能实例化。期初我在测试类上面加了这些注解

```
@ContextConfiguration(classes = ApiApplication.class)
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest// ioc
@WebAppConfiguration
```

要么报无法加载application，要么就是报无法实例化redisUtils。关机回家睡觉第二天就能跑起来，而且还没操作数据库（JPA）,期初dao方法提示空指针异常。进过一晚上，我在测试就能这正常启动

```
@ContextConfiguration(classes = ApiApplication.class) //没有这个注解也能启动（这个注解主要是读取配置bean）
@SpringBootTest// 提供测试ioc环境
```

```java

@ContextConfiguration(classes = ApiApplication.class)
@SpringBootTest// ioc
public class TaskApplicationTests extends AbstractTestNGSpringContextTests {

    @Autowired
    private Cm03PartContentService cm03PartContentService;

    @Test(dataProvider = "createNews")


           for (int i = 1; i <= 9; i++) {
               String url = "http://api.dagoogle.cn/news/nlist?cid=" + i + "&page=1&psize=10";
               String newsInfoUrl = "http://api.dagoogle.cn/news/ndetail?aid=";
               String partId = "13272310455";
               HttpClient httpClient = new HttpClient();
               Response response = httpClient.get(url);
               JSONObject jsonObjecttemp = response.asJSONObject().getJSONObject("data");
               List<NewsEntity> newsEntities = JSONArray.parseArray(jsonObjecttemp.get("list").toString(), NewsEntity.class);
               System.out.println(newsEntities.toString());
               for (NewsEntity newstemp : newsEntities) {
                   String aid = newstemp.getAid();
                   Response response1 = httpClient.get(newsInfoUrl + aid);
                   NewsInfo newsInfo = JSONObject.parseObject(response1.asJSONObject().getJSONObject("data").toString(), NewsInfo.class);
                   Cm03PartContent cm03PartContent = new Cm03PartContent();
                   cm03PartContent.setCm03ContentId(UUID.randomUUID().toString());
                   cm03PartContent.setCm03PartId(partId);
                   cm03PartContent.setCm03FullTitile(newsInfo.getTitle());
                   cm03PartContent.setCm03Date(new Date());
                   cm03PartContent.setCm03Summary(newsInfo.getSummary());
                   cm03PartContent.setCm03Text(newsInfo.getContent());
                   cm03PartContent.setCm03PicUrl1(newsInfo.getHeadpic());
                   cm03PartContent.setCm03EntId(10137);
                   cm03PartContent.setCm03OrgId("b64069f6c3a84f8a863573b17a29b13f");
                   cm03PartContent.setCm03IsDeleted(0);
                   cm03PartContent.setCm03AuthorAcctId("00003fb952004bc3adf90c4a724ad4c4");
                   cm03PartContentService.doSave(RouteInfor.buildRouteInfoInt(10137), cm03PartContent, null); //dao方法
                   System.out.println(newsInfo.getHeadpic());

               }

       }
  }

    @DataProvider(name = "createNews")
    public Object[][] createNewsData(){
        return new Object[][]{
          new Object[]{"cid=1"}
        };
    }
    
    
    @dataprovider数据没用到，期初是想用来传递参数
```

一定要继承

```java
AbstractTestNGSpringContextTests  
```

这个类。

经过自己的实验注释掉junit的注解

```
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest
```

就会报无法出现 无法  load application 异常，这两个注解用一个就好，我理解的是必须有类能提供测试运行环境

testNG才能实现注入ioc里面的bean。