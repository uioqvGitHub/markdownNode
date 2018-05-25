### dubbo

```
web-->config-->user-beans-->application-dubbo.xml 
dubbo的基本配置

web-->config-->user-beans-->application-dubbo-export.xml 
当前项目对外提供的接口

web-->config-->user-beans-->application-dubbo-import.xml 
当前项目需要导入的接口
```

###Service

web-->config-->user-beans-->application-service.xml 

dao    扫描 com.souche.paimai.shield.manager

service     扫描 com.souche.paimai.shield.service

### model

com.souche.paimai.shield.model包下