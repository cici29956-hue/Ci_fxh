1.==更新系统并安装Ningx+php==
	sudo apt update                                  //  这串命令是更新Linux系统
	sudo apt install nginx php-fpm -y      //  安装Nginx
	
2.==启动服务(kali 默认没启动)==
	sudo systemctl start nginx                  //  启动Nginx服务器
	sudo systemctl start php8.4-fpm       
	//   启动php-fpm(这是php的进程管理器是负责管责php进程的,算是包工头)

3.==创建测试的php文件==
	echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/index.php
	//  echo是输出文本,变量,内容(后面的内容就是用输出的)
	//  |  这个是管道符号指的是别**前面的输出** 是 **后面的输入**
	//  tee是读取标准输入,同时输出到终端和指定文件
4.==配置Nginx解析php==
	sudo vim /etc/nginx/sites-available/default
	//  /etc/nginx/          Nginx服务器的主配置目录,存放所有的配置文件
	//  sites-available/    Nginx的虚拟主机配置存放目录,这令通常存放所有的可用的网站配置文件
	//  default                 这是默认站点的配置文件名
	
	location ~ \.php$ { 
	//匹配所有  .php 结尾的请求  ~表示启用正则匹配  \.php$匹配url末尾的  .php
	(\转义.  $表示结尾)
		include snippets/fastcgi-php.conf;
		//引用Nginx预设的FastCGI通用配置(这个文件里包含了 `fastcgi_param` 等一系列参数，用来规范 PHP 请求的转发规则，不用手动写一堆重复配置)
		fastcgi_pass unix:/run/php/php8.4-fpm.sock; 
		//将PHP请求转发给PHP-FPM进程处理(unix:   表示使用 **Unix 域套接字（本地 sock 文件）** 通信，比 TCP 端口更快、更安全    /run/php/php8.2-fpm.sock  是 PHP-FPM 监听的 sock(套接字--是计算机网络中"进程精通行"的一种标准接口) 文件路径（和你 `ls /run/php/` 看到的文件名一致）)
	}


5.==查看php-fpm中的套接字文件==
	ls /run/php/
	//  这用可以查看php-fpm的版本(上面在配置Nginx的时候要用上,很关键),但是要检查php-fpm的服务状态,如果没启动要将服务跑起来,不然执行不了这串指令

6.==在浏览器输入http://127.0.0.1/index.php即可==