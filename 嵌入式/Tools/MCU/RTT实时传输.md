
# RTT介绍

> 参考：
> https://www.segger.com/products/debug-probes/j-link/technology/about-real-time-transfer/

> SEGGER 的实时传输（RTT）是嵌入式应用中系统监控和交互式用户输入输出的成熟技术。它结合了软件运营和半托管的优势，且性能非常高。

> 通过 RTT，可以从目标微控制器输出信息，并以极高速度向应用程序发送输入，而不影响目标的实时行为。SEGGER RTT 可与任何 [J-Link](https://www.segger.com/products/debug-probes/j-link/) 型号及任何支持后台内存访问的目标处理器（即 Cortex-M 和 RX 目标）使用。

就是可以将RTT当成调试信息日志来使用，用法和printf类似，并且几乎不组设

