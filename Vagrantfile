Vagrant.configure("2") do |config|
  # Webserver
  config.vm.define "webserver" do |webserver|
    webserver.vm.box = "centos/7"
    webserver.vm.network "forwarded_port", guest: 80, host: 8080
    webserver.vm.network "forwarded_port", guest: 21, host: 2121

    webserver.vm.provision "shell", inline: <<-SHELL
      # Install necessary packages
      yum install -y httpd vsftpd

      # Configure HTTP server
      echo "<html><body><h1>Welcome to My Web Server!</h1></body></html>" > /var/www/html/index.html
      echo "Error 404: Page Not Found" > /var/www/html/404.html

      # Configure FTP server
      systemctl enable vsftpd
      systemctl start vsftpd

      # Create user 'webdev' with password '4TheWeb!'
      useradd webdev
      echo '4TheWeb!' | passwd --stdin webdev
      chown -R webdev:webdev /var/www/html
    SHELL
  end

  # Email server
  config.vm.define "emailserver" do |emailserver|
    yum install -y postfix dovecot

    # Configure Postfix
    echo "myhostname = mailserver.local" >> /etc/postfix/main.cf
    echo "mydomain = local" >> /etc/postfix/main.cf
    echo "myorigin = \$mydomain" >> /etc/postfix/main.cf
    echo "inet_interfaces = all" >> /etc/postfix/main.cf
    echo "mydestination = \$myhostname, localhost.\$mydomain, localhost" >> /etc/postfix/main.cf
    systemctl enable postfix
    systemctl start postfix

    # Configure Dovecot
    echo "listen = *" >> /etc/dovecot/dovecot.conf
    systemctl enable dovecot
    systemctl start dovecot
  end

  # Database server
  config.vm.define "dbserver" do |dbserver|
    # Install necessary packages
    yum install -y mariadb-server

    # Start and enable MariaDB
    systemctl start mariadb
    systemctl enable mariadb

    # Secure MariaDB installation (set root password and remove anonymous users)
    mysql_secure_installation <<EOF

y
4TheDB!
4TheDB!
y
y
y
y
EOF

    # Create user 'dbdev' with password '4TheDB!'
    mysql -u root -p4TheDB! -e "CREATE USER 'dbdev'@'localhost' IDENTIFIED BY '4TheDB!';"
    mysql -u root -p4TheDB! -e "GRANT ALL PRIVILEGES ON *.* TO 'dbdev'@'localhost' WITH GRANT OPTION;"
    mysql -u root -p4TheDB! -e "FLUSH PRIVILEGES;"
  SHELL
  end

  # Monitoring server
  config.vm.define "monitoringserver" do |monitoringserver|
    # Install necessary packages
    yum install -y mariadb-server

    # Start and enable MariaDB
    systemctl start mariadb
    systemctl enable mariadb

    # Secure MariaDB installation (set root password and remove anonymous users)
    mysql_secure_installation <<EOF

y
4TheDB!
4TheDB!
y
y
y
y
y
EOF

    # Create user 'dbdev' with password '4TheDB!'
    mysql -u root -p4TheDB! -e "CREATE USER 'dbdev'@'localhost' IDENTIFIED BY '4TheDB!';"
    mysql -u root -p4TheDB! -e "GRANT ALL PRIVILEGES ON *.* TO 'dbdev'@'localhost' WITH GRANT OPTION;"
    mysql -u root -p4TheDB! -e "FLUSH PRIVILEGES;"
  SHELL
  end


  # File server
  config.vm.define "fileserver" do |fileserver|
     # Install necessary packages
    yum install -y lvm2

    # Create a physical volume and volume group
    pvcreate /dev/sdb
    vgcreate myvg /dev/sdb

    # Create a logical volume
    lvcreate -L 1G -n mylv myvg

    # Format the logical volume
    mkfs.ext4 /dev/myvg/mylv

    # Mount the logical volume
    mkdir /mnt/shared
    mount /dev/myvg/mylv /mnt/shared

    # Ensure the logical volume is mounted on boot
    echo "/dev/myvg/mylv   /mnt/shared   ext4   defaults   0   0" >> /etc/fstab
  SHELL
  end
end
