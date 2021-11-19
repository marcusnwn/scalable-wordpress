# Scalable-wordpress

![scalable-wordpress](https://user-images.githubusercontent.com/9261163/142619963-6b506231-4f12-45de-8948-c0733d8f544f.png)

[Link](http://ec2-13-213-39-102.ap-southeast-1.compute.amazonaws.com/)


Use AWS RDS as the database

- Scalable using Read Replica

- Auto backup

- Highly available using multi AZ 

Create an EC2 instance for hosting Wordpress

- Allow SSH, HTTP in security group

Create a RDS

- In RDS VPC Secuirty Group, edit inbound rules to allow EC2 wordpress security group

In EC2, install mysql client and connect to mysql database

```
sudo yum install -y mysql
mysql -h mysql–instance1.123456789012.us-east-1.rds.amazonaws.com -P 3306 -u mymasteruser -p
```

Create database 

```
CREATE DATABASE wordpress;
```

Create Database user

```
CREATE USER 'wordpress' IDENTIFIED BY 'wordpress-pass';
GRANT ALL PRIVILEGES ON wordpress.* TO wordpress;
FLUSH PRIVILEGES;
Exit
```

Install and start Apache Web server on EC2
```
sudo yum install -y httpd
sudo service httpd start

```

Download and configure Wordpress
```
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
cd wordpress
cp wp-config-sample.php wp-config.php
vim wp-config.php
```

Edit the values
```
/** The name of the database for WordPress */
define( 'DB_NAME', 'database_name_here' );

/** MySQL database username */
define( 'DB_USER', 'username_here' );

/** MySQL database password */
define( 'DB_PASSWORD', 'password_here' );

/** MySQL hostname */
define( 'DB_HOST', 'localhost' );
```

Install dependencies

```
sudo amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2
```

Copy wordpress files to apache web server
```
cd /home/ec2-user
sudo cp -r wordpress/* /var/www/html/

```
Allow web server to install file
```
sudo chown -R apache:apache /var/www/html

```
Update wp-config.php, adding
```
define( ‘FS_METHOD’, ‘direct’ );
```

Restart web server
```
sudo service httpd restart
```

Plan in the future:

Use EFS, mount EFS to EC2 /var/www/html to have multiple EC2 instance share the storage. 

Use W3 Total Cache Wordpress plugin for caching by Elasticache
