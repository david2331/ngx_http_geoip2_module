# ngx_http_geoip2_module


sudo add-apt-repository ppa:maxmind/ppa && \
sudo apt update && sudo apt upgrade -y && \
sudo apt install build-essential dh-autoreconf unzip wget curl libmaxminddb0 libmaxminddb-dev mmdb-bin checkinstall -y && \
sudo mkdir ~/nginx-dev && cd ~/nginx-dev && \
sudo wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.42.tar.gz && \
sudo tar -zxf pcre-8.42.tar.gz && \
      cd pcre-8.42 && \
sudo ./configure && \
sudo make && \
sudo make install && \
      cd .. && \
sudo wget http://zlib.net/zlib-1.2.11.tar.gz && \
sudo tar -zxf zlib-1.2.11.tar.gz && \
      cd zlib-1.2.11 && \
sudo ./configure && \
sudo make && \
sudo make install && \
      cd .. && \
sudo wget https://www.openssl.org/source/openssl-1.0.2o.tar.gz && sudo tar xzvf openssl-1.0.2o.tar.gz && \
cd openssl-1.0.2o && \
sudo ./config --prefix=/usr && \
sudo make && \
sudo make install && \
cd .. && \

sudo rm *.gz && \

sudo wget https://github.com/leev/ngx_http_geoip2_module/archive/master.zip && \
sudo unzip master.zip && \
sudo mv ngx_http_geoip2_module-master ngx_http_geoip2_module && \
sudo rm master.zip && \

cd ~/nginx-dev && \
sudo wget https://nginx.org/download/nginx-1.15.0.tar.gz && \
sudo tar zxf nginx-1.15.0.tar.gz && \
cd nginx-1.15.0 && \

sudo ./configure \
--prefix=/usr/share/nginx \
--sbin-path=/usr/sbin/nginx \
--modules-path=/usr/lib/nginx/modules \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--pid-path=/var/run/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--user=www-data \
--group=www-data \
--build=Ubuntu \
--http-client-body-temp-path=/var/lib/nginx/body \
--http-fastcgi-temp-path=/var/lib/nginx/fastcgi \
--http-proxy-temp-path=/var/lib/nginx/proxy \
--http-scgi-temp-path=/var/lib/nginx/scgi \
--http-uwsgi-temp-path=/var/lib/nginx/uwsgi \
--with-openssl=../openssl-1.0.2o \
--with-openssl-opt=enable-ec_nistp_64_gcc_128 \
--with-openssl-opt=no-nextprotoneg \
--with-openssl-opt=no-weak-ssl-ciphers \
--with-openssl-opt=no-ssl3 \
--with-pcre=../pcre-8.42 \
--with-pcre-jit \
--with-zlib=../zlib-1.2.11 \
--with-compat \
--with-file-aio \
--with-threads \
--with-http_addition_module \
--with-http_auth_request_module \
--with-http_dav_module \
--with-http_flv_module \
--with-http_gunzip_module \
--with-http_gzip_static_module \
--with-http_mp4_module \
--with-http_random_index_module \
--with-http_realip_module \
--with-http_slice_module \
--with-http_ssl_module \
--with-http_sub_module \
--with-http_stub_status_module \
--with-http_v2_module \
--with-http_secure_link_module \
--with-mail \
--with-mail_ssl_module \
--with-stream \
--with-stream_realip_module \
--with-stream_ssl_module \
--with-stream_ssl_preread_module \
--with-debug \
--add-module=../ngx_http_geoip2_module \
--with-cc-opt='-g -O2 -fstack-protector --param=ssp-buffer-size=4 -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2' \
--with-ld-opt='-Wl,-z,relro -Wl,--as-needed' && \

sudo make && \
sudo make install && \
sudo mkdir -p /var/lib/nginx && \
sudo ldconfig && \

sudo echo "
[Unit]
Description=A high performance web server and a reverse proxy server
After=network.target

[Service]
Type=forking
PIDFile=/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t -q -g 'daemon on; master_process on;'
ExecStart=/usr/sbin/nginx -g 'daemon on; master_process on;'
ExecReload=/usr/sbin/nginx -g 'daemon on; master_process on;' -s reload
ExecStop=-/sbin/start-stop-daemon --quiet --stop --retry QUIT/5 --pidfile /run/nginx.pid
TimeoutStopSec=5
KillMode=mixed

[Install]
WantedBy=multi-user.target " > ~/nginx.service && \

sudo mv ~/nginx.service /etc/systemd/system/nginx.service && \

sudo systemctl start nginx.service && sudo systemctl enable nginx.service && \

sudo mkdir /etc/nginx/geoip && cd /etc/nginx/geoip && \

sudo wget http://geolite.maxmind.com/download/geoip/database/GeoLite2-Country.mmdb.gz && \

sudo gunzip GeoLite2-Country.mmdb.gz
