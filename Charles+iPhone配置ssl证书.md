## Charles+iPhone配置ssl证书

### 一、手机

###1. 配置代理
设置->无线局域网->选和电脑同一网络的无线->配置代理->手动 

服务器配置电脑的IP，端口设置为8888

####2. 安装证书
在Charles->help->ssl proxysettings->选mobile 那行，会弹出个提示，

在手机浏览器里访问提示里出现的网址（我的是chls.pro/ssl），安装


####3. 证书信任
Navigate to Settings > General > About > Certificate Trust Settings and turn the switch on for your custom certificate.

如果最后一步没有配置的话，访问会报错：`SSLHandshake: Remote host closed connection during handshake`



### 二、Charles配置

一般手机上的代理配置好后，打开Charles会有个弹窗，是否允许代理，点允许，要是点错了，可以在Proxy->Access control setting里把IP加上。

#### 配置ssl检测的host 
Proxy->SSL Proxying ->Add , 在host里填*，port输入框里填*
