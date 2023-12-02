# README
SystemUI是在**system-server进程**之外的一个为系统提供UI的*持久进程*。  
这里的大部分代码起点是一系列由**SystemUIApplication**启动的基于SystemUI的service。而这些service是基于由Dependency提供注入的客制化依赖。  

