# 自动加载XML配置文件
1. 现在已经找到的加载xml方式有：
    - 
```C++
TiXmlDocument doc;
TiXmlElement *pConfig = doc.FirstChildElement("config");
```