[TOC]



### 类页面对照

####AbortAuctionManageAction     			流拍列表

##### --`AbortAuctionManageService.getAbortAuctionManageListData`		获取流拍列表	

1. 通过`OrderDAO`的`queryByQO`查询出`OrderVO` page
   1. `QueryByQO` 写死查`source='auction-dsc'`
   2. `QueryByQO` 写死查`OrderStatusEnum.ABORTED `     `ABORTED(1400, "流拍")`
   3. page.items为空   返回  page
   4. page.items不为空   -> 2  
2. 遍历 OrderVO page 的items从OrderVO中取出carId封装到list中
3. 根据`source='auction-dsc'`与 carId   in    carIdList查找CarBaseDTO list
4. 根据carIdList  查询   DetectionInfoDO  list   检测信息
   1. 如果CarBaseDTOList不为空遍历OrderVOPage的items与CarBaseDTOList
   2. 比较他们的CarId相等
   3. 将orderVO与CarBaseDTO转换为AbortAuctionManageListDTO
   4. 根据这个OrderVO的carId获取DetectionInfoDO  检测信息
   5. 检测信息不为空
      1. 设置AbortAuctionManageListDTO的Level根据DetectionInfoDO检测信息的grade
      2. 设置AbortAuctionManageListDTO的url根据 url前缀与检测id
   6. AbortAuctionManageListDTO添加到AbortAuctionManageListDTOList
   7. 设置Page的一些属性与AbortAuctionManageListDTOList并返回

#####--AbortAuctionManageService.getAbortAuctionRecords				获取汽车流拍记录

1. 根据carId  获取OrderVOList 通过OrderDAO

#####--AbortAuctionManageService.getBidHistory							获取出价记录

#####--AbortAuctionManageService.batchTransactionFailed					批量失败



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