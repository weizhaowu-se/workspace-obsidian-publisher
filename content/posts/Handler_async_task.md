---
share: "true"
---
title: 异步消息处理
date: 2016-09-26
tags:
- android
- 第一行代码

---
<font style="font-family:微软雅黑">
## Handler
### Handler的使用
1. 继承Handler类，Override它的handleMessage方法（处理消息）

		Handler handler = new Handler() {
	        @Override
	        public void handleMessage(Message msg) {
	            super.handleMessage(msg);
	            textView.setText("after handling" + msg.getData().getString("key"));
	        }
	    };
	* tips:Message可能需要用到的两个成员分别是
		* msg.what
		* msg.setData(),msg.getData()

2. 在新开的线程中使用handler的sendMessage方法发送消息，以达到修改UI元素的效果。

                new Thread(new Runnable() {
                    @Override
                    public void run() {
                        Message msg = new Message();
                        Bundle bundle = new Bundle();
                        bundle.putString("key", textView.getText().toString());
                        msg.setData(bundle);
                        handler.sendMessage(msg);
                    }
                }).start();
3. 一些思考


		MessageQueue：消息队列
		Looper：循环，一直从消息队列中获得消息，如果没有消息则一直循环。成员变量有MessageQueue。ThreadLocal变量，每个线程唯一且相同。
		Handler：成员变量有Looper，亦即也拥有MessageQueue，发送消息时向消息队列插入消息，有Looper负责发现消息并且根据Message的target（亦即handler）来负责处理消息。
	* 所以，当我们在子线程中用handler发送消息时，插入的MessageQueue是主线程的MessageQueue，此时由handler的操作便变成了主线程的操作而非在子线程中操作界面。

## AsyncTask
1. 继承AsyncTask类

		//参数，运行时返回值类型，结果类型
	    class DownloadTask extends AsyncTask<String, Integer, Boolean> {
        int count = 0;
		//任务启动前执行的函数
        @Override
        protected void onPreExecute() {
            progressDialog.show();
        }
		//后台执行的函数，可以进行耗时操作
        @Override
        protected Boolean doInBackground(String... params) {
            while (true) {
                try {
                    TimeUnit.SECONDS.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                count++;
                if (count >= 10) {
                    break;
                }
				//这个方法会调用更新进度显示的函数。
                publishProgress(count);
            }
            return true;
        }
		//更新进度显示
        @Override
        protected void onProgressUpdate(Integer... values) {
            progressDialog.setMessage("downloaded " + values[0]*10 + "%");
        }
		//后台执行完成之后调用。
        @Override
        protected void onPostExecute(Boolean aBoolean) {
            progressDialog.dismiss();
            if(aBoolean) {
                Toast.makeText(MessageHandleActivity.this, "succeed", Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(MessageHandleActivity.this, "failed", Toast.LENGTH_SHORT).show();
            }
        }
    }