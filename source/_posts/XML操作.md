---
title: XML操作
date: 2018-1-6 1:27:00
categories: JAVA
tags: JAVA
---

### 一、需要被解析的xml
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

### 二、利用Dom4j进行解析
```java
public void dom4jParseXML(String path){
    try {
        // 1. 获取SAXReader对象
        org.dom4j.io.SAXReader reader = new SAXReader();
        // 获取resouce
        URL resource = getClass().getClassLoader().getResource(path);
        // 2. 利用SAXReader对象的read方法获取Docment对象
        org.dom4j.Document document = reader.read(resource);
        // 3. 获取document对象的根节点
        org.dom4j.Element rootElement = document.getRootElement();
        // 4. 通过根节点的elementIterator方法获取对应下面的所有子节点
        Iterator it = rootElement.elementIterator();
        // 5. 循环迭代器
        while (it.hasNext()){
            // 5.1 获取到每一个学生节点
            org.dom4j.Element ele = (org.dom4j.Element) it.next();

            System.out.println("======开始一个学生的解析======");
            //开始某一个学生的解析
            List<Attribute> attributes = ele.attributes();
            for (Attribute attribute : attributes) {
                System.out.println(attribute.getName() + ":" + attribute.getValue());
            }
            //获取该学生的所有的子节点
            Iterator stdAtt = ele.elementIterator();
            while (stdAtt.hasNext()){
                org.dom4j.Element oneAttr = (org.dom4j.Element) stdAtt.next();
                System.out.println(oneAttr.getQName().getName() +":"+ oneAttr.getText());
            }
            System.out.println("======结束一个学生的解析======");

        }
    } catch (DocumentException e) {
        e.printStackTrace();
    }
}
```

### 三、直接利用的jdk的dom进行解析
（目前此段代码还有点儿问题，暂时不究，后期再补）
目标是将上述的xml解析成为一个map对象：
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

    @Test
    public void test(){
        parseXML("students.xml  ");
    }
    private Map<String,Object> parseXML(String path){
        try {
            // 获取到DocumentBuilder
            DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
            DocumentBuilder db = dbf.newDocumentBuilder();

            //获取到Document
//            InputStream in = new FileInputStream(path);//经常出现找不到文件的错误
            URL resource = getClass().getClassLoader().getResource(path);
            URLConnection urlConnection = resource.openConnection();
            final InputStream in = urlConnection.getInputStream();
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
            return null;
        }
    }
}

```

