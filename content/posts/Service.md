---
share: "true"
---
title: Service
date: 2016-09-26
tags:
- android
- 第一行代码

---
<font style="font-family:微软雅黑">
## Service
### 基本用法
#### 和Activity进行通信
1. 继承Binder对象

	    public class DownloadBinder extends Binder{
        int count = 0;
        Handler handler;
        public void setHandler(Handler handler) {
            this.handler = handler;
        }
        public void startDownload() {

        }
    }
2. 继承Service，Override onBind函数,新增成员：DownloadBinder。

		public class MyService extends Service {
		    DownloadBinder downloadBinder = new DownloadBinder();
		    @Nullable
		    @Override
		    public IBinder onBind(Intent intent) {
		        return downloadBinder;
		    }
		}

3. Activity中，连接时获得DownloadBinder实例，这样就可以通过Binder来与Service进行通信。

	    ServiceConnection connection = new ServiceConnection() {
	        @Override
	        public void onServiceConnected(ComponentName name, IBinder service) {
	            binder = (MyService.DownloadBinder) service;
	            binder.setHandler(handler);
	            binder.startDownload();
	        }
	
	        @Override
	        public void onServiceDisconnected(ComponentName name) {
	        
	        }};
4. 绑定service

                Intent intent = new Intent(ServiceLearnActivity.this, MyService.class);
                bindService(intent, connection, BIND_AUTO_CREATE);


#### 生命周期
* startService方法调用时，启动相应的服务，如果服务之前没有创建，那么会先回调onCreate方法，接下来回调onStartCommand方法。
* 每个服务只会存在一个实例。
* bindService方法：回调onBind方法：如果服务之前还没有创建，那么会先回调onCreate方法。