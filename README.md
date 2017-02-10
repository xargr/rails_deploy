# All package and actions to deploy rails app
=============================================
# Estimated time ~40 minutes
-----------------------------

* [Rails 5](http://rubyonrails.org/){:target="_blank"}
* [Mysql](https://www.mysql.com/)
* [Puma](http://puma.io/)
* [Ubuntu 16.04](https://www.ubuntu.com/)
* [Capistrano()](http://capistranorb.com/)


### The tutorial work with server by [digitalocean.com](https://m.do.co/c/03deef324558)
##### Login and create a droplet Ubuntu 16.04x64 1gb cpu & 30gb disk

```
ssh root@xxx.xxx.xx

sudo apt-get install aptitude

sudo apt-get update

sudo aptitude update

sudo aptitude safe-upgrade
```
# If error with locale

sudo locale-gen el_GR.UTF-8

#################

ssh-copy-id root@xxx.xxx.xx

sudo adduser deploy

sudo adduser deploy sudo

ssh-copy-id deploy@xxx.xxx.xx

sudo vim /etc/ssh/sshd_config

####
#change to no
PermitRootLogin no

#change port 22 to anything
port 3423
######


sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties libffi-dev nodejs


sudo apt-get install curl git-core nginx -y

cd
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
exec $SHELL


git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
exec $SHELL


rbenv install -l

rbenv install 2.4.0

rbenv global 2.4.0

rbenv rehash

ruby -v

gem install bundler

rbenv rehash

gem install rails -V --no-ri --no-rdoc

rbenv rehash

ssh-keygen -t rsa

cat ~/.ssh/id_rsa.pub

ssh -T git@github.com

sudo aptitude update

sudo apt-get install mysql-server mysql-client libmysqlclient-dev

sudo mysql_install_db

sudo mysql_secure_installation

######


group :development do
    gem 'capistrano',         require: false
    gem 'capistrano-rvm',     require: false
    gem 'capistrano-rails',   require: false
    gem 'capistrano-bundler', require: false
    gem 'capistrano3-puma',   require: false
end

gem 'puma'

######


bundle

rbenv rehash

cap install





#####config/deploy.rb

# Change these
server 'your_server_ip', port: your_port_num, roles: [:web, :app, :db], primary: true

set :repo_url,        'git@example.com:username/appname.git'
set :application,     'appname'
set :user,            'deploy'
set :puma_threads,    [4, 16]
set :puma_workers,    0

# Don't change these unless you know what you're doing
set :pty,             true
set :use_sudo,        false
set :stage,           :production
set :deploy_via,      :remote_cache
set :deploy_to,       "/home/#{fetch(:user)}/apps/#{fetch(:application)}"
set :puma_bind,       "unix://#{shared_path}/tmp/sockets/#{fetch(:application)}-puma.sock"
set :puma_state,      "#{shared_path}/tmp/pids/puma.state"
set :puma_pid,        "#{shared_path}/tmp/pids/puma.pid"
set :puma_access_log, "#{release_path}/log/puma.error.log"
set :puma_error_log,  "#{release_path}/log/puma.access.log"
set :ssh_options,     { forward_agent: true, user: fetch(:user), keys: %w(~/.ssh/id_rsa.pub) }
set :puma_preload_app, true
set :puma_worker_timeout, nil
set :puma_init_active_record, true  # Change to false when not using ActiveRecord

## Defaults:
# set :scm,           :git
# set :branch,        :master
# set :format,        :pretty
# set :log_level,     :debug
# set :keep_releases, 5

## Linked Files & Directories (Default None):
# set :linked_files, %w{config/database.yml}
# set :linked_dirs,  %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system}

namespace :puma do
  desc 'Create Directories for Puma Pids and Socket'
  task :make_dirs do
    on roles(:app) do
      execute "mkdir #{shared_path}/tmp/sockets -p"
      execute "mkdir #{shared_path}/tmp/pids -p"
    end
  end

  before :start, :make_dirs
end

namespace :deploy do
  desc "Make sure local git is in sync with remote."
  task :check_revision do
    on roles(:app) do
      unless `git rev-parse HEAD` == `git rev-parse origin/master`
        puts "WARNING: HEAD is not the same as origin/master"
        puts "Run `git push` to sync changes."
        exit
      end
    end
  end

  desc 'Initial Deploy'
  task :initial do
    on roles(:app) do
      before 'deploy:restart', 'puma:start'
      invoke 'deploy'
    end
  end

  desc 'Restart application'
  task :restart do
    on roles(:app), in: :sequence, wait: 5 do
      invoke 'puma:restart'
    end
  end

  before :starting,     :check_revision
  after  :finishing,    :compile_assets
  after  :finishing,    :cleanup
  after  :finishing,    :restart
end

# ps aux | grep puma    # Get puma pid
# kill -s SIGUSR2 pid   # Restart puma
# kill -s SIGTERM pid   # Stop puma


############



git add -A
git commit -m "Set up Puma, Nginx & Capistrano"
git push origin master



## make /home/deploy/apps/app/shared/config/database.yml
production:
  adapter: mysql2
  pool: 5
  timeout: 5000
  encoding: utf8
  database: app
  username: root
  password: 6936822140

###make /home/deploy/apps/app/shared/config/secrets.yml
production:
  secret_key_base: <%= ENV["SECRET_KEY_BASE"] %>

rails secret
sudo nano /etc/environment
#### and add
export SECRET_KEY_BASE=abb2e9e094bb8fb1aa216defed455caa0f9


RAILS_ENV=production bundle exec rake db:create


cap production deploy:initial


###set nginx virtualhost
sudo rm /etc/nginx/sites-enabled/default
sudo ln -nfs "/home/deploy/apps/appname/current/config/nginx.conf" "/etc/nginx/sites-enabled/appname"




