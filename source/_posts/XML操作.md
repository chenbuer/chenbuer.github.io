---
title: XML操作
date: 2018-1-6 1:27:00
categories: JAVA
tags: JAVA
---

### 一、直接利用的jdk解析xml
解析一个这样的xml：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<学生花名册>
    <学生 id="1">
        <性别>男</性别>
        <姓名>李华</姓名>
        <年龄>14</年龄>
        <电话>6287555</电话>
    </学生>
    <学生 id="2">
        <性别>女</性别>
        <姓名>张三</姓名>
        <年龄>16</年龄>
        <电话>8273425</电话>
    </学生>
</学生花名册>
```
<!--more-->
目标是将上述的xml解析成为一个map对象：
```javascript
  [id1:{
      属性1：值1，
      属性2：值2
  }，
  id2:{
      属性1：值1，
      属性2：值2
  }]
 ```
代码如下：
```java
public class XMLParseTest {
    private Map<String,Object> parseXML(String path){
        try {
            // 获取到DocumentBuilder
            DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
            DocumentBuilder db = dbf.newDocumentBuilder();

            //获取到Document
            InputStream in = new FileInputStream(path);
            Document doc = db.parse(in);

            //获取到根节点
            Element root = doc.getDocumentElement();

            //获取到根节点下的子节点，也就是所有的学生
            NodeList childNodes = root.getChildNodes();
            if (null == childNodes){
                return null;
            }
            
            // 所有学生的所有信息都放到了这个map里面
            Map<String, Object> allStds = new HashMap<String, Object>();

            for (int i=0 ; i < childNodes.getLength() ; i++){
                // 一个学生
                Node oneStd = childNodes.item(i);
                // 若此Node是一个节点
                if (Node.ELEMENT_NODE == oneStd.getNodeType()){
                    // 属性也是一个Node，获取到Id
                    Node idNode = oneStd.getAttributes().getNamedItem("id");
                    String id = idNode.getNodeValue();

                    //获取这个学生的子节点
                    NodeList oneStdChildNodes = oneStd.getChildNodes();
                    //这个学生的所有属性都放到了这个map里面
                    Map<String , Object> oneStdAttrs = new HashMap<String, Object>();
                    for (int j=0; j<oneStdChildNodes.getLength(); j++){
                        //获取到该学生的一个属性Node
                        Node stdOne = oneStdChildNodes.item(j);
                        String attribute = stdOne.getNodeName();
                        String val = stdOne.getNodeValue();
                        oneStdAttrs.put(attribute,val);
                        
                    }

                    allStds.put(id, oneStdChildNodes);

                }
            }
            
            return allStds;

        } catch (Exception e) {
            e.printStackTrace();
        }

    }


}

```

