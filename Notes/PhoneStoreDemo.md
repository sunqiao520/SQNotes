# PhoneStoreDemo

### 后端结构

```text
com.sq.phone_store_demo
----config
        CorsConfig   //解决跨域问题
----controller
        AddressHandler
        OrderHandler
        PhoneHandler
----dto
        OrderDTO
----entity
        BuyerAddress
        OrderMaster
        PhoneCategory
        PhoneInfo
        PhoneSpecs
----enums
		PayStatusEnum
		ResultEnum
----exception
		PhoneException
----form
		AddressForm
		OrderForm
----repository
		BuyerAddressRepository
		OrderMasterRepository
		PhoneCategoryRepository
		PhoneInfoRepository
		PhoneSpecsRepository
----service
	----impl
	        AddressServiceImpl
	        OrderServiceImpl
	        PhoneServiceImpl
        AddressService
        OrderService
        PhoneService
----util
        KeyUtil
        PhoneUtil
        ResultVOUtil
----vo
        AddressVO
        DataVO
        OrderDetailVO
        PhoneCategoryVO
        PhoneInfoVO
        PhoneSpecsCasVO
        PhoneSpecsVO
        ResultVO
        SkuVO
        SpecsPackageVO
        TreeVO
    PhoneStoreDemoApplication
```

### 后端工程创建

SpringBoot模板

![](https://gitee.com/sun-qiao321/picture/raw/master/images/20210515162318.png)

组件选择

Lombok

Spring Web

Spring Data JPA

MySQL Driver

![](https://gitee.com/sun-qiao321/picture/raw/master/images/20210515162533.png)

