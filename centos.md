centos.md

# yum install kernel-devel安装失败

原因:yum源失效

```
yum clean all
yum makecache
yum update
```
[yum install kernel-devel安装失败](https://blog.csdn.net/weixin_43858015/article/details/106199661)

# vagrant 虚拟多台机器

```
Vagrant.configure("2") do |config|
  	config.vm.define "k8smaster" do |vb|
		config.vm.provider "virtualbox" do |v|
		  v.memory=4098
		  v.cpus=2
		end
      vb.vm.host_name="k8smaster"
	  vb.vm.network "public_network",ip: "192.168.1.120"
	  vb.vm.box = "centos7"
	  vb.disksize.size = '100GB'
	  vb.vm.provision "shell", inline: <<-SHELL
		sudo su
		yum clean all
        yum makecache
        yum update -y
      SHELL
	end
	
	config.vm.define "k8snode1" do |vb|
		config.vm.provider "virtualbox" do |v|
		  v.memory=4098
		  v.cpus=2
		end
      vb.vm.host_name="k8snode1"
	  vb.vm.network "public_network",ip: "192.168.1.121"
	  vb.vm.box = "centos7"
	  vb.disksize.size = '100GB'
	  vb.vm.provision "shell", inline: <<-SHELL
		sudo su
		yum clean all
        yum makecache
        yum update -y
      SHELL
	end
	
	config.vm.define "k8snode2" do |vb|
		config.vm.provider "virtualbox" do |v|
		  v.memory=4098
		  v.cpus=2
		end
      vb.vm.host_name="k8snode2"
	  vb.vm.network "public_network",ip: "192.168.1.122"
	  vb.vm.box = "centos7"
	  vb.disksize.size = '100GB'
	  vb.vm.provision "shell", inline: <<-SHELL
		sudo su
		yum clean all
        yum makecache
        yum update -y
      SHELL
	end
end
```

各台vm的详细SSH配置信息可以用如下命令进行查看

```
vagrant ssh-config
```

如果是查看单机的SSH配置信息，例如 hostname 名为 k8smaster 的机器的SSH配置信息，=，可以使用如下命令进行查看:

```
vagrant ssh k8smaster
```

因为 centos 不能启动成功，会报上面的错误，所以需要重复三次,可以把第3个VM虚拟机器启动完成

```
vagrant halt
vagrant up
vagrant provision
```