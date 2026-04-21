
# IM UIKit Android 常见问题排查指南
<!--keywords:IM UIKit,Android,常见问题,FAQ,故障排查,问题解决,资源文件,发送照片,音视频通话,话单-->

本文介绍如何排查 IM UIKit 的常见问题。

## 1. IM UIKit 源码导入项目后找不到资源文件


1. 在项目根目录下找到`gradle.propertes`文件。
2. 将`android.nonTransitiveRClass`设置为`false`。

## 2. 引入IM UIKit 之后，发送照片或者文件出现 Crash


如果发送照片或者文件的时候出现 crash，并触发报错`java.lang.IllegalArgumentException: Failed to find configured root that contains /data/user/0/*** /cache/nim/file/ ***at androidx.core.content.FileProvider$SimplePathStrategy.getUriForFile(FileProvider.java:800)`，说明没有`cache` 目录的读取权限（根据报错中的`*** /cache/nim/file/ ***`判断），需要在`FileProvidr` 中配置 `cache` 目录。




配置步骤：





1. 在工程的`AndroidManifest.xml`文件中的`<Application>`节点配置如下`provider`内容。


    ```java
    <provider
        android:name="com.netease.yunxin.kit.common.utils.CommonFileProvider"
        android:authorities="${applicationId}.IMKitFileProvider"
        android:exported="false"
        android:grantUriPermissions="true">
        <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/file_paths" />
    </provider>

    ```


2. 在工程的`res`目录下，添加`xml`目录，并创建`file_paths.xml`文件。在文件中添加如下内容。
    ```java 
    <paths>
        <external-path
            name="root"
            path="." />
        <external-cache-path
            name="tempPictures"
            path="TempPictures" />
        <!-- cache 目录报错添加 -->
        <cache-path
            name="cachePath"
            path="."/>
    </paths>
    ```



:::note note
- 发送照片或者文件时出现 crash 也可能触发其他类似报错，以上仅以`cache` 目录的配置步骤为例进行说明。
- 如果报错提示无其他目录的读取权限，添加相应的`path`即可，配置步骤类似。
:::



## 3. 接入呼叫组件后，话单无法展示

若您在 9.3.0 之前的 UIKit 版本中引入呼叫组件实现音视频通话时，可能会出现话单无法展示的问题。

在呼叫组件初始化时，或者在您接入 IM 的 Activity 中的 `onCreate` 中设置呼叫组件的话单发送监听以及复写话单发送功能。

以 Activity 为例，`MainActivity` 是您展示会话列表和好友的页面。

::: details 示例代码
```
public class MainActivity extends AppCompatActivity {
    protected void onCreate(Bundle savedInstanceState) {
        //设置呼叫组件话单监听
        NERTCVideoCall.sharedInstance()
        .setCallOrderListener(
        new DefaultCallOrderImpl() {
          @Override
          public void onTimeout(ChannelType channelType, String accountId, int callType) {
            ALog.dApi(
                "CallOrderImpl",
                new ParameterMap("onTimeout")
                    .append("channelType", channelType)
                    .append("callType", callType)
                    .append("accountId", accountId)
                    .append("enableOrder", isEnable())
                    .toValue());
            if (!isEnable()) {
              return;
            }
            if (NERTCVideoCall.sharedInstance().getCurrentState() == CallState.STATE_INVITED) {
              return;
            }
            if (NetworkUtils.isConnected()) {
              sendOrder(
                  channelType, accountId, NrtcCallStatus.NrtcCallStatusTimeout);
            } else {
              sendOrder(
                  channelType, accountId, NrtcCallStatus.NrtcCallStatusCanceled);
            }
          }

          @Override
          public void onCanceled(ChannelType channelType, String accountId, int callType) {
            sendOrder(channelType, accountId, NrtcCallStatus.NrtcCallStatusCanceled);
          }

          @Override
          public void onReject(ChannelType channelType, String accountId, int callType) {
            sendOrder(channelType, accountId, NrtcCallStatus.NrtcCallStatusRejected);
          }

          @Override
          public void onBusy(ChannelType channelType, String accountId, int callType) {
            sendOrder(channelType, accountId, NrtcCallStatus.NrtcCallStatusBusy);
          }
        });
  }   
        
private void sendOrder(ChannelType channelType, String accountId, int status) {
  NetCallAttachment netCallAttachment =
          new NetCallAttachment.NetCallAttachmentBuilder()
                  .withType(channelType.getValue())
                  .withStatus(status)
                  .build();
  IMMessage message =
          MessageBuilder.createNrtcNetcallMessage(accountId, SessionTypeEnum.P2P, netCallAttachment);
  ChatRepo.sendMessage(message,true,null);
}
}
```
:::
