
## Android 版本更新

## 目录

* [功能介绍](#功能介绍)
* [效果图与示例 apk](#效果图与示例-apk)
* [Gradle 依赖](#Gradle依赖)
* [自定义接口协议](#自定义接口协议)
* [使用默认的接口协议](#使用默认的接口协议)
* [License](#license)

## 功能介绍

- [x] 实现app版本更新
- [x] 自定义接口协议，可以不改变现有项目的协议就能使用
- [x] 支持get,post请求
- [x] 支持进度显示，对话框进度条，和通知栏进度条展示
- [x] 支持后台下载
- [x] 支持强制更新
- [x] 支持简单主题色配置(可以自动从顶部图片提取主色)
- [x] 完美支持android7.0

## 效果图与示例 apk

<img src="https://raw.githubusercontent.com/WVector/AppUpdateDemo/master/image/example_01.png?raw=true" width="1000">

<img src="https://raw.githubusercontent.com/WVector/AppUpdateDemo/master/image/example_02.png?raw=true" width="1000">

<img src="https://raw.githubusercontent.com/WVector/AppUpdateDemo/master/image/example_03.png?raw=true" width="1000">

<img src="https://raw.githubusercontent.com/WVector/AppUpdateDemo/master/image/example_05.png" width="1000">

<img src="https://raw.githubusercontent.com/WVector/AppUpdateDemo/master/image/example_06.png" width="1000">

	
[点击下载 Demo.apk](https://raw.githubusercontent.com/WVector/AppUpdateDemo/master/apk/app-debug.apk) 或扫描下面的二维码安装

![Demo apk文件二维](https://raw.githubusercontent.com/WVector/AppUpdateDemo/master/image/1498810770.png)



## Gradle 依赖

```gradle
dependencies {
    compile 'com.qianwen:update-app:3.2.3'
}
```

[![Download](https://api.bintray.com/packages/qianwen/maven/update-app/images/download.svg) ](https://bintray.com/qianwen/maven/update-app/_latestVersion) [![API](https://img.shields.io/badge/API-14%2B-orange.svg?style=flat)](https://android-arsenal.com/api?level=14) [![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)


## 自定义接口协议

自定义接口协议，根据自己项目的接口，自己实现 parseJson 方法

```java

        Map<String, String> params = new HashMap<String, String>();

        params.put("key1", "value1");
        params.put("key2", "value2");
        params.put("key3", "value3");
        params.put("key4", "value4");


        new UpdateAppManager
                .Builder()
                //当前Activity
                .setActivity(this)
                //实现httpManager接口的对象
                .setHttpManager(new UpdateAppHttpUtil())
                //设置请求方式 默认get,
                .setPost(false)
                //更新地址
                .setUpdateUrl("https://raw.githubusercontent.com/WVector/AppUpdateDemo/master/json/json.txt")
                //添加自定义参数
                .setParams(params)
                //设置头部
                .setTopPic(R.mipmap.top_5)
                //设置主题色
                .setThemeColor(0xff034ea0)
                .build()
                //检测是否有新版本
                .checkNewApp(new UpdateCallback() {
                    /**
                     * 解析json,自定义协议
                     *
                     * @param json 服务器返回的json
                     * @return UpdateAppBean
                     */
                    @Override
                    protected UpdateAppBean parseJson(String json) {
                        UpdateAppBean updateAppBean = new UpdateAppBean();
                        try {
                            JSONObject jsonObject = new JSONObject(json);
                            updateAppBean
                                    //是否更新Yes,No
                                    .setUpdate(jsonObject.getString("update"))
                                    //新版本号
                                    .setNew_version(jsonObject.getString("new_version"))
                                    //下载地址
                                    .setApk_file_url(jsonObject.getString("apk_file_url"))
                                    //大小
                                    .setTarget_size(jsonObject.getString("target_size"))
                                    //更新内容
                                    .setUpdate_log(jsonObject.getString("update_log"))
                                    //是否强制更新
                                    .setConstraint(jsonObject.getBoolean("constraint"));
                        } catch (JSONException e) {
                            e.printStackTrace();
                        }
                        return updateAppBean;
                    }

                    /**
                     * 有新版本
                     *
                     * @param updateApp        新版本信息
                     * @param updateAppManager app更新管理器
                     */
                    @Override
                    public void hasNewApp(UpdateAppBean updateApp, UpdateAppManager updateAppManager) {
                        updateAppManager.showDialog();
                    }

                    /**
                     * 网络请求之前
                     */
                    @Override
                    public void onBefore() {
                        CProgressDialogUtils.showProgressDialog(MainActivity.this);
                    }

                    /**
                     * 网路请求之后
                     */
                    @Override
                    public void onAfter() {
                        CProgressDialogUtils.cancelProgressDialog(MainActivity.this);
                    }

                    /**
                     * 没有新版本
                     */
                    @Override
                    public void noNewApp() {
                        Toast.makeText(MainActivity.this, "没有新版本", Toast.LENGTH_SHORT).show();
                    }
                });


```

## 使用默认的接口协议 


### 接口说明

#### 1，和服务器交互请求参数
	1,appkey app的唯一标志
	appkey可以在manifest文件中配置，也可以在代码中添加
	xml配置如下：

```xml

  <meta-data
            android:name="UPDATE_APP_KEY"
            android:value="ab55ce55Ac4bcP408cPb8c1Aaeac179c5f6f"/>

```
	
	2,version 版本号，工具自动添加(服务器判断客户端传过来的version和服务器存的最新的version，决定是否更新)
	
```json

```

	3, 服务器app后台管理界面(下次放出服务器的代码)

<img src="https://raw.githubusercontent.com/WVector/AppUpdateDemo/master/image/example_04.png?raw=true" width="1000">

#### 2, 服务器的返回json格式
	1,有新版本

```json

{
  "update": "Yes",//有新版本
  "new_version": "0.8.3",//新版本号
  "apk_file_url": "https://raw.githubusercontent.com/WVector/AppUpdateDemo/master/apk/app-debug.apk", //apk下载地址
  "update_log": "1，添加删除信用卡接口\r\n2，添加vip认证\r\n3，区分自定义消费，一个小时不限制。\r\n4，添加放弃任务接口，小时内不生成。\r\n5，消费任务手动生成。",//更新内容
  "target_size": "5M",//apk大小
  "constraint": false//是否强制更新
}

```

	2,没有新版本

```json

{
  "update": "No",//没有新版本
}

```
### 1，根据自己项目使用的网络框架，自己实现HttpManager接口，

```java

	class UpdateAppHttpUtil implements HttpManager {
	    /**
	     * 异步get
	     *
	     * @param url      get请求地址
	     * @param params   get参数
	     * @param callBack 回调
	     */
	    @Override
	    public void asyncGet(@NonNull String url, @NonNull Map<String, String> params, @NonNull final Callback callBack) {
	        OkHttpUtils.get()
	                .url(url)
	                .params(params)
	                .build()
	                .execute(new StringCallback() {
	                    @Override
	                    public void onError(Call call, Response response, Exception e, int id) {
	                        callBack.onError(validateError(e, response));
	                    }
	
	                    @Override
	                    public void onResponse(String response, int id) {
	                        callBack.onResponse(response);
	                    }
	                });
	    }
	
	    /**
	     * 异步post
	     *
	     * @param url      post请求地址
	     * @param params   post请求参数
	     * @param callBack 回调
	     */
	    @Override
	    public void asyncPost(@NonNull String url, @NonNull Map<String, String> params, @NonNull final Callback callBack) {
	        OkHttpUtils.post()
	                .url(url)
	                .params(params)
	                .build()
	                .execute(new StringCallback() {
	                    @Override
	                    public void onError(Call call, Response response, Exception e, int id) {
	                        callBack.onError(validateError(e, response));
	                    }
	
	                    @Override
	                    public void onResponse(String response, int id) {
	                        callBack.onResponse(response);
	                    }
	                });
	
	    }
	
	    /**
	     * 下载
	     *
	     * @param url      下载地址
	     * @param path     文件保存路径
	     * @param fileName 文件名称
	     * @param callback 回调
	     */
	    @Override
	    public void download(@NonNull String url, @NonNull String path, @NonNull String fileName, @NonNull final FileCallback callback) {
	        OkHttpUtils.get()
	                .url(url)
	                .build()
	                .execute(new FileCallBack(path, fileName) {
	                    @Override
	                    public void inProgress(float progress, long total, int id) {
	                        super.inProgress(progress, total, id);
	                        callback.onProgress(progress, total);
	                    }
	
	                    @Override
	                    public void onError(Call call, Response response, Exception e, int id) {
	                        callback.onError(validateError(e, response));
	                    }
	
	                    @Override
	                    public void onResponse(File response, int id) {
	                        callback.onResponse(response);
	
	                    }
	
	                    @Override
	                    public void onBefore(Request request, int id) {
	                        super.onBefore(request, id);
	                        callback.onBefore();
	                    }
	                });
	
	    }
	}

```



### 3,客户端检测是否有新版本，并且更新下载


```java

	String updateUrl = "https://raw.githubusercontent.com/WVector/AppUpdateDemo/master/json/json.txt";
	new UpdateAppManager
	        .Builder()
	        //当前Activity
	        .setActivity(this)
	        //实现httpManager接口的对象
	        .setHttpManager(new UpdateAppHttpUtil())
	        //更新地址
	        .setUpdateUrl(updateUrl)
	        .build()
	        //检测是否有新版本
	        .checkNewApp(new UpdateCallback() {
	            /**
	             * 有新版本
	             * @param updateApp 新版本信息
	             * @param updateAppManager app更新管理器
	             */
	            @Override
	            public void hasNewApp(UpdateAppBean updateApp, UpdateAppManager updateAppManager) {
	                updateAppManager.showDialog();
	            }
	
	            /**
	             * 网络请求之前
	             */
	            @Override
	            public void onBefore() {
	                CProgressDialogUtils.showProgressDialog(MainActivity.this);
	            }
	
	            /**
	             * 网路请求之后
	             */
	            @Override
	            public void onAfter() {
	                CProgressDialogUtils.cancelProgressDialog(MainActivity.this);
	            }
	
	            /**
	             * 没有新版本
	             */
	
	            @Override
	            public void noNewApp() {
	                Toast.makeText(MainActivity.this, "没有新版本", Toast.LENGTH_SHORT).show();
	            }
	        });

   	            
```




#### 进度条使用的是代码家的「[NumberProgressBar](https://github.com/daimajia/NumberProgressBar)」

## License

   	Copyright 2017 千匍

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.