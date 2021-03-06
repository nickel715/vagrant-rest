Vagrant-Rest
============

A lamp stack with chef, centos 7.3, vagrant, nginx, php-7.0, php-fpm, mysql 5.7 and composer. But instead of using apache, I switched to nginx.

Install
=======

1. choose your virtualization product
 - install virtual-box >= 5.1.12
 - install parallels 10
2. install vagrant = 1.8.7
3. install the necessary plugins for vagrant, if not yet happened
 ```
 vagrant plugin install vagrant-hostmanager
 vagrant plugin install vagrant-cachier
 vagrant plugin install vagrant-winnfsd # only for Windows
 ```

 Hostmaster is needed to add/remove entries in your local /etc/hosts file. To support development domains
 Cachier is needed to prevent downloading rpm´s again. This is usefull during setting up a vm, when you have online internet  via cellphone like inside a train :-)

 If you're using parallels you also have to install the vagrant plugin
 ```
 vagrant plugin install vagrant-parallels
 ```


4. do the git stuff
 ```
 git clone https://github.com/ofeige/vagrant-rest
 cd vagrant-rest
 ```

5. start vagrant with virtual box
 ```
 vagrant up
 ```
 or with parallels
 ```
 vagrant up --provider=parallels
 ```

Config Option
=============

You can setup dedicated virtual hosts, when you change following line

```
node.hostmanager.aliases = %w(rest-demo)
```

and

```
:lamp  => {
  vhosts: [ "rest-demo" ]
}
```

here you can add more virtual hosts or change the name. Please always change both lines. The first one, will create entries in you /etc/hosts, without these entries you can´t access the virtual hosts with your local browser.

How it works
============

if you call http://www.project1.local it will search for a app.php inside the project1.local/web/ folder. It is really easy to start with a symfony2 application. If you need an other rewrite you can change the ngnix template file under chef/cookbooks/lamp/templates/default/wildcard-site.erb for your need!

if you want to change mysql settings, update this file: chef/cookbooks/lamp/templates/default/my-mysql-settings.erb

Please don´t forget to run ```vagrant provision``` after you change the template files.

If you want to connect to mysql from your host system you have to use the mysql password! If you want to connect from your vagrant environment you have to add "-h 127.0.0.1"

Port 80 connect to varnish and Port 8080 directly to your project without caching

Demo application
================

The following steps will enable you to see a first rest application demo.

1. Clone the rest demo in your vagrant-rest folder
 ```
 git clone https://github.com/ofeige/rest-demo
 ```

2. Setup the demo application
 ```
 vagrant ssh
 cd /vagrant/rest-demo/
 composer install
 # just set the database_port: 3306 and the database_password: changeme
 ```

3. Start the demo application
 ```
 cd bin
 php console doctrine:database:create
 php console doctrine:schema:create
 php console server:start
 ```
4. Execute the unit tests
 ```
 cd /vagrant/rest-demo/ && vendor/phpunit/phpunit/phpunit
 ```

Change Log
==========
13 November 2014
First release for this project

22 June 2014
 - Fix fileupload issues (ngnix & php-fpm related)
 - Clean the project structure and remove unused stuff
 - Use of only one chef cookbook "lamp" instead of "angi" + "lamp"
 - remove all submodule and used now copy of official cookbooks

16 September 2015
 - fix .dev dns problems
 - provide correct example inside README
 - rewrote the cookbooks, it is much cleaner now and no need for submodules

03 October 2015
 - add default timezone setting for php

24 November 2015
 - add possibility to change the mysql pw

11 Dezember 2015
 - add mysql settings to Vagrantfile

21 January 2016
- fix plugin name vagrant-hostmanager instead of vagrant-hostmaster
- fix vagrant 1.8.1 problem with chef_zero config option. solution was using chef_solo
- .local is a official domain now. removed the toplevel domain

10 December 2016
- updated all cookbooks to the latest version
- updated to centos 7.2
- add varnish for caching

13 January 2017
- improved the README
- added information about the rest demo application
- changed virtualbox and vagrant version

17 January 2017
- added Windows support
- improved plugin handling
- fixed connection problems with between the vagrantbox and virtualbox
