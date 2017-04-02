# How to install Sympa from source on Debian Jessie

## Brace yourself

Create user:

```
sudo adduser --disabled-login --gecos Sympa sympa
```

Install toolchain:

```
sudo apt-get install automake gettext
```

Install MySQL without password input during installation (password is set to 'a'):

```
debconf-set-selections <<< 'mysql-server mysql-server/root_password password a'
debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password a'
apt-get -y install mysql-server
```

Create the database:

```
echo "CREATE DATABASE sympa CHARACTER SET utf8;" | mysql -u root -pa
echo "GRANT ALL PRIVILEGES ON sympa.* TO sympa@localhost IDENTIFIED BY 'sympa';" | mysql -u root -pa
```

Install dependencies:

```
sudo apt-get update
sudo apt-get install -y \
    build-essential mhonarc xml-twig-tools \
    libarchive-zip-perl libauthcas-perl libcgi-fast-perl libcgi-pm-perl \
    libclass-singleton-perl libclone-perl libcrypt-ciphersaber-perl \
    libcrypt-openssl-x509-perl libcrypt-smime-perl libdata-password-perl \
    libdatetime-format-mail-perl libdatetime-timezone-perl libdbd-csv-perl \
    libdbi-perl libemail-simple-perl libencode-perl \
    libfile-copy-recursive-perl libfile-nfslock-perl libhtml-format-perl \
    libhtml-stripscripts-parser-perl libhtml-tree-perl libintl-perl \
    libio-stringy-perl libmail-dkim-perl libmailtools-perl \
    libmime-charset-perl libmime-encwords-perl libmime-lite-html-perl \
    libmime-tools-perl libmsgcat-perl libnet-cidr-perl libnet-dns-perl \
    libnet-ldap-perl libproc-processtable-perl libregexp-common-perl \
    libscalar-list-utils-perl libsoap-lite-perl libssl-dev libtemplate-perl \
    libterm-progressbar-perl libunicode-linebreak-perl liburi-perl \
    libwww-perl libxml2-dev libxml-libxml-perl libxml-perl
```

Prepare `/etc/mail`:

```
sudo mkdir /etc/mail/
```

Postfix configuration:

```
sudo apt-get install postfix

cat <<EOF | sudo tee -a /etc/aliases
sympa: "| /home/sympa/bin/queue sympa@localhost.org"
listmaster: "| /home/sympa/bin/queue listmaster@localhost.org"
bounce+*: "| /home/sympa/bin/bouncequeue sympa@localhost.org"
abuse-feedback-report: "| /home/sympa/bin/bouncequeue sympa@localhost.org"
sympa-request: postmaster
sympa-owner: postmaster
EOF
sudo newaliases

sudo sed -e 's@alias_maps\s*=\s*\(.*\)@alias_maps = \1,hash:/etc/mail/sympa_aliases@'\
         -e 's@alias_database\s*=\s*\(.*\)@alias_database = \1,hash:/etc/mail/sympa_aliases@'\
         -i /etc/postfix/main.cf

sudo systemctl restart posfix
```

## Let's the show begin

Clone and make:

```
cd /tmp/
git clone git@github.com:sympa-community/sympa
cd sympa
autoreconf
automake --add-missing
autoreconf
./configure --enable-fhs --without-initdir --with-unitsdir=/etc/systemd/system --disable-smtpc
make
sudo make install
```

Hack `sympa_wizard.pl` to make it non-interactive:

```
sudo sed -e 's@my \$db_type;@my $db_type = \'mysql\';@'\
         -e 's@my \$rep = <STDIN>;@#my $rep = <STDIN>;@'\
         -e 's@foreach my \$mod (sort keys %\$cpan_modules) {@foreach my $mod (sort keys %$cpan_modules) {\n        next if ($mod eq "DBD::Oracle" || $mod eq "DBD::Sybase" || $mod eq "DBD::Pg" || $mod eq "DBD::ODBC" || $mod eq "DBD::SQLite");@'\
         -i /home/sympa/sbin/sympa_wizard.pl
```

Check the dependencies:

```
sudo /home/sympa/sbin/sympa_wizard.pl --check
```

Create the configuration file with the wizard (use a real mail address for listmaster):

```
sudo /home/sympa/sbin/sympa_wizard.pl --batch email=sympa listmaster=a@example.org db_type=mysql db_name=sympa db_host=localhost db_user=sympa db_passwd=sympa domain=example.org wwsympa_url=http://sympa.example.org/sympa
```

Enable the services:

```
sudo systemctl daemon-reload
sudo systemctl enable sympa.service
sudo systemctl start sympa.service
```

## To infinity and beyond

Let's make Sympa accessible through the web.

Install the tools:

```
sudo apt-get -y install nginx spawn-fcgi
sudo cat <<EOF > /etc/systemd/system/sympa-fcgi.service
[Unit]
Description=Sympa fcgi
Documentation=man:spawn-fcgi

[Service]
ExecStart=/usr/bin/spawn-fcgi -n -u sympa -g sympa -a 127.0.0.1 -p 8442 /home/sympa/lib/sympa/cgi/wwsympa.fcgi

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable sympa-fcgi.service
sudo systemctl start sympa-fcgi.service
```

And configure Nginx:

```
sudo cat << EOF > /etc/nginx/sites-available/default
server {
    listen       80;
    server_name  sympa.localhost.org;
    root         /usr/lib/cgi-bin/sympa;
    access_log   /var/log/nginx/sympa.localhost.org;
    error_log    /var/log/nginx/sympa.localhost.org_error.log;
    error_page   403 500 502 503 504 /50x.html;

    rewrite ^/\$ http://sympa.example.org/sympa/ permanent;

    location /static-sympa {
        alias /home/sympa/var/lib/sympa/static_content/;
        access_log off;
    }

    location /50x.html {
        root /usr/share/nginx/html;
    }

    location ~* \.(php|pl|py|jsp|asp|sh|cgi|bin|csh|ksh|out|run|o)$ {
        deny all;
    }

    location ~ /\.ht {
        deny all;
    }

    location / {
                gzip off;
                fastcgi_pass   127.0.0.1:8442;
                fastcgi_split_path_info ^(/sympa)(.+)$;
                fastcgi_param  QUERY_STRING       \$query_string;
                fastcgi_param  REQUEST_METHOD     \$request_method;
                fastcgi_param  CONTENT_TYPE       \$content_type;
                fastcgi_param  CONTENT_LENGTH     \$content_length;
                fastcgi_param  PATH_\INFO          \$fastcgi_path_info;
                fastcgi_param  SCRIPT_NAME        \$fastcgi_script_name;
                fastcgi_param  REQUEST_URI        \$request_uri;
                fastcgi_param  DOCUMENT_URI       \$document_uri;
                fastcgi_param  DOCUMENT_ROOT      \$document_root;
                fastcgi_param  SERVER_PROTOCOL    \$server_protocol;
                fastcgi_param  GATEWAY_INTERFACE  CGI/1.1;
                fastcgi_param  SERVER_SOFTWARE    nginx;
                fastcgi_param  REMOTE_ADDR        \$remote_addr;
                fastcgi_param  REMOTE_PORT        \$remote_port;
                fastcgi_param  SERVER_ADDR        \$server_addr;
                fastcgi_param  SERVER_PORT        \$server_port;
                fastcgi_param  SERVER_NAME        \$server_name;
                fastcgi_param  REMOTE_USER        \$remote_user;
                fastcgi_param  SCRIPT_FILENAME    \$document_root/wwsympa-wrapper.fcgi;
                fastcgi_param  HTTP_HOST          \$server_name;
                fastcgi_intercept_errors on;
    }
}
EOF

systemctl restart nginx
```
