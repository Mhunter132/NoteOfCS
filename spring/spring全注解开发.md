@configuration

指定这个类是配置类，并取代xml配置文件。

@bean

指定咖啡豆组件

@componentScan(value="com.sxt",

​								includeFilters={

​										@Filter(type=FilterType.ANNOTION, 

​										classes={Controller.class} ,

​										useDefaultFilters=false )  } )

@ComponentScans(

value={

​				@componentScan(value="com.sxt",

​								includeFilters={

​										@Filter(type=FilterType.ANNOTION, 

​										classes={Controller.class} ,

​										useDefaultFilters=false )  } )}

)

