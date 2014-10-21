VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.define "ubuntu" do |config|
    config.vm.box = "trustyx64"
    config.vm.box_url = "http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_ubuntu-14.04_chef-provisionerless.box"
    config.vm.hostname = "docker"
    config.vm.provider :virtualbox do |vb|
      vb.memory = 4096
      vb.cpus = 2
    end
    config.vm.synced_folder ".", "/opt/go/src/github.com/docker/libcontainer"
    config.vm.provision "shell", inline: <<-EOF
    set -e

    # System packages
    echo "Installing Base Packages"
    export DEBIAN_FRONTEND=noninteractive
    sudo apt-get update -qq
    sudo apt-get install -qqy --force-yes build-essential git curl cgroup-bin

    # Install Go
    GOVERSION="1.3"
    GOTARBALL="go${GOVERSION}.linux-amd64.tar.gz"
    export GOROOT=/usr/local/go
    export GOPATH=/opt/go:/opt/go/src/github.com/docker/libcontainer/vendor
    export PATH=$PATH:$GOROOT/bin:$GOPATH/bin

    echo "Installing Go $GOVERSION"
    if [ ! $(which go) ]; then
        echo "    Downloading $GOTARBALL"
        wget --quiet --directory-prefix=/tmp http://golang.org/dl/$GOTARBALL

        echo "    Extracting $GOTARBALL to $GOROOT"
        sudo tar -C /usr/local -xzf /tmp/$GOTARBALL

        echo "    Configuring GOPATH"
        sudo mkdir -p $GOPATH/src $GOPATH/bin $GOPATH/pkg
        sudo chown -R vagrant $GOPATH

        echo "    Configuring env vars"
        echo "export PATH=\$PATH:$GOROOT/bin:$GOPATH/bin" | sudo tee /etc/profile.d/golang.sh > /dev/null
        echo "export GOROOT=$GOROOT" | sudo tee --append /etc/profile.d/golang.sh > /dev/null
        echo "export GOPATH=$GOPATH" | sudo tee --append /etc/profile.d/golang.sh > /dev/null
    fi
    # Auto cd to libcontainer dir
    echo "cd /opt/go/src/github.com/docker/libcontainer" >> /home/vagrant/.bashrc

    # Cleanup
    sudo apt-get autoremove

  EOF
  end
end
