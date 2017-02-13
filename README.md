# All package and actions to deploy rails app
=============================================
# Estimated time ~40 minutes
-----------------------------

* [Rails 5](http://rubyonrails.org/)
* [Nginx](https://www.nginx.com/resources/wiki/)
* [Mysql](https://www.mysql.com/)
* [Puma](http://puma.io/)
* [Ubuntu 16.04](https://www.ubuntu.com/)
* [Capistrano](http://capistranorb.com/)


### The tutorial work with server by [digitalocean.com](https://m.do.co/c/03deef324558)
##### Login and create a droplet Ubuntu 16.04x64 1gb cpu & 30gb disk

```
ssh root@xxx.xxx.xx

sudo apt-get install aptitude

sudo apt-get update

sudo aptitude update

sudo aptitude safe-upgrade
```
If you have error with locale
------------------------------
```
sudo locale-gen el_GR.UTF-8
```
##### Next exit from server and create ssh connection
```
ssh-copy-id root@xxx.xxx.xx

sudo adduser deploy

sudo adduser deploy sudo

ssh-copy-id deploy@xxx.xxx.xx
```

##### Edit ssh setting
```
sudo vim /etc/ssh/sshd_config
```
##### Next, we need to find the line that looks like this:

```
PermitRootLogin yes
```
##### Modify this line to "no" like this to disable root login:
```
PermitRootLogin no
```

##### Next, we need to find the line that looks like this:
```
Port 22
```
##### Modify this line between 22 and 50000:

```
Port 4323
```

##### Next, reload service ssh
```
service ssh restart
```

#### Install additional packages from Rails
```
sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties libffi-dev nodejs
```


##### Install git & nginx
```
sudo apt-get install curl git-core nginx -y
```

##### Install version ruby language rbenv
```
cd
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
exec $SHELL
```


#### Install ruby build plugin for rbenv

```
git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
exec $SHELL
```


###### Show ruby available versions

```
rbenv install -l

rbenv install 2.4.0

rbenv global 2.4.0

rbenv rehash

ruby -v
```

**You just add following line to your local `~/.gemrc` file (it is in your home folder)**

`gem: --no-document`

##### Next, install ruby gems version control system

```
gem install bundler

rbenv rehash
```


##### Install Rails

```
gem install rails -V --no-ri --no-rdoc

rbenv rehash
```


### Create ssh key and copy

```
ssh-keygen -t rsa

cat ~/.ssh/id_rsa.pub
```

##### Visit the github and add ssh, after test it

```
ssh -T git@github.com
```


#### Next, install Mysql
```
sudo aptitude update

sudo apt-get install mysql-server mysql-client libmysqlclient-dev

sudo mysql_install_db

sudo mysql_secure_installation
```

Create mysql database

```
mysql -u root -p

CREATE DATABASE `app` CHARACTER SET utf8 COLLATE utf8_general_ci;
```



**Add additional gem into Gemfile**

```
group :development do
    gem 'capistrano',         require: false
    gem 'capistrano-rvm',     require: false
    gem 'capistrano-rails',   require: false
    gem 'capistrano-bundler', require: false
    gem 'capistrano3-puma',   require: false
end

gem 'puma'
```

#### Next, install gems

```
bundle

rbenv rehash
```

#### Create files from caristrano

```
cap install
```




Copy [config/deploy.rb](../master/config/deploy.rb) & [Capfile](../master/Capfile) files
 and paste in your rails app



```
git add -A
git commit -m "Set up Puma, Nginx & Capistrano"
git push origin master
```

Run capistrano init

```
cap production deploy:initial
```



#### make file /home/deploy/apps/app/shared/config/database.yml
```
production:
  adapter: mysql2
  pool: 5
  timeout: 5000
  encoding: utf8
  database: app
  username: root
  password: xxxxxxxxx
```



#### Set ubuntu environment variable
```
rails secret

sudo vim /etc/environment
```

#### and add this line below

```
export SECRET_KEY_BASE=abb2e9e094bb8fb1aa216defed455caa0f9
```


At first time run in apps/app/release/2017456534

```
RAILS_ENV=production bundle exec rake db:create
```

Example [config/nginx.conf](../master/config/nginx.conf) file, paste in your rails app


##### set nginx virtualhost
```
sudo rm /etc/nginx/sites-enabled/default

sudo ln -nfs "/home/deploy/apps/app/current/config/nginx.conf" "/etc/nginx/sites-enabled/app"

sudo service nginx restart
```


At the end, run

```
cap production deploy
```

####How to install let's encrypt [#1](https://github.com/xargr/rails_deploy/issues/1)

if you have some problem, create new issue in this repo

##Happy coding...




