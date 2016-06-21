---
layout: post
section-type: post
title: How to solve CORS problem in your api with spring boot
category: tech
tags: [ 'tutorial', 'spring', 'documentation', 'api' ]
---


Recently I've had to migrate an old-fashion api to a new one with spring boot from scratch.

One of the main problems when we create an api is that we have to solve the CORS problem, when your clients want to make more than only GET operations.

After looking into Internet and because i am using spring boot 1.3.5, I've chosen use the new annotation @CrossOrigin, but, 
it didn't worked. 

I really don't know why but my main problem is that the CORS problem will have to be solved during today.

So I opted by a more traditional solution.

I've created a java servlet filter to configure the responses of my api.

This is an approach of the filter:

````
package com.myolnir.cors;

import org.springframework.stereotype.Component;
import javax.servlet.*;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Component
public class CORSFilter implements Filter {
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) throws IOException, ServletException {
        HttpServletResponse response = (HttpServletResponse) res;
        response.setHeader("Access-Control-Allow-Origin", "*");
        response.setHeader("Access-Control-Allow-Methods", "PUT, POST, GET, OPTIONS, DELETE");
        response.setHeader("Access-Control-Max-Age", "151200");
        response.setHeader("Access-Control-Allow-Headers", "Content-Type,X-Requested-With,Host,User-Agent,Accept," +
                "Accept-Language,Accept-Encoding,Accept-Charset,Keep-Alive,Connection,Referer,Origin");
        chain.doFilter(req, res);
    }

    public void init(FilterConfig filterConfig) {
    }

    public void destroy() {
    }

}
````

With this little filter, your api will be prepared to allow POST, PUT and DELETE operations.

Maybe it is not the "spring way" to solve the problem, but because the rush of find the solution, It's worked for my.

Also, not everything in Java world is based in spring, sometimes with a traditional java approach we can solve a problem, and as you can see
in the piece of code i've used spring by injecting the filter in the spring boot context.

I hope you've found usefull this small post.

Thanks for reading me.

See you next time!