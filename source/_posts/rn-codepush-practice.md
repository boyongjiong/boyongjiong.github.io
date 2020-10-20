---
title: React Native App Code-Push实践
date: 2018-01-20 10:11:50
tags:
categories: 实践向
---

## 1. Install the CodePush CLI
```
npm install -g code-push-cli
```
Note: On Mac OS and Linux, you **may** need to prefix this command with **sudo**

## 2. Create a CodePush account
Before you can release any updates, you first need to create a CodePush account. To do this, simply type the following command via the CLI and authenticate with either your GitHub or Microsoft account:
```
code-push register
```

![B1936EFD-EC60-473E-8857-7642F6EEC3FC](https://tva1.sinaimg.cn/large/a9034e0egy1gjvmn2cfhaj21yc086tal.jpg)
eg:
![55B62D1C-B1E3-4936-8873-63444B8FDAA2](https://tva2.sinaimg.cn/large/a9034e0egy1gjvmn287g1j20d105gwex.jpg)
---
相关可用的命令

 * `npm install -g code-push-cli`  安装
 * `code-push register`  注册
 * `code-push login`  登录
 * `code-push logout`  注销	
 * `code-push access-key ls`  列出登录的 token
 * `code-push access-key rm <accessKey>`  删除某个 token

---
## 3. Register your app with the service
In order to let the service know about your app, simply register it using a recognizable name. Create an app for each OS you target:
```
code-push app add MyApp-IOS ios react-native

code-push app add MyApp-Android  android react-native
```

![2E22E3CE-3B0F-48DD-9043-EC706E2A04E1](https://tvax1.sinaimg.cn/large/a9034e0egy1gjvmnyj6hdj21c60jegps.jpg)

**JiqirenApp-IOS**:
 Production: `NXMw9_pCK6l40N12ipIy2ZZ3qf6_dc56ecd6-49b8-414b-bdd1-dc122f99eba6_`
 Staging: `y5XDr08m8PXr7e5J0rc5AXoIBfT-dc56ecd6-49b8-414b-bdd1-dc122f99eba6`

**JiqirenApp-Android**:
 Production: `n6XjjNKJ0mSXrUAQ4tAVeMNz9BUcdc56ecd6-49b8-414b-bdd1-dc122f99eba6`
 Staging: `kqIcG8s8bwTNjrdf6ajbXOefcU2Vdc56ecd6-49b8-414b-bdd1-dc122f99eba6`

---
相关可用的命令
 * `code-push app add <AppName>` 添加项目
 * `code-push app remove <AppName>` 删除项目
 * `code-push app list` 列出账号下所有项目
 * `code-push collaborator add <AppName> next@126.com`

---
## 4. CodePush-ify your app
Add the appropriate CodePush client SDKs to your app, and configure them to query for updates against the app deployment created above. The following provide details on how to do this for each unique app type:

[Cordova](https://microsoft.github.io/code-push/docs/cordova.html#link-2)
[React-Native(ios)](https://microsoft.github.io/code-push/docs/react-native.html#link-3)

### Getting Started
```
yarn add react-native-code-push
```

![3C193AD9-FD38-4D8A-9DF1-64E2DAED1BAA](https://tvax3.sinaimg.cn/large/a9034e0egy1gjvmp3mgqxj21z40dutcz.jpg)
**NOTE**: This guide assumes you have used the `react-native init`command to initialize your React Native project. As of March 2017, the command `create-react-native-app` can also be used to initialize a React Native project. If using this command, please run `npm run eject` in your project’s home directory to get a project very similar to what `react-native init` would have created.

### IOS Setup
```
react-native link
```

![5F9A9550-2C86-49DA-9D1C-26FB2C71701D](https://tva3.sinaimg.cn/large/a9034e0egy1gjvmrgpx8sj21t20eejwo.jpg)
**before:**
![3C2E4875-99BF-44D5-A254-80D272A05626](https://tvax1.sinaimg.cn/large/a9034e0egy1gjvmrgdxbij219u0vytey.jpg)
**After:**
![D5D705CC-AFD2-4618-925A-48868F1A54EF](https://tvax4.sinaimg.cn/large/a9034e0egy1gjvmrgyrqej219s0vw7av.jpg)

**Note:** - boyongjiong  2018/1/20
> 千万要注意react-native link 之后，最好是去对照着插件提供的 PLUGIN INSTALLATION (IOS - MANUAL) 对照一遍，不知道在什么情况下就会出现错误， 上面这块儿是 Plugin Configuration（ios）的过程，理论上`react-native link`之后这些操作都不需要再管了，但是我们项目里面 link 就是出错，他在修改代码的时候处理好像有点问题，直接找相关代码，不确定是否已经被注释

#### Plugin Configuration(ios)

**Note:** If you used RNPM or react-native link to automatically link the plugin, these steps have already been done for you so you may skip this section.

Once your Xcode project has been setup to build/link the CodePush plugin, you need to configure your app to consult CodePush for the location of your JS bundle, since it is responsible for synchronizing it with updates that are are released to the CodePush server.
To do this, perform the follow steps:

 1. Open up the `AppDelegate.m` file, and add an import statement for the CodePush headers:
```
  #import <CodePush/CodePush.h>
```
 2. Find the following line of code, which loads your JS Bundle from the app binary for production releases:
```
jsCodeLocation = [[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
```
 3. Replace it with this line:
```
jsCodeLocation = [CodePush bundleURL];
```

This change configures your app to always load the most recent version of your app’s JS bundle. On the first launch, this will correspond to the file that was compiled with the app. However, after an update has been pushed via CodePush, this will return the location of the most recently installed update.

**NOTE:** The `bundleURL` method assumes your app’s JS bundle is named `main.jsbundle`. If you have configured your app to use a different fie name, simply call the `bundleURLForResource:` method(which assumes you’re using the `.jsbundle` extension) or `bundleURLForResource:withExtension:`method instead, in order to overwrite that default behavior.

Typically, you’re only going to want to use CodePush to resolve your JS bundle location within release builds, and therefore, we recommend using the DEBUG pre-processor macro to dynamically switch between using the packager server and CodePush, depending on whether you are debugging or not. This will make it much simpler to ensure you get the right behavior you want in production, while still being able to use the Chrome Dev Tolls, live reload, etc. at debug-time

```
NSURL *jsCodeLocation;

#ifdef DEBUG
    jsCodeLocation = [NSURL URLWithString:@"http://localhost:8081/index.ios.bundle?platform=ios&dev=true"];
#else
    jsCodeLocation = [CodePush bundleURL];
#endif
```

To let the CodePush runtime know which deployment it should query for updates against, open your app’s **Info.plist** file and add a new entry named CodePushDeploymentKey, whose value is the key of the deployment you want to configure this app against(e.g. the key for the Staging deployment for the FooBar app). You can retrieve this value by running 

**`code-push deployment ls <AppName> -k`**

![D01456B2-030F-4A1B-B590-B17FA4FAC5C3](https://tvax4.sinaimg.cn/large/a9034e0egy1gjvmz8klc1j21oq0acwgu.jpg)
in the CodePush CLI(the -k flag is necessary since keys aren’t displayed by default) and copying the value of the `Deployment Key` column which corresponds to the deployment you want to use(see below). Note that using the deployment’s name(e.g. Staging) will not work. That ‘friendly name’ is intended only for authenticated management usage from the CLI, and not for public consumption within your app.

#### 关于 deployment-key 的设置
在我们想 CodePush 注册 App 的时候，CodePush 会给我们两个 deployment-key 分别是在生产环境与测试环境时使用的，我们可以通过如下步骤来设置 deployment-key。
1.用Xcode 打开项目 ➜ Xcode的项目导航视图中的PROJECT下选择你的项目 ➜ 选择Info页签 ➜ 在Configurations节点下单击 + 按钮 ➜ 选择Duplicate "Release ➜ 输入Staging(名称可以自定义)； 
![C4C2FB0C-EFEB-47CF-B62A-13FB27749761](https://tva1.sinaimg.cn/large/a9034e0egy1gjvn0zjvhfj215q0ymwik.jpg)
2.然后选择Build Settings页签 ➜ 单击 + 按钮然后选择添加User-Defined Setting
3.然后输入CODEPUSH_KEY(名称可以自定义)
![4B65DCF6-A5A3-4597-BE32-ECDD6F3F074B](https://tvax2.sinaimg.cn/large/a9034e0egy1gjvn1h2j43j215q0yogse.jpg)
4.打开 Info.plist文件，在CodePushDeploymentKey列的Value中输入$(CODEPUSH_KEY)
![BD133722-D5E6-4BD9-9005-A9F7FCA93E0E](https://tvax1.sinaimg.cn/large/a9034e0egy1gjvn1x2793j215u0ymqbv.jpg)

### Android Setup
In order to integrate CodePush into your Android project, perform the following steps:

#### Plugin Installation(Android)
In order to accommodate as many developer preferences as possible, the CodePush plugin supports Android installation via two mechanisms:
  1. `RNPM - React Native Package Manager (RNPM) ` is an awesome tool that provides the simplest installation experience possible for React Native plugins. If you’re already using it, or you want to use it, then we recommend this approach.
  2. `Manual`If you don’t want to depend on any additional tools or are fine with a few extra installation steps (it’s a one-time thing), then go with this approach.

**Note:** Due to a code change from the React Native repository

![4FFAD87E-B3A4-4DA3-971D-3BA90F7FA1CC](https://tvax4.sinaimg.cn/large/a9034e0egy1gjvn2ug9xrj20nu0g5dkf.jpg)
![1C2031FF-D408-4D64-AC84-81A6A8E88423](https://tvax4.sinaimg.cn/large/a9034e0egy1gjvn2uab5qj20o90jlgp6.jpg)
#### Plugin Configuration(Android)
**Note:**If you used  RNPM or `react-native link` to automatically link the plugin, these steps have been done for you so you may skip this section. （但是还是做一下检查，以及跟着配置一下，因为在 link 的时候，他要求填 deployment key 的时候，我们只输入一个 Staging 的，或者不输入，还得之后再进行具体的配置）

After installing the plugin adn syncing your Android Studio with Gradle, you need to configure your app to consult CodePush for the location of your JS bundle, since it will “take control” of managing the current and all future versions. To do this:

**For React Native >= v0.29**
Update the `MainApplication.java` file to use CodePush via the following changes:

**旧版本**
```
public class MainApplication extends Application implements ReactApplication {
  private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
    @Override
    protected boolean getUseDeveloperSupport() {
      return BuildConfig.DEBUG;
    }
    @Override
    protected String getJSBundleFile() {
      return CodePush.getJSBundleFile();
    }
    @Override
    protected List<ReactPackage> getPackages() {
      // 3. Instantiate an instance of the CodePush runtime and add it to the list of
      // existing packages, specifying the right deployment key. If you don't already
      // have it, you can run "code-push deployment ls <appName> -k" to retrieve your key.
      return Arrays.<ReactPackage>asList(
        new MainReactPackage(),
        new CodePush("deployment-key-here", MainApplication.this, BuildConfig.DEBUG)
      );
    }
  };
  @Override
  public ReactNativeHost getReactNativeHost() {
      return mReactNativeHost;
  }
}
```

**新版本**
```
public class MainApplication extends NavigationApplication {
  protected List<ReactPackage> getPackages() {
    return Arrays.<ReactPackage>asList(
      new MainReactPackage(),
      new CodePush(getResources().getString(R.string.reactNativeCodePush_androidDeploymentKey), getApplicationContext(), BuildConfig.DEBUG),
      new ReactVideoPackage(),
      new SpeechPackage(),
      new DplusReactPackage(),
      new RCTPdfView(),
      new RNFetchBlobPackage(),
      new PickerViewPackage(),
      new RCTToastPackage(),
      new RNSpinkitPackage(),
      new ReactNativeConfigPackage(),
      new VectorIconsPackage()
    );
  }

  @Override
  public String getJSMainModuleName() {
    return "index";
  }

  @Override
  public boolean isDebug() {
    return BuildConfig.DEBUG;
  }

  @Nullable
  @Override
  public List<ReactPackage> createAdditionalReactPackages() {
    return getPackages();
  }

  @Override
  public void onCreate() {
    super.onCreate();
    SoLoader.init(this, /* native exopackage */ false);
    RNUMConfigure.init(this, "5a484223f43e48271100002f", "Umeng", UMConfigure.DEVICE_TYPE_PHONE,
            "669c30a9584623e70e8cd01b0381dcb4");
    MobclickAgent.setSessionContinueMillis(1000);
    MobclickAgent.setScenarioType(this, MobclickAgent.EScenarioType.E_DUM_NORMAL);
  }
}
```

#### 关于 deployment-key 的设置
在上述代码中我们在创建 CodePush 实例的时候需要设置一个 deployment-key分生产环境与测试环境两种，所以建议大家在 build.gradle 中进行设置。在 build.gradle 中的设置方法如下：

打开 android/app/build.gradle 文件，找到 android { buildTypes {} } 然后添加如下代码即可：
```
android {
  ...
  buildTypes {
    debug {
      ...
      // CodePush updates should not be tested in Debug mode
      ...
    }
    releaseStaging {
      ...
      buildConfigField "String", "CODEPUSH_KEY", '"<INSERT_STAGING_KEY>"'
      ...
    }
    release {
      ...
      buildConfigField "String", "CODEPUSH_KEY", '"<INSERT_PRODUCTION_KEY>"'
      ...
    }
  }
  ...
}
```

> 心得：另外，我们也可以将 deployment-key 存放在 local.properties 中：

```
code_push_key_production=erASzHa1-wTdODdPJDh6DBF2Jwo94JFH08Kvb
code_push_key_staging=mQY75RkFbX6SiZU1kVT1II7OqWst4JFH08Kvb
```

然后就可以再 android/app/build.gradle 可以通过下面方式来引用它了：
```
Propertire properties = new Properties()
properties.load(project.rootProject.file('local.properties').newDataInputStream())
android {
  ...
  buildTypes {
    debug {
      ...
      // CodePush updates should not be tested in Debug mode
      ...
    }
    releaseStaging {
      ...
      buildConfigField "String", "CODEPUSH_KEY", '"'+properties.getProperty("code_push_key_staging")+'"'
      ...
    }
    release {
      ...
      buildConfigField "String", "CODEPUSH_KEY", '"'+properties.getProperty("code_push_key_production")+'"'
      ...
    }
  }
  ...
}
```

在 android/app/build.gradle 设置好 deployment-key 之后呢，我们就可以这样使用了:
```
@Override
protected List<ReactPackage> getPackages() {
  return Arrays.<ReactPackage>asList(
    ...
    new CodePush(BuildConfig.CODEPUSH_KEY, MainApplication.this, BuildConfig.DEBUG), // Add change this line
  )
}
```

然后修改 versionName。
在 android/app/build.gradle 中有个 android.defaultConfig.versionName属性，我们需要把应用版本改成 1.0.0 (默认是1.0，但 CodePush 需要三位数)。

```
adnroid {
  defaultConfig{
    versionName "1.0"
  }
}
```
---
## 5. Release an app update
After making changes to your app’s code or assets, push the update to your staging environment by using the CLI command which corresponds to the app type you are building (React Native or Cordova), and specifies the name of your CodePush app and the platform that your update is targetting (iOS or Android).

**React Native**
Run the `release-react` command in the CodePush CLI, which will handle bundling your JavaScript and asset files and releasing the update to the CodePush server.

For example: `code-push release-react MyApp-iOS ios`

![554BD5D6-AA50-4C62-A131-F24735562D48](https://tvax4.sinaimg.cn/large/a9034e0egy1gjvn5ejzg2j21z403qmyd.jpg)

**Android**:
 * bundle: 
```
react-native bundle --platform ios --entry-file index.js --bundle-output ./bundles/main.jsbundle --dev false

code-push release JiqirenApp-IOS ./bundles/index.ios.bundle 1.0.0 --deploymentName Staging --description "要想生活过得去，头上必须带点绿" --mandatory true
```
 * release:
```
// 注意打包的时候要包括上 assets 资源
react-native bundle --platform android --entry-file index.js --bundle-output ./bundles/index.android.bundle --dev false

code-push release JiqirenApp-Android ./bundles/index.android.bundle 1.0.0 --deploymentName Staging --description "要想生活过得去，头上必须带点绿" --mandatory true
```

**iOS**:

**Cordova**:
Run the `release-cordova` command in the CodePush CLI, which will handle bundling your JavaScript and asset files and releasing the update to the CodePush server.

For example: `code-push release-cordova MyApp-Android android`

---
## Run your app
And that’s it! All users running your app will receive the update using the experience you configured in step #4. For more details, refer to the CLI and client SDK documentation (Cordova, React Native).
