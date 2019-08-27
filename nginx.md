             # Install nginx trên centos 
**1. Tạo file nginx.repo
* Vào đường dẫn: vim /etc/yum.repos.d/nginx.repo
* Thêm:
    '''
       [nginx]
       name=nginx repo
       baseurl=http://nginx.org/packages/mainline/centos/7/$basearch/
       gpgcheck=0
       enabled=1
        '''
2. Install
   yum install nginx
3. Start nginx
   systemctl start nginx
4. Enable nginx ( để start nginx nếu reboot lại server)
     systemctl enable nginx
5. Tao virtualhost
* Virtual host được tạo trong thư mục /etc/nginx/conf.d
* Tạo virtualhost 
   - vim /etc/nginx/conf.d/virtualhost.conf
   - Cấu hình file virtualhost.conf: 
       
    server {
       listen       80;
       server_name  bongdaweb.com;
       access_log  /var/log/nginx/virtualhost/access.log;
       error_log   /var/log/nginx/virtualhost/error.log;
    location / {
           root   /usr/share/nginx/html;
           index   index.html;
    }
 }
