Install Traccar on DigitalOcean VPS

Use following link to register a DigitalOcean account, receive $200 credit and support our open source project:

https://m.do.co/c/9c8f727601b1
Commands
Install unzip utility and MySQL server

apt update && apt -y install unzip mysql-server
Set database password and create a new database

mysql -u root --execute="ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root'; GRANT ALL ON *.* TO 'root'@'localhost' WITH GRANT OPTION; FLUSH PRIVILEGES; CREATE DATABASE traccar;"
Download the latest installer

wget https://www.traccar.org/download/traccar-linux-64-latest.zip
Unzip the file and run the installer

unzip traccar-linux-*.zip && ./traccar.run
Update the configuration file to use MySQL database

cat > /opt/traccar/conf/traccar.xml << EOF
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE properties SYSTEM 'http://java.sun.com/dtd/properties.dtd'>

<properties>

    <entry key='database.driver'>com.mysql.cj.jdbc.Driver</entry>
    <entry key='database.url'>jdbc:mysql://localhost/traccar?zeroDateTimeBehavior=round&amp;serverTimezone=UTC&amp;allowPublicKeyRetrieval=true&amp;useSSL=false&amp;allowMultiQueries=true&amp;autoReconnect=true&amp;useUnicode=yes&amp;characterEncoding=UTF-8&amp;sessionVariables=sql_mode=''</entry>
    <entry key='database.user'>root</entry>
    <entry key='database.password'>root</entry>

</properties>
EOF
Start Traccar service

service traccar start
Note that you can use above commands on any Ubuntu server, not just DigitalOcean.
