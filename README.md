# mytube
a video sharing system base on docker and clipbucket

 The video sharing system based on docker and clipbucket is actually installing clipbucket on docker and doing localization and modification. 

Current version v1.0, see the features and shortcomings here v1.0

Here is a brief description of how to use, please read my blog post for detailed usage. 

[Docker-based open source video sharing system solution](https://medium.com/@MMParvin)

#  Clipbucket overview 

This video system belongs to the LAMP technology stack product, mainly the CMS system built by PHP code, and uses PHP-CLI to execute the bash command to call the transcoding software. The software used for transcoding has


*  Ffmpeg, the famous audio and video software, various formats of codec, transcoding, recording, editing and so on. 
*  Flvtool2, because ffmpeg does not support video support in flv format, so this product is needed. 
*  Imagemagick, look at the name to know that this is for processing images, in this system we use it to get video screenshots. 
* MP4Box (gpac), when transcoding, ffmpeg first decodes the video, and then encodes it into H264 video stream and AAC audio stream (the specific format is adjustable). HTML5 supports MP4 format video playback by default, so you need to pack audio and video streams into MP4 files. This software does this.

#  When Clipbucket meets Docker 

 Docker's detailed installation tutorial in the blog I mentioned earlier, please read my blog if you have not used it. 
 
##  Download image 
 The image size is 1.3G

Domestic download point 

```bash
# Daocloud Download Point
docker pull daocloud.io/xuping/clipbucket:v1.0

# 灵雀云下载点
sudo docker pull index.alauda.cn/xuping/clipbucket:v1.0
```
 Foreign download point 

```bash
# docker hub Download point
sudo docker pull xuping/clipbucket:v1.0

```
 View the downloaded image 

```bash
docker images
```

##  Mirror related information 

 Installed software: In addition to the previously listed video transcoding related software, there are some plugins for apache2, MariaDB, php5, php5 
 
* Root password: 123
* Mysql root password: 123, turn off root remote login
* The database used by clipbucket: clipbucket
* Clipbucket administrator account: admin: 123
* Apache default directory: /var/www/html
* The php.ini configuration is as follows:

```bash
upload_max_filesize = 1024M
max_execution_time = 7300
max_input_time = 3000
memory_limit = 512M
magic_quotes_gpc = on
magic_quotes_runtime = off
post_max_size = 1024M
output_buffering = off
display_errors = on
```
##  Running mirror 

 View the downloaded image 

```bash
sudo docker images
```
 Running mirror 

```bash
docker run --restart=always -d -p 8081:80 -p 2222:22 -v /data:/data xuping/clipbucket:v1.0  /usr/bin/supervisord 
```
If it is downloaded from a domestic site, add the domain name to the image name, for example```daocloud.io/xuping/clipbucket:v1.0```

 The meaning of the parameters below is explained below. 

* --restart=always Make the container run automatically with docker startup
*  -d Run the container in Detached mode 
* -p 8081:80 -p 2222:22 port mapping, mapping port 22 of the container to host 2222 port, port 80 mapping to port 8081
* -v /data:/data Mount the /data directory of the host to the /data directory in the container
* Xuping/clipbucket:v1.0 is the name of my image
* /usr/bin/supervisord The program that runs when the container starts. This is a daemon that ensures that the software that is started is running, which is due to another feature of docker.

##  Store the data file in the host 

As mentioned earlier, to persist data, it is best to have the data on the host. The following are actually available through the Dockerfile or in a startup script, but this version has not yet been implemented, and subsequent versions will be updated via the Dockerfile.


```bash
#  Because the /data directory of the host has been mounted to the /data directory of the container,
# So you can think that the current /data directory is the host's /data directory

#  database file transfer
cp -r /var/lib/mysql/clipbucket   /data
rm -r /var/lib/mysql/clipbucket/
ln -s /data/clipbucket/ /var/lib/mysql

# 视频文件转移
cp -r /var/www/html/files /data
rm -r /var/www/html/files
ln -s /data/files/ /var/www/html/
ls -l /var/www/html/files
```
##  change Password 

 For convenience, the default password is relatively simple. You must change the password after you deploy the image. You need to change the system root password and the mysql root password. 
 
 
```bash
# Modify the system root password
passwd root

# Modify mysql root password
# login mysql, default password 123
mysql -uroot -p

mysql> use mysql;
mysql> update user set password=PASSWORD("你的密码") where User='root';
mysql> flush privileges;

```
 Modify the mysql password in the clipbucket configuration file 
 
 
```bash
vi /var/www/html/includes/dbconnect.php

# Modify $DBPASS = '123'; 123 is your password。
```

#  Read more about the blog. 
[Docker-based open source video sharing system solution](https://medium.com/@MMParvin)
