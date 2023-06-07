---
title: Android网络请求回调
date: 2023-06-07 15:58:40
tags:
categories: 
- 前端杂烩
---
# Android的HTTP网络请求超时UI更新（回调）

###  原因及运行原理
当在网络请求出现连接超时（httpURLConnection.setConnectTimeout(TimeOut)）的时候，此时调用的活动中的Dialog弹窗会一直浮现在桌面上，而网络请求是位于子线程，活动只是调用之后就没有太多的关联，这时当网络出现连接超时的时候会一直卡在httpURLConnection.connect()中，此时可以在网络请求类中新建一个接口，并且实现一个刷新方法，再在活动中实例化一个请求类，并且参数为接口对象，然后再到网络请求出现超时，并且抛出异常的try...catch代码块的异常处理块中用接口对象调用刷新的方法，由于接口内的刷新方法时抽象方法，所以会自动往子级里面去找到运行的代码块，也就是说运行活动中覆写的代码块，注意：虽然运行的代码块时活动中的，但是其调用的主体方法还是在子线程中，所以需要更新UI的时候，还是需要回调到主线程中        

```java
public  class BaseHttpConn {

    private static final String TAG = "BaseHttpConn";
    private static TimeOut shuaxin;
    private static int TimeOut = 3*1000;
    public static HttpURLConnection httpConn(String baseUrl, HashMap<String, String> map) {
        HttpURLConnection httpURLConnection = null;
        try {
            URL url = new URL(baseUrl);
            httpURLConnection = (HttpURLConnection) url.openConnection();
            httpURLConnection.setConnectTimeout(TimeOut);//连接超时
            httpURLConnection.setReadTimeout(TimeOut);//读取超时
            httpURLConnection.setRequestMethod("POST");//请求方式
            httpURLConnection.setInstanceFollowRedirects(true);//是否重定向
            httpURLConnection.setUseCaches(true);//是否启用缓存
            httpURLConnection.setRequestProperty("Content-Type","application/json");//传入服务器的数据类型
            OutputStream outputStream = httpURLConnection.getOutputStream();
            outputStream.write(getByte(map));
            outputStream.close();
        } catch (Exception e) {
            e.printStackTrace();
            httpURLConnection.disconnect();//关闭此次网络请求
            shuaxin.ShuaXin();//通过接口对象调用抽象方法ShuaXin();

        }
        return httpURLConnection;
    }

    private static byte[] getByte(HashMap<String,String> map) {
        JSONObject jsonObject = new JSONObject();
        for(String key: map.keySet()){
            try {
                jsonObject.put(key,map.get(key));
            } catch (JSONException e) {
                e.printStackTrace();
            }
        }
        return jsonObject.toString().getBytes();
    }

    public BaseHttpConn(TimeOut timeOut) {
        this.shuaxin = timeOut;
    }

    //监听接口
    public interface TimeOut{
        void ShuaXin();//接口中刷新的方法
    }

}
```
### Activity中的代码
```java
     BaseHttpConn baseHttpConn = new BaseHttpConn(new BaseHttpConn.TimeOut() {
        @Override
        public void ShuaXin() {
            progressDialog.dismiss();
            getActivity().runOnUiThread(new Runnable() {//位于碎片中，所以需要通过getActivity()获取活动
                @Override
                public void run() {
                    Toast.makeText(getContext(),"网络连接失败",Toast.LENGTH_SHORT).show();
                }
            });
        }
    });
```
