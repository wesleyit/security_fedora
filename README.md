# Segurança

Para preparar um Fedora como estação de segurança, é necessário instalar
uma série de ferramentas de apoio.

-   crunch: gerar wordlists
-   wpscan: scan de wordpress
-   metasploit framework: canivete suiço
-   w3af: canivete #2
-   zap: web proxy
-   burp: proxy and analysis
-   maltego: information gathering
-   john: the jumbo password cracker
-   dradis: collaboration and reporting

## Crunch
```{bash}
cd /opt
mkdir crunch
cd crunch
wget http://downloads.sourceforge.net/project/crunch-wordlist/crunch-wordlist/crunch-3.6.tgz
tar -zxvf crunch*.tgz
cd crunch*/
make
sudo ln -s $(pwd)/crunch /usr/local/bin/
sudo cp /opt/crunch/crunch-3.6/crunch.1 /usr/local/share/man/man1/
```

## W3af
```
cd /opt
git clone --depth 1 https://github.com/andresriancho/w3af.git
cd w3af
sudo dnf install -y \
  webkitgtk-devel.x86_64 webkitgtk3-devel.x86_64 \
  webkitgtk4-devel.x86_64 pywebkitgtk.x86_64 pygtksourceview.x86_64
sudo ln -s $(pwd)/w3af_gui /usr/local/bin/
sudo ln -s $(pwd)/w3af_console /usr/local/bin/
```

## ZAP
```
cd /opt/
wget 'https://github.com/zaproxy/zaproxy/releases/download/2.5.0/ZAP_2.5.0_Linux.tar.gz'
tar -zxvf ZAP*
mv ZAP*/ zap
rm -f ZAP*.gz
cd zap
sudo ln -s $(pwd)/zap.sh /usr/local/bin/
```

## Burp
```
mkdir /opt/burp
cd /opt/burp
wget -O burp.jar \
  'https://portswigger.net/Burp/Releases/Download?productId=100&version=1.7.06&type=jar'
```

## Maltego
```
wget https://www.paterva.com/malv4/community/MaltegoCE.v4.0.11.9358.noarch.rpm \
-O /tmp/maltego.rpm
sudo dnf install -y /tmp/maltego.rpm
```

## John (jumbo community edition)
```
mkdir /opt/john
cd /opt/john
wget http://www.openwall.com/john/j/john-1.8.0-jumbo-1.tar.gz
tar john-1.8.0-jumbo-1.tar.gz
cd john-1.8.0-jumbo-1/src
export CFLAGS='-std=gnu89 -DJOHN_SYSTEMWIDE=1'
export CFLAGS_EXTRA='-std=gnu89 -DJOHN_SYSTEMWIDE=1'
./configure --prefix=/usr/local/john
make clean && make
sudo make install
cd ..
sudo cp -r run /usr/local/john
sudo chmod -R 775 /usr/local/john
sudo ln -s /usr/local/john /usr/share/john
echo "export PATH=/usr/local/john:$PATH" | sudo tee -a /etc/profile.d/john.sh
sudo chmod 755 /etc/profile.d/john.sh
source /etc/profile.d/john.sh
```

## Beef
```
cd /tmp
sudo dnf install -y git make gcc openssl-devel gcc-c++ patch readline \
  readline-devel zlib zlib-devel libyaml-devel libffi-devel bzip2 autoconf \
  automake libtool bison sqlite-devel
git clone git://github.com/beefproject/beef.git /opt/beef
cd /opt/beef
gem install bundler
bundle
cat > /usr/local/bin/beef.sh <<EOF
#!/bin/bash
clear
cd /opt/beef
./beef
EOF
chmod 755 /usr/local/bin/beef.sh
```

## SQLMap
```
git clone https://github.com/sqlmapproject/sqlmap.git /opt/sqlmap
cat > /usr/local/bin/sqlmap <<EOF
#!/bin/bash
cd /opt/sqlmap
python sqlmap.py \$@
EOF
chmod 755 /usr/local/bin/sqlmap
```

## Commix
```
git clone https://github.com/stasinopoulos/commix.git /opt/commix
cat > /usr/local/bin/commix <<EOF
#!/bin/bash
cd /opt/commix
python commix.py \$@
EOF
chmod 755 /usr/local/bin/commix
```

## Panoptic
```
git clone https://github.com/lightos/Panoptic.git /opt/panoptic
cat > /usr/local/bin/panoptic <<EOF
#!/bin/bash
cd /opt/panoptic
python panoptic.py \$@
EOF
chmod 755 /usr/local/bin/panoptic
```

## WPScan
```
sudo dnf install -y gcc ruby-devel libxml2 libxml2-devel libxslt libxslt-devel \
  libcurl-devel patch rpm-build
git clone https://github.com/wpscanteam/wpscan.git /opt/wpscan
cd /opt/wpscan
rm -f .ruby-version
gem install bundler
bundle install --without test
cat > /usr/local/bin/wpscan <<EOF
#!/bin/bash
cd /opt/wpscan
ruby wpscan.rb \$@
EOF
chmod 755 /usr/local/bin/wpscan
```

## Dradis
```
sudo dnf install -y mysql++-devel
cd /opt/
mkdir dradis
cd dradis
git clone https://github.com/dradis/dradis-ce.git
cd dradis-ce/
./bin/setup./bin/setup
cat > /usr/local/bin/dradis <<EOF
#!/bin/bash
cd /opt/dradis/dradis-ce/
bundle exec rails server \$@
EOF
chmod 755 /usr/local/bin/dradis
```
