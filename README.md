Multistream
-----------------



Used to allow the LinusTechTips team to stream to both YouTube and Twitch at the same time :D

(ps. it's not really designed for randoms to be able to setup, but if you somewhat know your way around Linux you should be able to, no issue.)


Stats page: http://10.20.0.70:8080/stats

Tutorial
-------------------

Designed for ubunutu 18.04

This assumes you are working in the /home/linus directory. If otherwise you will need to change the config in step 9 to reflect that, otherwise the stats page will not work. This also assumes you are using a non-root user named "linus". Please use a non-root user. :P

pre-setup: Set your network card's IP - we are using 10.20.0.70 as the static IP for the streaming server. Note, there are a LOT of places that the IP will need to be changed inside nginx.conf to get this working with a different IP address.



1. **Install dependencies**
	1. `sudo apt install build-essential libpcre3 libpcre3-dev libssl-dev zlib1g-dev ffmpeg -y`
1. **Make and enter "build" directory**
	1. `mkdir build && cd build`


1. **Download newest NGINX "stable" release (right click, copy, then wget from here: http://nginx.org/en/download.html)**
	1. `wget http://nginx.org/download/nginx-1.14.0.tar.gz`


1. **clone the NGINX RTMP module from github**
	1. `git clone git://github.com/arut/nginx-rtmp-module.git`


1. **Extract the NGINX tarball, and then enter the directory**
	1. `tar xzf nginx-1.14.0.tar.gz && cd nginx-1.14.0`


1. **Build NGINX w/ the RTMP module**
	1. `sudo ./configure --with-http_ssl_module --add-module=../nginx-rtmp-module`
	1. `sudo make`
	1. `sudo make install`


1. **Start NGINX**
	1. `sudo /usr/local/nginx/sbin/nginx`


1. **Set NGINX to start on boot**
	1. `sudo bash -c "curl https://raw.githubusercontent.com/tivyhosting/multistream/master/nginx > /etc/init.d/nginx"`
	1. `sudo chmod +x /etc/init.d/nginx`
	1. `sudo update-rc.d -f nginx defaults`
	1. `sudo systemctl enable nginx`


1. **Configure NGINX w/ RTMP config (you will need to edit streamkeys/settings probably)**
	1. `sudo rm -rf /usr/local/nginx/conf/nginx.conf`
	1. `sudo bash -c "curl https://raw.githubusercontent.com/tivyhosting/multistream/master/nginx.conf > /usr/local/nginx/conf/nginx.conf"`


1. **Set your desired stream keys in the nginx.conf config file (the defaults are empty)**
	1. Replace "TWITCH_STREAM_KEY" with your twitch stream key, and "YOUTUBE_STREAM_KEY" with your youtube stream key.
		1. `sudo nano /usr/local/nginx/conf/nginx.conf`
	

1. **Also set your desired storage location for the live recording off the input stream** (please note this directory MUST be set to 777 permissions as NGINX uses a no-perm user to run it's workers)
	1. Replace "/home/linus/recordings" with your desired recording location
		1. `sudo nano /usr/local/nginx/conf/nginx.conf`


1. **Restart NGINX**
	1. `sudo service nginx restart`


1.  **You should now be able to stream to the NGINX server. We have 3 distinct ingest servers for the 3 different intended streaming setups.**
	1. Streaming to both YouTube and Twitch: `rtmp://10.20.0.70:1935/transcode-yt-twitch`
	1. Streaming to just YouTube: `rtmp://10.20.0.70:1935/transcode-yt`
	1. (Streaming to both YouTube and Floatplane: `rtmp://10.20.0.70:1935/transcode-yt-fp` (NOT YET WORKING))
