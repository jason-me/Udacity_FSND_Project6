Udacity_FSND_Project6 - Linux Server Configuration -- Charitable Goods Inventory Catalog by Jason M. Hester

==============================

Summary

    Baseline installation of a Linux distribution on a virtual machine prepared to host a web application, that includes installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

IP Address and URL

    IP: http://18.222.93.215/
    Hostname: ec2-18-222-93-215.us-east-2.compute.amazonaws.com
    Password for Grader: "GRADER"
    SSH: Port 2200

Software Installed

    Apache2
    GIT
    mod_wsgi
    Python
    PIP
    virtualenv
    Flask
    httplib2
    sqlalchemy
    psycopg2-binary
    requests
    PostgreSQL

Configurations

    cd .ssh
    -----------------------

    chmod 600 ~/.ssh/LightsailDefaultPrivateKey-us-east-Hester2.pem
    -----------------------

    ssh -i ~/.ssh/LightsailDefaultPrivateKey-us-east-Hester2.pem ubuntu@18.222.93.215
    -----------------------

    sudo su -
    -----------------------

    sudo adduser grader
    -----------------------

    sudo nano /etc/sudoers.d/grader
    "grader ALL=(ALL:ALL)ALL"
    -----------------------

    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    -----------------------

    sudo apt-get install finger
    -----------------------


    *New Terminal
    ssh-keygen -f ~/.ssh/LightsailDefaultPrivateKey-us-east-Hester2.rsa
    -----------------------

    cat ~/.ssh/LightsailDefaultPrivateKey-us-east-Hester2.rsa.pub
    -----------------------

    *Server Terminal
    cd /home/grader
    -----------------------

    mkdir .ssh
    -----------------------

    touch .ssh/authorized_keys
    -----------------------

    nano .ssh/authorized_keys
    -----------------------

    sudo chmod 700 /home/grader/.ssh
    sudo chmod 644 /home/grader/.ssh/authorized_keys
    -----------------------

    sudo chown -R grader:grader /home/grader/.ssh
    -----------------------

    sudo service ssh restart
    -----------------------

    ssh -i ~/.ssh/LightsailDefaultPrivateKey-us-east-Hester2.rsa grader@18.222.93.215
    -----------------------

    sudo nano /etc/ssh/sshd_config
    Change PasswordAuth and PermitRootLogin to No and Port to 2200
    -----------------------

    sudo service ssh restart
    -----------------------

    ssh -i ~/.ssh/LightsailDefaultPrivateKey-us-east-Hester2.rsa grader@18.222.93.215 -p 2200
    -----------------------

    sudo ufw allow 2200/tcp
    sudo ufw allow 80/tcp
    sudo ufw allow 123/udp
    sudo ufw enable
    -----------------------

    sudo ufw status
    -----------------------

    sudo apt-get install apache2
    sudo apt-get install libapache2-mod-wsgi python-dev
    sudo apt-get install git
    -----------------------

    sudo a2enmod wsgi
    sudo service apache2 restart
    -----------------------

    cd /var/www
    sudo mkdir catalog
    sudo chown -R grader:grader catalog
    cd catalog
    -----------------------

    var/www/catalog/catalog.wsgi
    -----------------------

    git clone https://github.com/jason-me/fullstack-nanodegree-vm.git catalog
    git branch production
    -----------------------

    sudo nano catalog.wsgi
    "import sys
    import logging
    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0, "/var/www/catalog/")

    from catalog import app as application
    application.secret_key = 'super_secret_key'"
    -----------------------

    mv project.py __init__.py
    -----------------------

    sudo apt-get install python-pip
    -----------------------

    pip install virtualenv
    virtualenv venv
    source venv/bin/activate
    chmod -R 777 venv
    -----------------------

    pip install flask
    pip install httplib2 oauth2client sqlalchemy psycopg2-binary requests
    -----------------------

    Set __init__.py file path for client secrets to: /var/www/catalog/catalog/client_secrets.json
    change:
    if __name__ == '__main__':
    #    app.secret_key = 'super_secret_key'
    #    app.debug = True
    #app.run(host='0.0.0.0', port=8000)
     app.run()
    -----------------------

    sudo nano /etc/apache2/sites-available/catalog.conf
    "<VirtualHost *:80>
        ServerName 18.222.93.215
        ServerAlias ec2-18-222-93-215.us-east-2.compute.amazonaws.com
        ServerAdmin admin@18.222.93.215
        WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
        WSGIProcessGroup catalog
        WSGIScriptAlias / /var/www/catalog/catalog.wsgi
        <Directory /var/www/catalog/catalog/>
            Order allow,deny
            Allow from all
        </Directory>
        Alias /static /var/www/catalog/catalog/static
        <Directory /var/www/catalog/catalog/static/>
            Order allow,deny
            Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
    "
    -----------------------

    sudo a2ensite catalog.conf
    sudo a2dissite 000-default.conf
    sudo service apache2 reload
    -----------------------

    sudo apt-get install libpq-dev python-dev
    sudo apt-get install postgresql postgresql-contrib
    sudo su - postgres
    psql
    -----------------------

    postgres=# CREATE USER catalog WITH PASSWORD 'GRADER';
    postgres=# ALTER USER catalog CREATEDB;
    postgres=# CREATE DATABASE catalog with OWNER catalog;
    postgres=# \c catalog
    catalog=# REVOKE ALL ON SCHEMA public FROM public;
    catalog=# GRANT ALL ON SCHEMA public TO catalog;
    catalog=# \q
    $ exit
    -----------------------

    Edit all engines from
    sqlite://catalog.db to postgresql://catalog:GRADER@localhost/catalog
    -----------------------

    sudo service apache2 restart
    -----------------------

    python database_setup.py
    python database_init.py
    python __init__.py

Third Party Resources

    https://github.com/mulligan121/Udacity-Linux-Configuration
    https://github.com/iliketomatoes/linux_server_configuration
    https://github.com/vectors36/Udacity_FullStack_Linux
    http://flask.pocoo.org/docs/dev/deploying/mod_wsgi/#troubleshooting

