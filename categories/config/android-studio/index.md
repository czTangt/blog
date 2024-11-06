# Android Studio配置


关于 Android Studio 的使用和配置。

&lt;!--more--&gt; 

## Gradle下载配置

直接更换国内腾讯 [镜像源](https://mirrors.cloud.tencent.com/gradle/)，打开 `gradle - wrapper -gradle-weapper.properties` 进行更改。然后点击 `Sync Now` 进行同步。参考-&gt; [Android导入项目时Gradle下载速度慢_导入gradle项目特别慢](https://blog.csdn.net/Systemmax20/article/details/131343623)

```xml
#Sun Feb 25 20:22:32 GMT&#43;08:00 2024
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
distributionUrl=https\://mirrors.cloud.tencent.com/gradle/gradle-8.2-bin.zip # 这里就对应替换为腾讯的镜像地址
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
```

但是这样也是很慢，还是得等，有时候还会突然跑到源地址去下载，搞不明白。(后续补充：有时候改了国内源，然后停止加载zip文件，之后重试一下就快很多了)

## 新版 AS 添加依赖

这里是在 `settings.gradle.kts` 文件中添加 maven 仓库，然后在 app 的 `build.gradle.kts` 文件中添加依赖。查看这里[Android Studio | 2022.3.1版本解决创建项目下载gradle缓慢问题](http://www.yizu.org/archives/846/) 

``` 
// settings.gradle.kts 文件中
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven { url = uri(&#34;https://jitpack.io&#34;) }// as改版后的新添加方式
    }
}

// build.gradle.kts 文件中
dependencies {
    implementation(&#34;com.github.Hitomis:CircleMenu:v1.1.0&#34;) // as改版后的新添加方式
    }
```

## SD卡读写权限

对于低版本 sdk，只需要以下权限即可(在AndroidManifest.xml中修改)，在manifest标签中添加

```xml
&lt;uses-permission android:name=&#34;android.permission.READ_EXTERNAL_STORAGE&#34;&gt;&lt;/uses-permission&gt;
&lt;uses-permission android:name=&#34;android.permission.WRITE_EXTERNAL_STORAGE&#34;&gt;&lt;/uses-permission&gt;
```

而对于高版本的 sdk，这里是34，则需要添加东西。新加入的在application标签中添加

```xml
&lt;uses-permission android:name=&#34;android.permission.READ_EXTERNAL_STORAGE&#34;&gt;&lt;/uses-permission&gt;
&lt;uses-permission android:name=&#34;android.permission.WRITE_EXTERNAL_STORAGE&#34;&gt;&lt;/uses-permission&gt;
&lt;uses-permission android:name=&#34;android.permission.MANAGE_EXTERNAL_STORAGE&#34;&gt;&lt;/uses-permission&gt;
android:requestLegacyExternalStorage=&#34;true&#34;
```

## dataBinding使用

刚开始创建的 `Launch Activity` 不用理会，但是对于新增加的`Activity`，想要使用 `dataBinding` 功能，就需要先在 `build.gradle.kts`文件中添加下面代码：

```kotlin
android {
  ......
	buildFeatures {
      dataBinding = true // 确保这里启用了数据绑定
  }
}
```

然后在相关 xml 文件中，对准 `androidx.constraintlayout.widget.ConstraintLayout`按下`alt &#43; enter` 转化为 databinding 的模式。这样后续的 activity 才可以使用并编译apk成功。

## 图片导入

资源主要就是存放在 res 目录下，如下表所示各个目录的作用。

| 目录 | 作用 |
| --- | --- |
| drawable | 存放所有的图片及图标配置（xml文件展示） |
| layout | 布局，创建一个Activity一般会同步创建一个布局。 |
| mipmap | 存放各种分辨率的图标，平常用的就是 xxhdpi |
| values | 一些固定的配置，例如值，主题等 |

这里 drawable 存放的东西大多就是 图标的配置，在 As 中可以利用 `File → New → Android Resource File` 来生成一个配置文件，例如背景之类的可以重复使用。

同时使用`File → New → Image Asset` 可以创建 app 应用的图标，提供 svg 图片即可自动创建。使用`File → New → Vector Asset` 则是创建图片，将一个 svg 格式的图片转化为 xml 文件形式，然后供 `ImageView` 等控件使用。

## So文件生成

### 添加新文件

- 对于头文件 .h

首先在 cpp 目录下创建相应文件，然后在 `CmakeLists.txt` 文件中添加下面的代码

``` cmake
include_directories(
    basic.h
)
```

- 对于文件 .cpp
在 cpp 目录下创建，然后在 `CmakeLists.txt` 文件中的 `add_libraty` 添加新创建的 .cpp文件。这样多个 .cpp 文件就会编译为一个 so 库。

``` cmake
add_library(${CMAKE_PROJECT_NAME} SHARED
        # List C/C&#43;&#43; source files with relative paths to this CMakeLists.txt.
        native-lib.cpp
        checkfrida.cpp)
```

![添加文件](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/img/20241105172840.png &#34;20241105172840&#34;)

### 编译多个so文件

这里就是再添加一个 add_library 文件，这样就可以编译为多个了。

``` cmake
add_library(
        # so 文件的名字
        checkroot
        # 共享库
        SHARED
        # List C/C&#43;&#43; source files with relative paths to this CMakeLists.txt.
        checkroot.cpp)
```

![编译多个文件](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/img/20241105173015.png &#34;20241105173015&#34;)

### 引入第三方库

- 使用第三方库的函数

这里就是利用 `target_link_libraries` 进行引用，注意对于每一个so文件，都需要进行引用操作。这里上面的是默认的，要是能在 `checkroot.cpp` 文件中使用 log，那么就需要自己手动进行引用了。

``` cmake
target_link_libraries(
        checkroot
        # List libraries link to the target library
        android
        log
        )
```

![引入库](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/img/20241105173045.png &#34;20241105173045&#34;)

### 使用不同架构

As 进行了改版，所以之前的方式不能使用了。这里是在 app 的 `build.gradle.kts` 文件中添加。下面展示了两种方式。

```
android {
		...
    defaultConfig {
				...
        ndk {
		        // 第一种方式
            abiFilters.addAll(arrayOf(&#34;arm64-v8a&#34;, &#34;x86_64&#34;))
            
            // 第二种方式
            //abiFilters.add(&#34;arm64-v8a&#34;)
            //abiFilters.add(&#34;x86_64&#34;)
            //abiFilters.add(&#34;armeabi-v7a&#34;)
            //abiFilters.add(&#34;x86&#34;)
        }
    }
}
```

## 相关操作

### 存储空间管理

移动 `.gradle` 到指定位置
- [将 .gradle文件 从C盘移动到D盘](https://www.cnblogs.com/tc310/p/16884368.html)，这里同时还需要相应修改 `idea.plugins.path` 和 `idea.log.path`

移动 `.android` 到指定位置
- [解决方案](https://blog.csdn.net/weixin_42768634/article/details/115142632)

### 生成apk

- `build -&gt; Generate Signed Bundle or APK`：选择APK，然后创建key（注意需要路径完整），之后再选择 release 即可
- 生成的 `app-debug.apk` 在路径 `/app/build/outputs/apk/debug/` 下面，或者在 `/app/build/intermediates/apk/debug/` 目录下。这里不知道是什么的变化引起的生成存放目录的变化。
- `build → Generate Signed Bundle or APK` 可以生成签名后的 apk（release版本），它存放在和 `debug`类似的目录下。这里我的 vivo 手机不能使用 debug 版本，只能下载 release 版本。

## 报错汇总

遇到问题 `Cannot use connection to Gradle distribution &#39;https://mirrors.cloud.tencent.com/gradle/gradle-8.2-bin.zip&#39; as it has been stopped.`
- 这里直接关闭项目，再重新打开即可。

`Error running &#39;app&#39;: Default Activity not found`
- 这里修改 configuration，更改 Launch Options -&gt; `Launch:nothing`
- [android studio怎么运行没有activity的service、broadcastReceiver、cotentProvicer等](https://blog.csdn.net/qq_36792930/article/details/91041904)

日志不能在 As 中显示
- 检查算法助手有没有 hook 对应程序，如果存在，那么它好像开机自启，自动将日志捕获了。关闭应用 hook 就可以显示日志了

---

> 作者: [czTang](https://github.com/czTangt)  
> URL: http://localhost:1313/blog/categories/config/android-studio/  

