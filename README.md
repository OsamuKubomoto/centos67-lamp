# README

Vagrant�ŗ����グ��2���VM�ŁAansible���g���ăv���r�W���j���O����playbook�����܂����B

## �J����

VirtualBox5.0.14
Vagrant1.8.1

## VM�̍\��

 * WEB�T�[�o�iweb�j192.168.33.10
 * ansible�T�[�o�iansible�j 192.168.33.20

### OS�ƃ~�h���E�F�A�̃o�[�W���� ��2016-02-04���_

 * centos 6.7
 * Apache 2.2.15
 * PHP 5.4.45
 * mysql 5.5.47
 * nodejs v0.10.36
 * npm 1.3.6
 * bower 1.7.7
 * Composer 1.0-dev

## ����

/provision/group_vars/all��K�X�C�����Ă��������B

## �\�z�菇

�v���W�F�N�g�f�B���N�g�����쐬���ړ�

    $ mkdir example-project && cd example-project

Vagrantfile���쐬

    $ vi Vagrantfile
    
    Vagrant.configure(2) do |config|
      config.vm.define :web do | web |
        web.vm.box = "bento/centos-6.7"
        web.vm.hostname = "web"
        web.vm.network "private_network", ip: "192.168.33.10"
        web.vm.network :private_network, ip: "192.168.0.1", virtualbox__intnet: "intnet"
        web.vm.synced_folder ".", "/vagrant", owner: "vagrant", group: "vagrant", mount_options: ['dmode=777','fmode=755']
        web.vm.provider :virtualbox do |v|
          v.customize ['modifyvm', :id, '--paravirtprovider', 'kvm']
        end
      end
    
      config.vm.define :ansible do | ansible |
        ansible.vm.box = "bento/centos-6.7"
        ansible.vm.hostname = "ansible"
        ansible.vm.network "private_network", ip: "192.168.33.20"
        ansible.vm.network :private_network, ip: "192.168.0.2", virtualbox__intnet: "intnet"
        ansible.vm.synced_folder ".", "/vagrant", owner: "vagrant", group: "vagrant", mount_options: ['dmode=777','fmode=755']
        ansible.vm.provider :virtualbox do |v|
          v.customize ['modifyvm', :id, '--paravirtprovider', 'kvm']
        end
      end
    end

git clone���Ă���

    $ git clone https://github.com/OsamuKubomoto/centos67-lamp.git provision

���z�}�V���N��

    $ vagrant up
    $ vagrant reload

ansible�T�[�o�Ƀ��O�C��

    $ vagrant ssh ansible

ansible���C���X�g�[��

    $ sudo yum -y install epel-release
    $ sudo yum -y install ansible

ssh���쐬

    $ ssh-keygen -t rsa
    ������͑S���G���^�[��OK

�閧���Őڑ��ł���悤�ɂ���

    $ ssh-copy-id vagrant@192.168.0.1
    Are you sure you want to continue connecting (yes/no)? yes
    vagrant@192.168.0.1's password: vagrant

�a�ʊm�F

    $ ansible -i provision/hosts web -m ping
    
    # ����Ȋ����ŕ\��������OK
    192.168.0.1 | success >> {
        "changed": false,
        "ping": "pong"
    }

provision���s

    $ ansible-playbook -i provision/hosts provision/site.yml

ansible�T�[�o���甲����

    $ exit

�e�o�[�W�������m�F

    $ vagrant ssh web -c "httpd -v && php -v && mysql --version && node -v && npm -v && bower --version && composer --version"
    
    # ����Ȋ����ŕ\��������OK
    Server version: Apache/2.2.15 (Unix)
    Server built:   Dec 15 2015 15:50:14
    PHP 5.4.45 (cli) (built: Jan  6 2016 17:00:57)
    Copyright (c) 1997-2014 The PHP Group
    Zend Engine v2.4.0, Copyright (c) 1998-2014 Zend Technologies
    mysql  Ver 14.14 Distrib 5.5.47, for Linux (x86_64) using readline 5.1
    v0.10.36
    1.3.6
    1.7.7
    Composer version 1.0-dev (7117a5775ffdcdfd31bbd52a138a6f9c65e7e3c2) 2016-02-03 17:02:06

�u���E�U�Ŋm�F

    http://192.168.33.10
    https://192.168.33.10
    https://192.168.33.10/phpmyadmin/
