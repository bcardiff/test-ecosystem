Vagrant.configure("2") do |config|
  [%w(debian debian/jessie64), %w(xenial ubuntu/xenial64), %w(xenial32 ubuntu/xenial32), %w(bionic ubuntu/bionic64)].each do |name, box|
    config.vm.define(name) do |c|
      c.vm.box = box

      c.vm.synced_folder ".", "/vagrant"

      c.vm.provision :shell, inline: %(
        set -e
        export DEBIAN_FRONTEND=noninteractive
        apt-get update

        # crystal deps
        apt-get update \
         && apt-get install -y git build-essential pkg-config software-properties-common curl \
            libpcre3-dev libevent-dev \
            libxml2-dev libyaml-dev libssl-dev zlib1g-dev libsqlite3-dev libgmp-dev \
            libedit-dev libreadline-dev gdb postgresql-client

        add-apt-repository "deb http://apt.llvm.org/$(lsb_release -cs)/ llvm-toolchain-$(lsb_release -cs)-4.0 main" \
         && curl -sSL https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add - \
         && apt-get update \
         && apt-get install -y llvm-4.0 libclang-4.0-dev

        # wget https://apt.llvm.org/llvm.sh
        # chmod +x llvm.sh
        # ./llvm.sh 10
        # apt-get install -y libclang-10-dev
        # ln -sf /usr/bin/ld.lld-10 /usr/bin/ld.lld
        # export LLVM_CONFIG=/usr/bin/llvm-config-10

        # bats
        cd /vagrant
        /vagrant/scripts/00-install-bats.sh
      )
    end
  end

  [%w(fedora bento/fedora-30)].each do |name, box|
    config.vm.define(name) do |c|
      c.vm.box = box

      c.vm.synced_folder ".", "/vagrant"

      c.vm.provision :shell, inline: %(
        set -e

        dnf -y groupinstall "C Development Tools and Libraries"
        dnf -y install git
        dnf -y install gmp-devel libbsd-devel libedit-devel libevent-devel libxml2-devel \
                       libyaml-devel openssl-devel readline-devel redhat-rpm-config
        dnf -y install sqlite-devel postgresql
        dnf -y install llvm

        # bats
        cd /vagrant
        /vagrant/scripts/00-install-bats.sh
      )
    end
  end

  config.vm.provider "virtualbox" do |vb|
    vb.memory = 4096
    vb.cpus = 2
  end
end
