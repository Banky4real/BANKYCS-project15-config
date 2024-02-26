#!/bin/bash
sudo mkdir /var/www/
sudo mount -t efs -o tls,accesspoint=fsap-0d2d00eb02ea79434 fs-0fbb38c2455bc2379:/ /var/www/
sudo yum install -y httpd 
sudo systemctl start httpd
sudo systemctl enable httpd
sudo yum module reset php -y
sudo yum module enable php:remi-7.4 -y
sudo yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
git clone https://github.com/Banky4real/tooling.git
sudo mkdir /var/www/html
sudo cp -R /tooling/html/*  /var/www/html/
cd /tooling
mysql -h bankycs-database.cz8wqg4y0kmz.us-east-1.rds.amazonaws.com -u BankyCSadmin -p toolingdb < tooling-db.sql
cd /var/www/html/
sudo touch healthstatus
sudo sed -i "s/$db = mysqli_connect('mysql.tooling.svc.cluster.local', 'admin', 'admin', 'tooling');/$db = mysqli_connect('bankycs-database.cz8wqg4y0kmz.us-east-1.rds.amazonaws.com', 'BankyCSadmin', 'admin12345', 'toolingdb');/g" functions.php
sudo chcon -t httpd_sys_rw_content_t /var/www/html/ -R
sudo systemctl restart httpd