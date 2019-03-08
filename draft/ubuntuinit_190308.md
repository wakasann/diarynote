install software

1. vim 

```
sudo apt-get install -y vim
```

1. [Typora](https://www.typora.io/)

 command copy from Typora website

```
# or run:
# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE

wget -qO - https://typora.io/linux/public-key.asc | sudo apt-key add -

# add Typora's repository

sudo add-apt-repository 'deb https://typora.io/linux ./'

sudo apt-get update

# install typora

sudo apt-get install typora
```

2. [Xampp](https://www.apachefriends.org/index.html)

download for linux run file at web browser, then go to download path at Terminal

```
chmod +x ./xampp*.run
sudo ./xampp*.run
sudo chmod 777 /opt/lampp/htdocs

vim ~/.profile
```

add following content at ~/.profile at end line

```
export XAMPP_HOME=/opt/lampp
export PATH=${XAMPP_HOME}/bin:$PATH
```

```
source ~/.profile
```

3. install [composer](https://getcomposer.org/download/)

at composer download page,last version is 1.8.4

[Packagist 镜像使用方法](https://pkg.phpcomposer.com/#tip1)

```
wget https://getcomposer.org/download/1.8.4/composer.phar
chmod +x composer.phar
sudo mv composer.phar /usr/local/bin/composer
# config composer repo.packagist
composer config -g repo.packagist composer https://packagist.phpcomposer.com
```

3. install ibus sunyinpin

```
sudo apt-get update
sudo apt-get install ibus-pinyin ibus-sunpinyin
```

4. install nodejs [Ubuntu安装最新版nodejs](https://www.cnblogs.com/flying_bat/p/8671816.html)

```
sudo apt-get install -y nodejs npm
sudo npm config set registry https://registry.npm.taobao.org
sudo npm install n -g
sudo n stable
```

5. redis `sudo apt-get install redis-server`
6. [git push错误failed to push some refs to的解决](https://www.cnblogs.com/henry2018/p/9567167.html)
7. ubuntu 开机之后，会进入 initramfs界面

```
fsck /dev/sda1 -y
```

`/dev/sda1` 以启动时，提示错误的卷称为主

8. [Mcrypt响应慢的一个原因](http://www.laruence.com/2012/09/24/2810.html)

```
'mcrypt_create_iv(): Cannot open source device'
```

