---
title: springMVC使用注意点
date: 2017-04-19 00:00:11
tags: JAVA
categories: spring
---

### 一、尽量不用@Response
> 因为使用@Response会出现中文乱码的情况，所以可以用一个工具类：
<!--more-->
```java

import java.io.PrintWriter;
import javax.servlet.http.HttpServletResponse;

public class ResponseUtil {
	public static void write(HttpServletResponse response, Object obj)throws Exception{
		response.setContentType("text/html;charset=utf-8");
		PrintWriter out = response.getWriter();
		out.println(obj.toString());
		out.flush();
		out.close();
	}
}

```