<div class="lab-edi-doc"><h1 id="-wordpress-">搭建 WordPress 个人博客</h1>
<h2 id="-lamp-">准备 LAMP 环境</h2>
<blockquote>
<p>任务时间：10min ~ 20min</p>
</blockquote>
<p>LAMP 是 Linux、Apache、MySQL 和 PHP 的缩写，是 Wordpress 系统依赖的基础运行环境。我们先来准备 LAMP 环境：</p>
<p>（由于部分服务安装过程中展示需要，建议您将下方<strong>终端</strong>部分的高度通过拖拽方式调高一点）</p>
<h3 id="-apache2">安装 Apache2</h3>
<p>在终端输入该命令 ，使用 <code>apt-get</code> 安装 Apache2：</p>
<pre><code>sudo apt-get install apache2 -y
</code></pre><p>安装好后，您可以通过访问实验室IP地址 <a target="_blank" href="http://&lt;您的 CVM IP 地址" title="null">http://&lt;您的 CVM IP 地址&gt;</a> 查看到 “it works” 界面，说明 apache2 安装成功。</p>
<h3 id="-php-">安装 PHP 组件</h3>
<p>apt-get 里有 php7.0 ，所以我们可以直接安装 php7.0 ：</p>
<pre><code>sudo apt-get install php7.0 -y
</code></pre><p>安装 php 相关组件：</p>
<pre><code>sudo apt-get install libapache2-mod-php7.0
</code></pre><h3 id="-mysql-">安装 MySQL 服务</h3>
<p>安装 MySQL 过程中，控制台会提示您输入 MySQL 的密码，您需要输入两次密码，并记住您输入的密码，后续步骤需要用到：</p>
<pre><code>sudo apt-get install mysql-server -y
</code></pre><p>安装 php MySQL相关组件：</p>
<pre><code>sudo apt-get install php7.0-mysql
</code></pre><h3 id="-phpmyadmin">安装 phpmyadmin</h3>
<p>使用 <code>apt-get</code> 安装 phpmyadmin，安装过程中，您需要根据提示选择 apache2 ，再输入root密码 和数据库密码：</p>
<pre><code>sudo apt-get install phpmyadmin -y
</code></pre><p>建立 <code>/var/www/html</code> 下的软连接：</p>
<pre><code>sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin
</code></pre><p>重启 MySQL 服务</p>
<pre><code>sudo service mysql restart
</code></pre><p>重启 Apache 服务：</p>
<pre><code>sudo systemctl restart apache2.service
</code></pre><h2 id="-wordpress">安装并配置 Wordpress</h2>
<blockquote>
<p>任务时间：10min ~ 20min</p>
</blockquote>
<h3 id="-wordpress">安装 Wordpress</h3>
<p>我们需要下载一个 Wordpress 压缩包：<sup>[<a href="#stage-2-step-1-Wordpress">?</a>]</sup></p>
<pre><code>wget https://cn.wordpress.org/wordpress-4.7.4-zh_CN.zip
</code></pre><p>下载完成后，解压这个压缩包</p>
<pre><code>sudo unzip wordpress-4.7.4-zh_CN.zip
</code></pre><p> 解压完后，就能在 <em>Wordpress</em> 文件夹里看到 Wordpress 的源码了 </p>
<p><a id="stage-2-step-1-Wordpress"></a></p>
<blockquote>
<p>到 Wordpress 官网找一个安装包并复制安装包下载路径。</p>
</blockquote>
<h3 id="-wordpress-">为 wordpress 配置一个数据库</h3>
<p>进入 mysql，输入以下代码后，按提示输入您MySQL密码:</p>
<pre><code>mysql -u root -p
</code></pre><p>为 wordpress 创建一个叫 wordpress 的数据库：</p>
<pre><code>CREATE DATABASE wordpress;
</code></pre><p>为 这个数据库设置一个用户为 wordpressuser：</p>
<pre><code>CREATE USER wordpressuser;
</code></pre><p>为这个用户配置一个密码为 password123：</p>
<pre><code>SET PASSWORD FOR wordpressuser= PASSWORD("password123");
</code></pre><p>为这个用户配置数据库的访问权限：</p>
<pre><code>GRANT ALL PRIVILEGES ON wordpress.* TO wordpressuser IDENTIFIED BY"password123";
</code></pre><p>生效这些配置</p>
<pre><code>FLUSH PRIVILEGES;
</code></pre><p>然后退出 mysql </p>
<pre><code>exit;
</code></pre><h3 id="-wordpress">配置 wordpress</h3>
<p>由于PHP默认访问 <em>/var/www/html/</em> 文件夹，所以我们需要把 wordpress 文件夹里的文件都复制到 <em>/var/www/html/</em> 文件夹</p>
<pre><code>sudo mv wordpress/* /var/www/html/
</code></pre><p>修改一下 /var/www/html/ 目录权限：</p>
<pre><code>sudo chmod -R 777 /var/www/html/
</code></pre><p>将apache指定到index.html</p>
<pre><code>sudo mv /var/www/html/index.html /var/www/html/index~.html
</code></pre><p>重启 Apache 服务：</p>
<pre><code>sudo systemctl restart apache2.service
</code></pre><h3 id="-">测试访问</h3>
<ul>
<li>Web 安装界面：<a target="_blank" href="http://&lt;您的 CVM IP 地址" title="null">http://&lt;您的 CVM IP 地址&gt;</a></li>
<li>博客访问地址：<a target="_blank" href="http://&lt;您的 CVM IP 地址" title="null">http://&lt;您的 CVM IP 地址&gt;</a> <sup>[<a href="#stage-2-step-4-cache">?</a>]</sup></li>
</ul>
<p><a id="stage-2-step-4-cache"></a></p>
<blockquote>
<p>如果还是看到 it works 页面，请清除浏览器缓存后重新加载</p>
</blockquote>
<h2 id="-">准备域名和解析</h2>
<blockquote>
<p>任务时间：15min ~ 30min</p>
</blockquote>
<h3 id="-">域名注册</h3>
<p>如果您还没有域名，可以<a target="_blank" href="https://dnspod.qcloud.com/?fromSource=lab" title="null">在腾讯云上选购</a>，过程可以参考下面的视频。</p>
<ul>
<li><em>视频 - 在腾讯云上购买域名</em></li>
</ul>
<h3 id="-">域名解析</h3>
<p>域名购买完成后, 需要将域名解析到实验云主机上，实验云主机的 IP 为：</p>
<pre><code>&lt;您的 CVM IP 地址&gt;
</code></pre><p>在腾讯云购买的域名，可以<em>到控制台添加解析记录</em>，过程可参考下面的视频：</p>
<ul>
<li><em>视频 - 如何在腾讯云上解析域名</em> </li>
</ul>
<p>域名设置解析后需要过一段时间才会生效，通过 <code>ping</code> 命令检查域名是否生效 <sup>[<a href="#stage-3-step-2-replace">?</a>]</sup>，如：</p>
<pre><code>ping www.yourdomain.com
</code></pre><p>如果 ping 命令返回的信息中含有你设置的解析的 IP 地址，说明解析成功。</p>
<p><a id="stage-3-step-2-replace"></a></p>
<blockquote>
<p>注意替换下面命令中的 <code>www.yourmpdomain.com</code> 为您自己的注册的域名</p>
</blockquote>
<h3 id="-">大功告成！</h3>
<p>恭喜，您的 WordPress 博客已经部署完成，您可以通过浏览器访问博客查看效果。</p>
<p>通过IP地址查看：</p>
<p>博客访问地址：<a target="_blank" href="http://&lt;您的域名" title="null">http://&lt;您的域名&gt;</a></p>
<p>通过域名查看：</p>
<p>博客访问地址：<a target="_blank" href="http://www.yourdomain.com" title="null">http://www.yourdomain.com</a>，其中替换 <code>www.yourdomain.com</code> 为之前申请的域名。</p>
</div>