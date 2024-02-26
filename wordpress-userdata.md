#!/bin/bash
sudo mkdir /var/www/
sudo mount -t efs -o tls,accesspoint=fsap-0afa03e4c0cab80ca fs-0fbb38c2455bc2379:/ /var/www/
sudo yum install -y httpd 
sudo systemctl start httpd
sudo systemctl enable httpd
sudo yum module reset php -y
sudo yum module enable php:remi-7.4 -y
sudo yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
sudo wget http://wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz
sudo rm -rf latest.tar.gz
sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
sudo mkdir /var/www/html/
sudo cp -R /wordpress/* /var/www/html/
cd /var/www/html/
sudo touch healthstatus
sudo sed -i "s/localhost/bankycs-database.cz8wqg4y0kmz.us-east-1.rds.amazonaws.com/g" wp-config.php 
sudo sed -i "s/username_here/BankyCSadmin/g" wp-config.php 
sudo sed -i "s/password_here/admin12345/g" wp-config.php 
sudo sed -i "s/database_name_here/wordpressdb/g" wp-config.php 
sudo chcon -t httpd_sys_rw_content_t /var/www/html/ -R
sudo systemctl restart httpd