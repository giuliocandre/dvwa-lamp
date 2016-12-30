DVWA LAMP container
=================

This definitely isn't for production. I've bundled DVWA with the tutum/lamp docker container. To run, clone the repo and build the container, naming it whatever you want. I don't intend to upload this to the Docker repository yet, but maybe at some future date.  



Usage
-----

	docker build -t remotephone/dvwa-lamp .

Running your LAMP docker image
------------------------------

Start your image binding the external ports 80 and 3306 in all interfaces to your container:

	docker run -d -p 80:80 -p 3306:3306 remotephone/lamp 

Test your deployment:

	http://localhost/setup.php




Connecting to the bundled MySQL server from within the container
----------------------------------------------------------------

The bundled MySQL server has a `root` user with no password for local connections.
Simply connect from your PHP code with this user:

	<?php
	$mysql = new mysqli("localhost", "root");
	echo "MySQL Server info: ".$mysql->host_info;
	?>


Connecting to the bundled MySQL server from outside the container
-----------------------------------------------------------------

The first time that you run your container, a new user `admin` with all privileges
will be created in MySQL with a random password. To get the password, check the logs
of the container by running:

	docker logs $CONTAINER_ID

You will see an output like the following:

	========================================================================
	You can now connect to this MySQL Server using:

	    mysql -uadmin -p47nnf4FweaKu -h<host> -P<port>

	Please remember to change the above password as soon as possible!
	MySQL user 'root' has no password but only allows local connections
	========================================================================

In this case, `47nnf4FweaKu` is the password allocated to the `admin` user.

You can then connect to MySQL:

	 mysql -uadmin -p47nnf4FweaKu

Remember that the `root` user does not allow connections from outside the container -
you should use this `admin` user instead!

The root user has been configured to use the DVWA password default, p@ssw0rd. You're a braver person than I am if you expose this. 


Setting a specific password for the MySQL server admin account
--------------------------------------------------------------

If you want to use a preset password instead of a random generated one, you can
set the environment variable `MYSQL_PASS` to your specific password when running the container:

	docker run -d -p 80:80 -p 3306:3306 -e MYSQL_PASS="mypass" <name> 

You can now test your new admin password:

	mysql -uadmin -p"mypass"


Disabling .htaccess
--------------------


For this version, I've disabled htaccess. You shouldn't be exposing this publicly anyway.

`.htaccess` is enabled by default. To disable `.htaccess`, you can remove the following contents from `Dockerfile`

	# config to enable .htaccess
    ADD apache_default /etc/apache2/sites-available/000-default.conf
    RUN a2enmod rewrite


**by http://www.tutum.co and updated by https://remotephone.github.io**
