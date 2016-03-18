---
layout: post
title:  "Tomcat乱码解决"
date:   2016-03-18 14:44:05 +0800
categories: blog
tags:   [java,tomcat]
---

最近遇到个问题，在浏览器中提交一个请求，包含中文值的参数，在服务器端接收到编码为`ISO-8859-1`，而不是期望的`UTF-8`，结果造成乱码问题。

应用的架构为jsp＋tomcat＋struts＋postgresql。

通过排查和struts，postgresql都没有关系。Tomcat的默认的编码使用的`ISO-8859-1`，一般通过以下几个步骤把整个环境设置为`UTF-8`。

1. 提交请求的页面
        
        - jsp `<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>`
        
        - HTML `<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">`

2. 请求编码设置

        - GET: 在server.xml的<Connector>中指定`URIEncoding="UTF-8"`。
        
        - POST：使用过滤器

            {% highlight java %}
            {% raw %}
            package yan.test.tomcat.encoding;
            
            import java.io.IOException;
            
            import javax.servlet.Filter;
            import javax.servlet.FilterChain;
            import javax.servlet.FilterConfig;
            import javax.servlet.ServletException;
            import javax.servlet.ServletRequest;
            import javax.servlet.ServletResponse;
            
            public class CharacterEncodingFilter implements Filter{
            
                @Override
                public void destroy(){
                }
                
                @Override
                public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException,ServletException   {
                request.setCharacterEncoding("UTF-8");
                response.setContentType("text/html; charset=UTF-8");
                chain.doFilter(request, response);
                }
                
                @Override
                public void init(FilterConfig arg0) throws ServletException {
                }
                
            }

            {% endraw %}
            {% endhighlight %}


            {% highlight xml %}
            {% raw %}
            <filter>  
                <filter-name>characterEncoding</filter-name>  
                <filter-class>yan.test.tomcat.encoding.CharacterEncodingFilter</filter-class>  
            </filter>  
            <filter-mapping>  
                <filter-name>characterEncoding</filter-name>  
                <url-pattern>/*</url-pattern>  
            </filter-mapping>  
            {% endraw %}
            {% endhighlight %}

我的问题很奇怪，通过上面的办法在servlet接收到的字符还是使用`ISO-8859-1`编码。最后发现原来在这个应用中配置了个valve<https://tomcat.apache.org/tomcat-7.0-doc/config/valve.html>。在请求到达我的编码过滤器的时候，已经被getParameter()，这样就导致请求编码使用了默认的`ISO-8859-1`。

解决方法是在该valve类的invoke类开始添加`request.setCharacterEncoding("UTF-8");`

#### 附录

中途修改请求参数的方法:

因为请求是不可更改的，如果向请求参数map中put，会得到例外`java.lang.IllegalStateException: No modifications are allowed to a locked ParameterMap`

方法：自己实现一个HttpServletRequestWrapper

        {% highlight java %}
        {% raw %}
        package yan.test.tomcat.encoding;
    
        import java.util.Enumeration;
        import java.util.HashMap;
        import java.util.Map;
        import java.util.Vector;
        
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletRequestWrapper;
            
        public class MYHttpServletRequestWrapper extends HttpServletRequestWrapper{
            
            Map<String, String[]> params = null;
            
            public MYHttpServletRequestWrapper(HttpServletRequest request,Map inParam) {
                super(request);
                params = new HashMap(inParam);
            }
            
            public void setParameter(String key,String value){
                params.put(key, new String[]{value});
            }
            public void setParameter(String key,String[] values){
                params.put(key, values);
            }
            
            @Override
            public String getParameter(String name) {
                 Object v = params.get(name);  
                    if (v == null) {  
                        return null;  
                    } else if (v instanceof String[]) {  
                        String[] strArr = (String[]) v;  
                        if (strArr.length > 0) {  
                            return strArr[0];  
                        } else {  
                            return null;  
                        }  
                    } else {  
                        return v.toString();  
                    }  
            }
            
            @Override
            public Map<String, String[]> getParameterMap() {
                return params;
            }
            
            @Override
            public Enumeration<String> getParameterNames() {
                 Vector l = new Vector(params.keySet());  
                 return l.elements();  
            }
            
            @Override
            public String[] getParameterValues(String name) {
                return params.get(name);
            }
            
        }
        {% endraw %}
        {% endhighlight %}

然后在修改请求的地方（例如一个过滤器）使用其中的2个setParameter()方法。

        {% highlight java %}
        {% raw %}
        if (request.getParameter("name") != null){
            HttpServletRequest http_req = (HttpServletRequest)request;  
            MYHttpServletRequestWrapper myrequest = new MYHttpServletRequestWrapper(http_req, request.getParameterMap());  
            myrequest.setParameter("name",new String(request.getParameter("name").getBytes("ISO-8859-1"),"UTF-8") );
            request =  myrequest;
        }
        chain.doFilter(request,response);
        {% endraw %}
        {% endhighlight %}


