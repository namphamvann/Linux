# Tối ưu mysql
* monitoring
* check log
* use some diagnostic tool (tool chẩn đoán)
I. Monitor (giám sát)
  1. top
     a. Load monitoring in linux server top, W and uptime for check the load on server
commands: 
```
top
W
uptime
```
	 
    b.kill process which is consuming high cpu
```
kill -9 PID
```
    c. if php process are consuming more cpu , can use:
```
killall -9 php
```
    d.restart apache
 ```
 killall -9 httpd
 /etc/init.d/httpd restart
```
2. Monitor bằng mytop
 b1:install mytop
 b2:dùng lệnh
```
mytop --prompt -d database_name
```	
3. Tăng số connection
 *Mặc định trong mysql số connection tối đa là 151 nonnection
 * Tăng connection:
```
MariaDB [(none)]> set global max_connections = 1001;
```
 * Tăng MaxRequestWorkers trong file cấu hình apache:
    - Vào đường dẫn /etc/httpd/conf/httpd.conf add thêm:
        MaxRequestWorkers 1000
        ServerLimit 1000
4. Tweak cache
 a. Thread cache size
-default off
-Khi một client disconnect thread của client sẽ được đặt vào cache nếu như nó nhỏ hơn thread_cache_size.
-Công thức: 8 + (max_connections / 100)
-câu lệnh:
```
MariaDB [(none)]> show variables like 'thread_cache_size'; --> mặc định bằng 0
MariaDB [(none)]> set global thread_cache_size = 4
MariaDB [(none)]> show variables like 'thread_%';
```	    

 Variable_name             | Value                     
-------------------------- | ---------------------------
 thread_cache_size         | 4                         
 thread_concurrency        | 10                        
 thread_handling           | one-thread-per-connection 
 thread_pool_idle_timeout  | 60                        
 thread_pool_max_threads   | 500                       
 thread_pool_oversubscribe | 3                         
 thread_pool_size          | 2                         
 thread_pool_stall_limit   | 500                       
 thread_stack              | 294912                    


b. Query cache
  - Đây là dung lượng bộ nhớ (RAM) được phân bổ cho kết quả truy vấn bộ đệm. Điều này sẽ tăng tốc kết quả từ cơ sở dữ liệu MySQL. Biến này được BẬT theo mặc định. Ba biến chính liên quan đến Cache truy vấn được liệt kê bên dưới
  - Query_cache_type: ON 0r OFF
  - Query_cache_size: dung lượng bộ nhớ Ram dành cho cache, thường để 6M/1G RAM
	    - câu lệnh: MariaDB [(none)]> set global query_cache_size = 62914560;
            - MariaDB [(none)]> SET GLOBAL query_cache_type = 1;	  
	    - MariaDB [(none)]> SHOW STATUS LIKE 'Qc%';

Variable_name           | Value      
----------------------- | ---------------------------
Qcache_free_blocks      | 2          
Qcache_free_memory      | 62808216                       
Qcache_hits             | 680                            
Qcache_inserts          | 50                              
Qcache_lowmem_prunes    | 0                              
Qcache_not_cached       | 50       
Qcache_queries_in_cache | 46       
Qcache_total_blocks     | 104      

Trong đó:
     *Qcache_free_memory: bộ nhớ đệm còn trống
     *cache_hits: là số lượng query đã được lấy từ cache
     *Qcache_lowmem_prunes: là số lượng các query không thể cache do thiếu RAM
     *Qcache_inserts: là số lượng query đã thực thi xong và được đưa vào cache 
     * Các truy vấn INSERT / UPDATE / DELETE sẽ được thực thi vào trong database mà không bị cache.
     * Nếu khởi động lại MySQL các cấu hình trên sẽ mất tác dụng. Để áp dụng vĩnh viễn các bạn cần phải khai báo vào file config của MySQL tại đường dẫn sau:
		     [root@server ~]# nano /etc/my.cnf
		- Với nội dung:      query_cache_size = 62914560 byte 
                                     query_cache_type = 1
		
      * Query_cache_limit:giới hạn bộ nhớ đệm cho truy vấn,không lưu vào bộ nhớ đệm nếu truy vấn lớn hơn số byte này. Giá trị mặc định là 1MB = 1048576 byte
	
c. Table open cache
5. Buffer

II. check mysql log files
1. Error log
2. General log
    Mặc định không đc bật
3. Slow query log
* Mặc định ko bật. Đây là file log quan trọng để lưu lại log làm database chậm
* Enable slow query logging by adding the following entry in MySQL configuration
     slow_query_log=/var/log/mariadb/slow.log
     

