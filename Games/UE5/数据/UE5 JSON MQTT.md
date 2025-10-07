# 参考
## MQTT
参考：
[UE5--MQTT通信 - 哔哩哔哩](https://www.bilibili.com/opus/1027123158085271602?spm_id_from=333.1391.0.0)

所用的MQTT插件地址：
[NinevaStudios/mqtt-utilities-unreal: MqttUtilities is a plugin for Unreal Engine intended to expose MQTT client functionality to blueprints](https://github.com/NinevaStudios/mqtt-utilities-unreal)

在项目根目录创建Plugins文件夹，将插件下载到此处，打开以下路径，编辑MqttUtilities.uplugin
![](img/Pasted%20image%2020251007170251.png)
修改EngineVersion成你的UE5的版本，我这里是5.6.1。
![](img/Pasted%20image%2020251007170350.png)
完成之后，重启UE5，加载即可。