# Apache ModSecurity学习记录

我的 Apache ModSecurity 学习和使用记录


## RHEL/CentOS 6.3/6.2/6.1/6/5.8 安装mod_security


### 第一步：安装依赖包

    # yum install httpd* gcc make pcre* libxml2* libcurl* lua* libtool openssl -y

### 第二步：安装Mod_Security

    # cd /usr/local/src
    # wget http://www.modsecurity.org/tarball/2.7.2/modsecurity-apache_2.7.2.tar.gz
    # tar -xzf modsecurity-apache_2.7.2.tar.gz
    # cd modsecurity-apache*
    # ./configure
    # make
    # make install
    # cp modsecurity.conf-recommended /etc/httpd/conf.d/modsecurity.conf

### 第三步：下载OWASP Mod_Security Core Rule Set

    # cd /etc/httpd/
    # wget http://downloads.sourceforge.net/project/mod-security/modsecurity-crs/0-CURRENT/modsecurity-crs_2.2.5.tar.gz
    # tar xzf modsecurity-crs_2.2.5.tar.gz
    # mv modsecurity-crs_2.2.5 modsecurity-crs
    # cd modsecurity-crs
    # cp modsecurity_crs_10_setup.conf.example modsecurity_crs_10_config.conf

### 第四步：修改配置文件

在 */etc/httpd/conf/httpd.conf* 中添加

    LoadModule security2_module modules/mod_security2.so
    LoadModule unique_id_module modules/mod_unique_id.so

在 */etc/httpd/conf/httpd.conf* 文件末尾添加

    <IfModule security2_module>
        Include modsecurity-crs/modsecurity_crs_10_config.conf
        Include modsecurity-crs/base_rules/*.conf
    </IfModule>

在 */etc/httpd/conf.d/modsecurity.conf* 中修改:

    SecRuleEngine DetectionOnly

为：

    SecRuleEngine On

### 第五步：测试

编辑文件 */var/www/html/myinc.php*

    <?php
        $i = $_GET['i'];
        include ($i); 
    ?>
    
编辑文件 */var/www/html/index.html*

    <h1> Hello World! </h1> <body bgcolor=red</body>
        
检查 */var/log/modsec_audit.log* 和 */var/log/httpd/error_log*

    # wget http://127.0.0.1/
    # wget http://localhost/
    # wget http://localhost/myinc.php?i=/etc/passwd


