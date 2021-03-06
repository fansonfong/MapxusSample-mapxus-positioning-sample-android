[API reference](https://mapxussample.github.io/MapxusSample-mapxus-positioning-sample-android/)

# Mapxus Positioning Android SDK使用说明

## 1. 创建工程

### 1.1 创建Android项目

首先创建一个新的 Android 项目（[Android创建项目指南](https://developer.android.com/studio/projects/create-project)），然后按照以下步骤手动集成。

### 1.2 配置项目

#### 1.2.1 配置项目仓库地址

在项目根目录下的build.gradle中添加jcenter()

~~~groovy
allprojects {
	repositories {
		jcenter()
	}
}
~~~

#### 1.2.2 添加Positioning SDK依赖
在需要使用到Positioning SDK的module的`build.gradle`文件中添加依赖：

~~~groovy
dependencies {
	implementation 'com.mapxus.positioning:positioning:0.4.0'
}
~~~

***注意***：Mapxus Positioning SDK 使用限制minSdkVersion 为19，所以请运行在Android4.4或以上的系统

#### 1.2.3 添加授权的 app id 和 secret

传入 app id 和 secret 有以下两种方式，可以按照自己的需要选择其中一种：

(1). 在 **AndroidManifest.xml** 文件的 *application* 节点中添加 *meta-data*

```xml
<application ...
	<meta-data
	      android:name="com.mapxus.api.v1.appid"
	      android:value="your appid" />
	<meta-data
	      android:name="com.mapxus.api.v1.secret"
	      android:value="your secret" />
</application>
```

(2). 在获取定位服务客户端MapxusPositioningClient实例时传入 app id 和 secret

~~~java
MapxusPositioningClient mMapxusPositioningClient = 
MapxusPositioningClient.getInstance(this, "your appid", "your secret");
~~~


#### 1.2.4 在项目清单文件AndroidMainfest.xml里添加权限
~~~xml
<!--获取运营商信息，用于支持提供运营商信息相关的接口-->
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<!--用于进行网络定位-->
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<!--用于访问GPS定位-->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<!--用于访问wifi网络信息，wifi信息会用于进行网络定位-->
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<!--这个权限用于获取wifi的获取权限，wifi信息会用来进行网络定位 -->
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
<!--访问网络，定位需要上网-->
<uses-permission android:name="android.permission.INTERNET" />
~~~

**注意：** **Mapxus Positioning SDK 0.3.3**版本之后权限`READ_PHONE_STATE`，**Mapxus Positioning SDK 0.3.4**版本后`WRITE_EXTERNAL_STORAGE`不再必须授予，可以删除。

#### 1.2.5 Android 6.0及以上权限请求

运行在Android6.0及以上设备如果项目配置targetSdkVersion小于23则会默认授予了所有申请的权限不需要检查和请求权限， 如果 targetSdkVersion >= 23 则需要。[Android在运行时请求权限指南](https://developer.android.com/training/permissions/requesting)

**Mapxus Positioning SDK** 需要检测获取的权限有：

*  `Manifest.permission.ACCESS_FINE_LOCATION`
*  `Manifest.permission.ACCESS_COARSE_LOCATION`

**注意**：关于如何动态请求权限详细可参考 Positioning Sample APP。 **Mapxus Positioning SDK 0.3.3**版本后不再需要检测`Manifest.permission.READ_PHONE_STATE`，**Mapxus Positioning SDK 0.3.4**版本后不再需要检测`Manifest.permission.WRITE_EXTERNAL_STORAGE`

### 1.3 设备状态的开启

1. 请在整个定位过程中确保**网络可用**，定位过程需要不断进行网络请求获取最新位置信息

2. 请**开启WIFI**，定位过程需要不断获取WIFI信息进行定位

3. Android6.0及以上设备出现在原生系统和部分第三方、厂商定制系统因没有开启GPS导致无法获取WIFI信息而不能进行定位的问题，所以Mapxus Positioning SDK在Android6.0及以上设备运行时**必须开启定位服务**以防止无法使用。

4. 使用**Mapxus Positioning SDK 0.3.5以前前版本**设备必须要有**气压传感器**，否则无法使用。**Mapxus Positioning SDK 0.3.5** 版本之后没有气压传感器的设备也可以使用定位服务可能会出现定位楼层跳动。**Mapxus Positioning SDK 0.3.6** 版本之后没有气压传感器的设备可以使用定位服务，但只能在单楼层定位不能自动切换楼层，如果需要调用changeFloor来进行楼层切换。

5. **Mapxus Positioning SDK 0.3.0** 版本之后加入室内外切换定位，目前该功能只能在支持原始GNSS测量的手机上使用。（大部分生产于2016年及以后以及系统为Android 7.0及以上）[点击查看支持的设备列表](https://developer.android.com/guide/topics/sensors/gnss#supported-devices)。 对于不支持室内外切换的设备，会默认是在室内中进行定位。或者可以使用提供的工具类 `Utils.isSupportGnss(Context context)`判断。

6. **Mapxus Positioning SDK 0.3.7**及以后版本**定位服务**须开启高精度模式，否则会返回`ERROR_LOCATION_SERVICE_DISABLED`错误。可通过以下代码跳转到设置定位服务精度的界面中设置其精度：

~~~java
Intent locationIntent = new Intent(Settings.ACTION_LOCATION_SOURCE_SETTINGS);
startActivity(locationIntent);
~~~

### 1.4 注意事项

1. **Mapxus Positioning SDK 0.3.6**版本之后更换了新版授权包，请尽快联系获取新的id与secret。

2. 使用**Mapxus Positioning SDK 0.3.6**如果需要同时使用到MapxusMap，MapxusMap SDK版本须同步升级为**2.3.3-beta**

3. 使用**Mapxus Positioning SDk 0.3.7**及以上版本如果需要同时使用到MapxusMap, MapxusMap SDK版本须升级为**2.4.1**及以上版本

| Mapxus Positioning SDK  |  对应的可使用MapxusMap SDK版本  |
| ----------------------- |  --------------------------- |
|    0.3.0 ~ 0.3.5        |  2.0.0-beta ~ 2.2.1-beta     |
|    0.3.6				   |  2.3.3-beta                  |
|    0.3.7及后续版本			|  2.4.1及后续版本              |
|    0.4.0及后续版本       |   3.0.0及后续版本              |

## 2.获取室内定位

### 2.1 获取实例

以下为获取MapxusPositioningClient的示例代码（在主线程中调用）：

~~~java
//声明MapxusPositioningClient对象
public MapxusPositioningClient mMapxusPositioningClient;

//获取MapxusPositioningClient定位服务客户端的实例
mMapxusPositioningClient = MapxusPositioningClient.getInstance(getApplicationContext());

//设置定位信息回调监听
mMapxusPositioningClient.setPositioningListener(listener);
~~~

### 2.2 配置定位参数

~~~java
//不设置此option则定位时会使用默认值
MapxusPositioningOption option = new MapxusPositioningOption();

//配置定位模式，NORMAL BACKGROUND GAME可选, 默认NORMAL（BACKFROUND、GAME模式暂时不可用）
positionerOption.setMode(LocationMode.NORMAL);

//设置定位参数，不配置则会使用默认参数开始定位
mIndoorPositionerClient.setPositionerOption(option);
~~~

### 2.3 获取定位

实现MapxusPositioningListener接口，获取定位结果

~~~java
//实现MapxusPositioningListener监听接口
private MapxusPositioningListener listener = new MapxusPositioningListener() {
	@Override
	public void onStateChange(PositioningState state) {
		//定位过程中状态变化回调
		//WAITING >>>> 调用start()到成功获取定位位置前的等待状态
		//RUNNING >>>> 定位成功后的运行状态
		//PAUSED  >>>> 调用pause()后成功暂停返回暂停状态
		//STOPPED >>>> 调用stop()后成功停止返回停止状态
	}

	@Override
	public void onError(ErrorInfo errorInfo) {
		//错误信息回调，各类错误信息解释请查看API文档
		//开始定位后出现的错误信息的返回 errorCode == WARNING 时定位引擎会抛出此错误信息后仍继续运行
		//其余错误返回后会自动销毁定位引擎，再使用须重新实例化IndoorLocatorClient 定位成功前初始化可能出现多个错误返回
	}

	@Override
	public void onOrientationChange(float orientation, @SensorAccuracy int accuracy) {
		//当前角度信息回调，取值范围在[0, 360]间，0代表正北方，90代表正东，180代表正南，270代表正西
		//accuracy为当前角度的精度等级，建议在非高精度模式下使用8字校准法校准角度
	}

	@Override
	public void onLocationChange(PositioningLocation location) {
		//定位位置信息回调，包括建筑ID、楼层信息、经纬度、定位精确度
	}

};

~~~
### 2.4 开始定位

~~~java
//调用start()之后只需要等待定位结果自动返回即可, 定位成功后会不断地请求定位更新位置
//只在第一次调用有效，其他状态下调用无效。重新start()请stop()后重新获取MapxusPositioningClient实例执行
//开始成功返回PositioningState.RUNNING
mMapxusPositioningClient.start(); //开始定位
~~~

### 2.5 暂停定位
~~~java
//暂停运行状态（其他状态下调用无效）的定位引擎，不再请求定位但不销毁定位服务
//暂停成功返回PositioningState.PAUSED
mMapxusPositioningClient.pause();
~~~
### 2.6 恢复定位
~~~java
//恢复暂停状态（其他状态下调用无效）的定位引擎，继续不断地请求定位更新位置
//恢复成功返回PositioningState.RUNNING
mMapxusPositioningClient.resume();
~~~
### 2.7 切换定位模式
~~~java
//在定位成功后的运行状态（其他状态下调用无效）切换定位模式（此方法暂时不可用）
mMapxusPositioningClient.changeMode(newMode);
~~~
### 2.8 切换定位楼层
~~~java
//在定位成功后的运行状态（其他状态下调用无效）切换定位定位楼层
mMapxusPositioningClient.changeFloor(floor);
~~~
### 2.9 停止定位
~~~java
//在开始后的任意状态调用均有效，可直接停止等待、暂停、运行状态下的定位服务，停止成功返回PositionerState.STOPED
//停止定位，销毁定位引擎，再使用需要重新获取MapxusPositioningClient实例
 mMapxusPositioningClient.stop();
~~~
### 2.10 开启后台定位服务
~~~java
//开启后台定位服务。
//Android6.0 以上为了节能优化，app退到后台一段时间没有使用系统会判断它处于空闲状态
//停用网络访问以及暂停同步和作业导致无法正常使用定位服务。开启后能防止出现这种情况
mMapxusPositioningClient.enableBackgroundPositioning(1, notification);
~~~
### 2.11 关闭后台定位服务
~~~java
//app返回前台后可关闭后台定位服务
mMapxusPositioningClient.disableBackgroundPositioning(true);
~~~

### 2.12 定位过程中切换定位楼层

~~~java
//定位过程中切换定位楼层
mMapxusPositioningClient.changeFloor(positioningFloor);
~~~


