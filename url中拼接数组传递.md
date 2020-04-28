---
title:  url中拼接数组传递
date: 2019-11-22
categories: 
- work
tags: 
- 笔记
---

 今天工作要完成一次删除收藏夹多个商品的需求。那么url中要怎么拼接呢？（暂不考虑字数限制）

```java
  public JsonResult deleteFavorite(@RequestParam (name = "rcdId") String ...rcdId){
        JsonResult jsonResult = new JsonResult();
        Integer entId = enviromentManager.getEnviromentClient().getEntId();
        String orgId = enviromentManager.getEnviromentClient().getOrgId();
        int count=0;
        for(int i = 0; i< rcdId.length ;i++) {
            Mbc2ProductFavorited mbc2ProductFavorited = new Mbc2ProductFavorited();
            if (!StringUtils.isEmpty(rcdId)) {
                try {
                    mbc2ProductFavorited.setMbc2RcdId(rcdId[i]);
                    mbc2ProductFavorited.setMbc2EntId(entId);
                    mbc2ProductFavorited.setMbc2OrgId(orgId);
                    mbc2ProductFavorited.setMbc2AcctId(enviromentManager.getEnviromentClient().getMemberId());
                    count = mbc2ProductFavoritedService.doDeleteModelWithKey(RouteInfor.buildRouteInfoInt(entId), mbc2ProductFavorited, null);

                } catch (Exception e) {
                    e.printStackTrace();
                    jsonResult.buildErrorResult(e);
                }
            }
        }
        jsonResult.buildSuccessResult(count+"");
        return  jsonResult;
    }
}
```

我起初想着用可边长参数传递，因为可变长参数的本质就是数组，所以我想着用 可边长数组或者数组接收

```
 1. http://localhost:8080/deleteFavorite?rcdId=1,2,3
          @RequestParam (name = "rcdId") String []rcdId
 2. http://localhost:8080/deleteFavorite?rcdId=1,2,3
          @RequestParam (name = "rcdId") String ...rcdId
  
```

