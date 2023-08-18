### 一. nginx文件配置解释：
#### 1. 全局块：配置影响nginx全局的指令。
		worker_processes    1； 

#### 2. event块：配置影响nginx服务器或与用户的网络连接。
		events { 
				worker_connections  1024; 
		} 
#### 3. http块：可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。反向代理主要配置这里 
		http{ 
			#http的全局块 
			.......
			#http的server块：配置虚拟主机的相关参数，一个http可以配置多个server 
			#一般反向代理写法： 
			server{ 
					listen               80;                                 # 在浏览器访问时的端口号，默认为80端口号，在浏览器输入地址时可以不用加端口号	 
					server_name    192.168.17.256;              # 在浏览器访问时的ip地址，如果购买了域名，备案后直接在浏览器用域名访问   
					#http的location块：配置请求的路由，一个server可以配置多个location          
					location ~ /edu/ {                                  # edu是访问地址的路径path
							proxy_pass   http:127.0.0.1:8080;       # 后端代码启动的地址 
					}
					location ~ /vod/ {                                  
							proxy_pass   http:127.0.0.1:8081;      
					}
			}
			#负载均衡写法：
			upstream myserver{
					server 192.168.17.129:8080 weight=2;
					server 192.168.17.129:8081 weight=1;
					#ip_hash;
					#fair；
			}
			server{
					listen               80;                                	
					server_name    192.168.17.256;              
					location ~ /edu/ {                                  
							proxy_pass   http://myserver;       
					}
			}
		}


### 二. nginx配置多个前端项目：
#### 1. 前端项目：
		1. webpack配置output.publicPath: '/app/'，
				开发模式时，需要把historyApiFallback配置为对象{index：'/app/index.html'}
		2. 路由vue-router的base属性：'/app/'
#### 2. nginx文件：
		1. 把打包好的文件放到html文件夹下的app文件夹
		2. 配置nginx：
		在location /app{
				root  html;  # 不需要加路径，会自动拼接
				#alias  html/app/; # 需要完整的文件路径
				index index.html; # 指定文件夹下的什么文件
				try_files $uri $uri/  /app/index.html; # 防止history模式下修改url报错
				gzip_static on; #开启gizp资源压缩
		}
		不存在的路径可以通过vue-router捕获且设置一个页面