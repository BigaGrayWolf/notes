#### SSH恶意流量检测

##### 步骤一：构造流量数据（分为两个维度：正常用户|恶意用户，登陆成功|登陆失败）

目前我想的是，通过两台虚拟机相互攻击，其中ubuntu系统中构建ssh_server,kali系统自带hydra和wireshark,采用python脚本控制进行数据收集。【可行性待验证】



bug:kali使用ifconfig命令获取IP失败，并且无法上网

解决方案：https://blog.csdn.net/D15993395520/article/details/104945487



安装ssh-server:

https://blog.csdn.net/jackghq/article/details/54974141



ssh会限制用户的错误登陆次数（默认为3次），该方案可修改（因为）安全原因不做了

http://blog.itpub.net/24376892/viewspace-675046/



kali安装libpcap-dev失败

kali更换为国内源

https://blog.csdn.net/weixin_43372529/article/details/103298291



python pypcap库

https://www.litreily.top/2018/12/31/pypcap-install/



##### 步骤二：数据解析\提取特征

wireshark保存的后缀为.pcap的文件，可用python进行解析，并以某种方式存储（推荐json格式）。提取两方面的内容：一是各层协议头部字段，二是应用层包含的加密内容。应用层包含加密内容采用信息熵的特征进行提取，同时将报文头部信息产生的特征进行融合。

【预计分工】：初步解析【肖】、头部字段特征提取【陈】、应用层加密内容特征提取【王】

##### 步骤三：模型训练

预计采用三种RNN模型：,GRU【陈】、LSTM【王】、BiLSTM【肖】。

