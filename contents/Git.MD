#### 设置全局代理
 走HTTP代理
> git config --global http.proxy "http://127.0.0.1:8080";  
> git config --global https.proxy "http://127.0.0.1:8080";  

 走socks5代理（如 小飞机 or V2xxxx）
> git config --global http.proxy "socks5://127.0.0.1:1080";  
> git config --global https.proxy "socks5://127.0.0.1:1080";

 取消设置
> git config --global --unset http.proxy;  
> git config --global --unset https.proxy;



#### 设置本地代理

> git config http.proxy "http://127.0.0.1:8080"  
> git config https.proxy "http://127.0.0.1:8080"



**KEY**（`河图洛书`）

`ghp_mLnpJXEIi1ZApWSZKTUqPAiCe0DzUk4aVD85`