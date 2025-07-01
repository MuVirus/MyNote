# 环境
## Windows 安装
直接使用VsCode安装ESP-IDF就行了，安装后，下载自己想要的ESP-IDF版本，我是esp-idf v5.4，所以我有两个目录：一个是54_tools，一个是v5.4（其中54_tools是自己取的）。

## 官方示例
esp-idf会提供示例，存放在 \v5.4\esp-idf\examples 

# 日志宏

^db9d0d

ESP_LOGI：Info（信息）级别，用于描述程序的运行状态、关键步骤或正常操作。
ESP_LOGE：Error（错误）级别，用于记录错误信息，通常是在程序运行过程中遇到问题或异常时使用。
ESP_LOGW：Warning（警告）级别，用于记录可能的问题或需要注意的情况。
ESP_LOGD：Debug（调试）级别，用于记录调试信息，通常在开发阶段使用。
ESP_LOGV：Verbose（详细）级别，用于记录非常详细的调试信息，通常用于深入调试。
