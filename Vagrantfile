# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/trusty32"

  config.vm.define 'dev', :primary => true do |dev|
    dev.vm.provider :virtualbox do |vb|
      vb.memory = 1024
      vb.customize ["modifyvm", :id, "--cpuexecutioncap", "50"]
      vb.customize ["modifyvm", :id, "--hwvirtex", "off"]
      vb.cpus = 1
    end

    dev.vm.network 'forwarded_port', :guest => 3000, :host => 3000
    dev.vm.network 'forwarded_port', :guest => 27017, :host => 27017
    dev.vm.network 'forwarded_port', :guest => 28017, :host => 28017

    dev.vm.provision :shell, privileged: true, inline: <<-SCRIPT
      add-apt-repository --yes ppa:brightbox/ruby-ng-experimental
      apt-get update

      apt-get install --no-install-recommends -y build-essential git libxml2-dev \
        libxslt1-dev imagemagick zlib1g-dev \
        ruby2.1 ruby2.1-dev curl wget imagemagick libmagickcore-dev libmagickwand-dev

      apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
      echo "deb http://repo.mongodb.org/apt/ubuntu "$(lsb_release -sc)"/mongodb-org/3.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.0.list
      apt-get update
      apt-get install -y mongodb-org

      # ping a particular version of mongodb
      echo "mongodb-org hold" | sudo dpkg --set-selections
      echo "mongodb-org-server hold" | sudo dpkg --set-selections
      echo "mongodb-org-shell hold" | sudo dpkg --set-selections
      echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
      echo "mongodb-org-tools hold" | sudo dpkg --set-selections

      # start mongodb service
      service mongod start

      cd /vagrant
      gem install bundler
      bundle install --jobs 2
      bundle exec rake db:setup
    SCRIPT
  end
end
