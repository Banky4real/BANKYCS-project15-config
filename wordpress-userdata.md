#!/bin/bash
mkdir /var/www/
sudo mount -t efs -o tls,accesspoint=fsap-02bdd3444d7daa571 fs-00e17c213e40a2245:/ /var/www/
yum install -y httpd 
systemctl start httpd
systemctl enable httpd
yum module reset php -y
yum module enable php:remi-7.4 -y
yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
systemctl start php-fpm
systemctl enable php-fpm
wget http://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz
rm -rf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
mkdir /var/www/html/
cp -R /wordpress/* /var/www/html/
cd /var/www/html/
touch healthstatus
sed -i "s/localhost/bankycs-database.czseckswarhm.us-east-1.rds.amazonaws.com/g" wp-config.php 
sed -i "s/username_here/BankyCSadmin/g" wp-config.php 
sed -i "s/password_here/Intercommunity@123./g" wp-config.php 
sed -i "s/database_name_here/wordpressdb/g" wp-config.php 
chcon -t httpd_sys_rw_content_t /var/www/html/ -R
systemctl restart httpd