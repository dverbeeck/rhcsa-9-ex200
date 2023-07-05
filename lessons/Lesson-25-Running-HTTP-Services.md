# Lesson 25: Running HTTP Services

- [Lesson 25: Running HTTP Services](#lesson-25-running-http-services)
    - [25.1 Exploring HTTP Configuration](#251-exploring-http-configuration)
    - [25.2 Creating a Basic Web Site](#252-creating-a-basic-web-site)
    - [Lesson 25 Lab: Managing HTTP Services](#lesson-25-lab-managing-http-services)
    - [Lesson 25 Lab Solution: Managing HTTP Services](#lesson-25-lab-solution-managing-http-services)

### 25.1 Exploring HTTP Configuration

- Apache (httpd) is a common web server on Linux.
- Nginx is another common web server.
- The main httpd configuration file is /etc/httpd/conf/httpd.conf
- Additional drop-in files can be stored in /etc/httpd/conf.d/
- The default DocumentRoot is /var/www/htdocs
- Apache looks for a file with the name index.html in this directory.

```bash
dnf install httpd;
systemclt status httpd;
ls /var/www/
ls /etc/httpd/conf/
vim /etc/httpd/conf/httpd.conf;
```

### 25.2 Creating a Basic Web Site

```bash
cd /var/www/html
ls;
curl http://localhost
vim index.html
Hello, welcome to my site!

curl http://localhost
cd /var/log/httpd
cat error_log
cd /var/www/html/
ls -l
chmod o+r index.html
curl http://localhost
```

### Lesson 25 Lab: Managing HTTP Services

- Configure Apache to serve a basic website that shows the text "hello world" after connecting to it.
- Use `curl localhost` to verify Connection.

### Lesson 25 Lab Solution: Managing HTTP Services

```bash
cd /var/www/html
echo "hello world" > index.html
ls -l;
chmod o+r index.html;
systemctl restart httpd.service;
curl localhost
```
