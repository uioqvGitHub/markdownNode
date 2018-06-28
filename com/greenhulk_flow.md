[TOC]



### 页面对照

####AbortAuctionManageAction     			流拍列表


####AuctionAudit 						发拍审核

####BondDetail shopCode=000055			资金详情-拍卖交易详细

####CarManageAction					拍卖市场车辆管理

####ConfigureManage					场次创建

####DisputeAction						争议列表

####DistributorList						经销商列表-车辆

####DistributorDetail shopCode=000055	资金详情-拍卖出价详细

####FundsAction							经销商列表-保证金

####InfoManage							消息管理

####MarketManager						市场管理

####	MarketInformation id=1				市场详情

####OrderAction							订单列表

####RoomManageAction					场次管理

####	RoomDetails.html roomUUID=417034303dc8425584ed56aa6d4b6ccd		场次详情

####RoomManageOfSelfEmployed			自营场次管理

####	RoomInfoAction.html roomUUID=74d19c723ebb4705acb4ede4101c489d	自营场次详情

####WaitForAuction						发拍审核通过



### 服务对照



#### `AbortAuctionManageService`							流拍管理服务

#####1. `getAbortAuctionManageListData `						获取流拍管理列表数据

```java
Page<AbortAuctionManageListDTO> getAbortAuctionManageListData(AbortAuctionManageQO abortAuctionQO);
```
#####2. `getBidHistory `										获取出价历史

```java
Page<BidDetailDTO> getBidHistory(BidHistoryQO bidHistoryQO);
```

#####3. `getAbortAuctionRecords `							获取流拍记录

```java
List<AbortAuctionRecordListDTO> getAbortAuctionRecords(String carId);
```

#####4. `batchTransactionFailed` 							批量拍卖失败

```java
boolean batchTransactionFailed(List<AbortAuctionManageListDTO> carIdList);
```



#### `AuctionAuditService`									拍卖审核服务

#####1. `queryAuctionAudit`									分页查询审核发拍数据

```java
Page<AuctionAuditDTO> queryAuctionAudit(CarAuctionAuditQO carAuctionAuditQO);
```

#####2. `updateStatusOfCar`  								更改审核状态

```java
boolean updateStatusOfCar(String carId,Integer examineStatus);
```

#####3. `exportDealerPage` 									导出自营发拍审核报表的URL

```java
String exportDealerPage(CarAuctionAuditQO carAuctionAuditQO,String sortParam,String sortOrder);
```

#####4. `queryAuctionAuditByCondition`						根据条件查询审核发拍数据

```java
Page<AuctionAuditDTO> queryAuctionAuditByCondition(CarAuctionAuditQO carAuctionAuditQO,String sortParam,String sortOrder);
```

#####5. `examinePassAuctionAudit`							批量审核通过

```java
boolean examinePassAuctionAudit(List<String> ids);
```



#### `AuctionOrderService`									拍卖订单服务

主要为`rpc`调用

#####1. `queryOrderPage`									分页查询审核发拍数据

```java
Page<OrderManageDTO> queryOrderPage(AuctionOrderQO auctionOrderQO);
```

#####2. `exportPageAsExcel`  								更改审核状态

```java
String exportPageAsExcel(AuctionOrderQO auctionOrderQO);
```

#####3. `getOrderCount` 										导出自营发拍审核报表的URL

```java
OrderCountDTO getOrderCount();
```

#####4. `getBidHistory`										根据条件查询审核发拍数据

```java
//     * 查询出价明细 shopCode选填 不填则查询该车辆所有出价记录
Page<BidDetailDTO> getBidHistory(String roomId, String carId, int pageIndex, int pageSize, String shopCode);
```



#### `BondService`											资金服务

#####1. `queryBalanceByShopCode`							分页查询资金管理信息

```java
FundManagementDTO queryBalanceByShopCode(String shopCode);
```

#####2. `queryFundsDetailPage`  								分页查询资金详细

```java
Page<FundsDetailDTO> queryFundsDetailPage(FundsDetailQO fundsDetailQO);
```

#####3. `exportDealerPage` 									导出拍卖交易明细列表的URL

```java
String exportDealerPage(FundsDetailQO fundsDetailQO);
```



#### `CarFilterServiceSPI`									过滤汽车信息服务

对汽车来源进行过滤

```java
private static final String SOURCE = "source";
private static final String ROOMUUID="auction_room_id";
private static final String[] MARKET={"auction-ycp","souche-dsc"};
```

#####1. `searchCarSourceData`								统计按市场分组车源数量

```java
public Map<String,Integer> searchCarSourceData() {
```

#####2. `searchRoomCarData`  								根据场次u u i d查询场次的汽车管理信息列表

```java
public List<CarManageListDTO> searchRoomCarData(String uuid) {
```



#### `CarManageService` 									汽车管理服务

#####1. `queryCarByManageQO`								分页查询汽车管理信息

```java
Page<CarManageListDTO> queryCarByManageQO(CarManageQO carQO);
```

#####2. `queryAllCarLocation`  								汽车所在地分组查询汽车数量

```java
List<ValueNameDTO> queryAllCarLocation();
```
#####3. `queryAllCarLevel`									汽车等级分组查询汽车数量

```java
List<ValueNameDTO> queryAllCarLevel();
```

#####4. `queryAllCarRegisterYear`  							汽车的上牌年份分组查询汽车数量

```java
List<ValueNameDTO> queryAllCarRegisterYear();
```
#####5. `queryAllCarMarket`									市场分组查询汽车数量

```java
List<ValueNameDTO> queryAllCarMarket();
```

#####6. `queryAllCarAuctionStatus`  						拍卖状态分组查询汽车数量

```java
List<ValueNameDTO> queryAllCarAuctionStatus();
```
#####7. `queryAllCarBrand`									汽车品牌分组查询汽车数量

```java
List<ValueNameDTO> queryAllCarBrand();
```

#####8. `convertDTOList`  									将List<CarBaseDTO>转化成List<CarManageListDTO>

```java
/**
* 将List<CarBaseDTO>转化成List<CarManageListDTO>
**/
List<CarManageListDTO> convertDTOList (List<CarBaseDTO> baseDTOList);

```
#####9. `carOffShelves`										车辆下架

```java
DubboResult<String> carOffShelves(CarOffShelvesDTO carOffShelvesDTO);  
```

#####10. `pushCarInfo`  										车辆推送

```java
Result<String> pushCarInfo(CarPushQO carQO);
```
#####11. `countTargetNum`									计算车辆推送的人数

```java
int countTargetNum(CarPushQO carQO);
```

#####12. `getBidHistory`  									获取出价记录

```java
Page<BidDetailDTO> getBidHistory(String roomId, String carId, int pageIndex, int pageSize);
```



#### `ConfigureService`										配置服务

#####1. `getConfigure`										获取配置类型 以及配置选项

   ```java
List<ConfigureManageDTO> getConfigure();
   ```

#####2. `getConfigureByConfigureCode`  						查询配置信息（配置选项 配置修改记录）

   ```java
 ConfigureManageDTO getConfigureByConfigureCode(int configureCode);
   ```

#####3. `deleteConfigureOption`								删除配置选项

   ```java
/**
 * @param optionId 配置选项的id
 * @return  1:删除成功，0删除失败"
 */
int deleteConfigureOption(int optionId);
   ```

#####4. `saveConfigureOption`  								添加配置选项

   ```java
/**
 * @param  options 添加的配置选项
 * @param  configureCode 配置类型的Code值
 * @return
 */
int saveConfigureOption(String options, int configureCode);
   ```

#####5. `checkOption`										校验配置选项是否存在

   ```java
/**
 * @param options 添加的配置选项
 * @param configureCode 配置类型的code值
 * @return 1:存在不可以添加，0:不存在可以添加"
 */
int checkOption(String options, int configureCode);
   ```

#####6. `getOptions`  										根据配置选项code值和所属配置类型的code值，获得配置选项信息

   ```java
/**
 * @param optionsCode  配置选项code
 * @param configureCode 配置类型的code值
 */
ConfigureOptionsDTO getOptions(int optionsCode, int configureCode);

   ```

#####7. `getOptionsList`										根据配置类型的code值，获得配置选项信息

   ```java
/**
 * @param configureCode 配置类型的code值
 * @return
 */
List<ConfigureOptionsDTO> getOptionsList( int configureCode);
   ```



####`CreateRoomService`									场次创建服务

##### 1. `findCarList`										查询车辆信息

```java
Page<RoomCarListDTO> findCarList(CarRoomQO carRoomQO);
```

##### 2. `findCarListByCarIdLis`  							根据车辆ids查询车辆信息

```java
List<RoomCarListDTO> findCarListByCarIdLis(String carIds);
```

##### 3. `craeteRoom`											创建场次

```java
void craeteRoom(RoomDetailDTO roomDetailDTO, List<RoomCarUpdateDTO> roomCarUpdateDTOList);
```

##### 4. `findCarListByRoomUUID`  							查询场次车辆

```java
List<RoomCarListDTO> findCarListByRoomUUID(String carRoomUUID);
```

##### 5. `findRoomByRoomUUID`								 查询场次信息

```java
RoomDetailDTO findRoomByRoomUUID(String carRoomUUID);
```

####`DisputeService`										争议服务

####`DistributorService`									经销商服务

####`ExcelFileService`										excel文件服务

####`FundsService`											资金服务	

####`InfoManageService`									消息管理服务						

####`MarketService`										市场服务

#### `OrderSimpleService`									简单订单服务

直接通过`dao`访问数据库

####`RoomCarService`										场次车辆服务

####`RoomDetailsService`   									场次详情服务

####`RoomManageService`									场次管理服务 

####`WaitForAuctionService`								等待拍卖服务			