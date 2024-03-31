---
share: "true"
title: servletRequest中读取body的问题
date: 2018-01-26 16:55:07
tags:
  - java web
  - servlet
  - java
  - web
---

# ServletRequest中读取body的数据
	
	 InputStream body = request.getInputStream();
## 问题
* 数据流仅仅能够读取一次,如果你想要多次读取(在多个调用链中)(比如说在多个过滤器中),例如

		chain.doFilter(request, response);
这样后面的调用中request将读取不到body的数据.
<!--more-->
## 解决
* 继承实现HttpServletRequest接口的HttpServletRequestWrapper,重载getInputStream方法.
* ![](http://7xkzud.com1.z0.glb.clouddn.com/18-1-26/37804903.jpg)

## 具体实现
### BufferedServletRequestWrapper类

	public class BufferedServletRequestWrapper extends HttpServletRequestWrapper {
	    private byte[] buffer;
	    public BufferedServletRequestWrapper(HttpServletRequest request) throws IOException {
	        super(request);
	        InputStream is = request.getInputStream();
	        ByteArrayOutputStream baos = new ByteArrayOutputStream();
	        byte buff[] = new byte[ 1024 ];
	        int read;
	        while( ( read = is.read( buff ) ) > 0 ) {
	            baos.write( buff, 0, read );
	        }
	        this.buffer = baos.toByteArray();
	    }
	    @Override
	    public ServletInputStream getInputStream() throws IOException {
	        return new BufferedServletInputStream( this.buffer );
	    }
	}
### 在过滤器中

	ServletRequest requestWrapper = new BufferedServletRequestWrapper(req);
	InputStream inputStream = requestWrapper.getInputStream();
	String content = IOUtils.toString(inputStream, req
			.getCharacterEncoding());
	/**对内容进行处理**/
	chain.doFilter(requestWrapper, response);
	/**由于我们在初始化时保存了byte,重载了getInputStream方法,所以后续调用时可以获得body中的数据**/
