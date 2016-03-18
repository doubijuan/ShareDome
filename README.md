# 探索Android调用系统的分享功能
## 很多的应用为了应用的推广和传播都会使用“分享”的功能，点击分享按钮，就能将想要分享的内容或者图片分享至QQ空间、微博、微信朋友圈等实现了分享功能的应用。这篇文章主要是为了学习与探索调用系统实现分享功能或者直接调起实现了分享功能的应用的activity来进行分享。

## 目前实现一键分享功能的方式有两种：
### 1.需要集成第三方官方SDK包，在获得官方授权后调用其API来完成一键分享功能，例如使用友盟分享等
    优点：无缝集成，功能多
    缺点：需要集成官方的SDK包并通过申请官方的授权才可进行开发
### 2.不需要使用任何第三方SDK包，可以直接调起实现了分享功能的应用的activity来进行分享
    优点：不需要使用任何第三方SDK包和申请官方授权
    缺点：需要手机安装你需要分享的应用（这一点非常重要，一开始测试的时候一直不成功，提示“没有应用可执行此操作”，后来找了很久才发现是我手机没有安装相对应的应用，这也是不好方便的地方）

## 按照惯例先来看一下最终效果图：
![](http://img.blog.csdn.net/20160116004744878?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

## ShareActivity.class
```Java
package com.xiaolijuan.sharedome;

import android.app.Activity;
import android.content.Context;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.os.Environment;
import android.view.View;
import android.widget.RelativeLayout;

import java.io.File;
import java.util.ArrayList;

/**
 * 项目名称：ShareDome
 * 类描述：
 * 创建人：xiaolijuan
 * 创建时间：2016/1/13 23:48
 */
public class ShareActivity extends Activity implements View.OnClickListener {
    private RelativeLayout mRlShareText, mRlShareSingleimage, mRlShareMultipleimage, mRlShareQQ, mRlShareTencent, mRlShareWechat, mRlShareMicroblog, mRlShareOther;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_share);
        bindView();
    }

    private void bindView() {
        mRlShareText = (RelativeLayout) findViewById(R.id.rl_share_text);
        mRlShareSingleimage = (RelativeLayout) findViewById(R.id.rl_share_singleimage);
        mRlShareMultipleimage = (RelativeLayout) findViewById(R.id.rl_share_multipleimage);

        mRlShareQQ = (RelativeLayout) findViewById(R.id.rl_share_qq);
        mRlShareTencent = (RelativeLayout) findViewById(R.id.rl_share_qqtencent);
        mRlShareWechat = (RelativeLayout) findViewById(R.id.rl_share_wechat);
        mRlShareMicroblog = (RelativeLayout) findViewById(R.id.rl_share_microblog);
        mRlShareOther = (RelativeLayout) findViewById(R.id.rl_share_other);

        mRlShareText.setOnClickListener(new ShareText());
        mRlShareSingleimage.setOnClickListener(new ShareSingleImage());
        mRlShareMultipleimage.setOnClickListener(new ShareMultipleImage());
        mRlShareQQ.setOnClickListener(this);
        mRlShareTencent.setOnClickListener(this);
        mRlShareWechat.setOnClickListener(this);
        mRlShareMicroblog.setOnClickListener(this);
        mRlShareOther.setOnClickListener(this);
    }

    //分享文字至所有第三方软件
    public class ShareText implements View.OnClickListener {
        @Override
        public void onClick(View v) {
            Intent intent = new Intent();
            intent.setAction(Intent.ACTION_SEND);
            intent.putExtra(Intent.EXTRA_TEXT, "这里是分享内容");
            intent.setType("text/plain");
            //设置分享列表的标题，并且每次都显示分享列表
            startActivity(Intent.createChooser(intent, "分享到"));
        }
    }

    //分享单张图片至所有第三方软件
    public class ShareSingleImage implements View.OnClickListener {
        @Override
        public void onClick(View v) {
            String imagePath = Environment.getExternalStorageDirectory() + File.separator + "13e277bb0b9c2e3ab90229463357bf40.jpg";
            //由文件得到uri
            Uri imageUri = Uri.fromFile(new File(imagePath));

            Intent shareIntent = new Intent();
            shareIntent.setAction(Intent.ACTION_SEND);
            shareIntent.putExtra(Intent.EXTRA_STREAM, imageUri);
            shareIntent.setType("image/*");
            startActivity(Intent.createChooser(shareIntent, "分享到"));
        }
    }

    //分享多张图片至所有第三方软件
    public class ShareMultipleImage implements View.OnClickListener {
        @Override
        public void onClick(View v) {
            ArrayList<Uri> uriList = new ArrayList<>();

            String path = Environment.getExternalStorageDirectory() + File.separator;
            uriList.add(Uri.fromFile(new File(path+"13e277bb0b9c2e3ab90229463357bf40.jpg")));
            uriList.add(Uri.fromFile(new File(path+"869895e73ddd710e8a132afb37461bf0.jpg")));
            uriList.add(Uri.fromFile(new File(path+"4753fc4cd47aa1833c70df4c08f4b7fa.jpg")));
            uriList.add(Uri.fromFile(new File(path+"355ee87cf0ff612331a790f31b3ad113.jpg")));
            uriList.add(Uri.fromFile(new File(path+"ce61ad4d44e3099d87040f38faabbf56.jpg")));

            Intent shareIntent = new Intent();
            shareIntent.setAction(Intent.ACTION_SEND_MULTIPLE);
            shareIntent.putParcelableArrayListExtra(Intent.EXTRA_STREAM, uriList);
            shareIntent.setType("image/*");
            startActivity(Intent.createChooser(shareIntent, "分享到"));
        }
    }

    @Override
    public void onClick(View v) {
        String pakName = "";
        Intent intent = new Intent(Intent.ACTION_SEND); // 启动分享发送的属性
        intent.setType("text/plain"); // 分享发送的数据类型
        switch (v.getId()) {
            case R.id.rl_share_qq:
                pakName = "com.qzone";  //qq空间
                break;
            case R.id.rl_share_qqtencent:
                pakName = "com.tencent.WBlog"; //腾讯微博
                break;
            case R.id.rl_share_wechat:
                pakName = "com.tencent.mm";  //微信
                break;
            case R.id.rl_share_microblog:
                pakName = "com.sina.weibo";  //微博
                break;
            case R.id.rl_share_other:
                break;
            default:
                break;
        }
        intent.setPackage(pakName);
        intent.putExtra(Intent.EXTRA_TEXT, "这里是分享内容"); // 分享的内容
        this.startActivity(Intent.createChooser(intent, ""));// 目标应用选择对话框的标题;
    }
}
```

## 由于分享功能是使用隐式调用Activtiy实现的，则需在响应的Activity中声明intent-filter，在对应的activity的xml里加上
```Java
<intent-filter>
                <action android:name="android.intent.action.SEND" />
                <category android:name="android.intent.category.DEFAULT" />
</intent-filter>
```
