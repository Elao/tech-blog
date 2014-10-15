
Hello,

During this tutorial, we will install Stats.d and Graphite on the same server our application is running on. Don&#8217;t forget that it&#8217;s best if you monitor your application with graphite & stats.d using another server because that will not be the case in this tutorial.

Requirements :

*   A Linux based server (this tutorial will explain the steps for Debian)
*   A running Symfony2 application
*   [StatsDClientBundle][1]

<div>
  Here&#8217;s the result we&#8217;ll have at the end (screenshot from a personal web application) :
</div>

<div>
  <div id='gallery-1' class='gallery galleryid-2423 gallery-columns-3 gallery-size-thumbnail'>
    <dl class='gallery-item'>
      <dt class='gallery-icon landscape'>
        <a href='http://old-blog.elao.dev/wp-content/uploads/2012/11/memory_usage.png'><img width="150" height="150" src="http://old-blog.elao.dev/wp-content/uploads/2012/11/memory_usage-150x150.png" class="attachment-thumbnail" alt="memory usage 150x150 Install Stats.d / Graphite on a debian server in order to monitor a Symfony2 application (1/2)"  title="Install Stats.d / Graphite on a debian server in order to monitor a Symfony2 application (1/2)" /></a>
      </dt>
    </dl>
    
    <dl class='gallery-item'>
      <dt class='gallery-icon landscape'>
        <a href='http://old-blog.elao.dev/wp-content/uploads/2012/11/users.png'><img width="150" height="150" src="http://old-blog.elao.dev/wp-content/uploads/2012/11/users-150x150.png" class="attachment-thumbnail" alt="users 150x150 Install Stats.d / Graphite on a debian server in order to monitor a Symfony2 application (1/2)"  title="Install Stats.d / Graphite on a debian server in order to monitor a Symfony2 application (1/2)" /></a>
      </dt>
    </dl>
    
    <dl class='gallery-item'>
      <dt class='gallery-icon landscape'>
        <a href='http://old-blog.elao.dev/wp-content/uploads/2012/11/matched_route.png'><img width="150" height="150" src="http://old-blog.elao.dev/wp-content/uploads/2012/11/matched_route-150x150.png" class="attachment-thumbnail" alt="matched route 150x150 Install Stats.d / Graphite on a debian server in order to monitor a Symfony2 application (1/2)"  title="Install Stats.d / Graphite on a debian server in order to monitor a Symfony2 application (1/2)" /></a>
      </dt>
    </dl>
    
    <br style="clear: both" />
  </div>
</div>

<div>
</div>

## Install Graphite

<noscript>
  <pre><code class="language-shell shell"># Installing graphite dependencies
apt-get install -y python2.6 python-pip python-cairo python-django python-django-tagging
apt-get install -y libapache2-mod-wsgi python-twisted python-memcache python-pysqlite2 python-simplejson 
pip install whisper
pip install carbon
pip install graphite-web

# Setup a vhost by grabbing the example the graphite team released on their repo.
# In this file, you'll provide the url used to access to your Graphite dashboard
wget https://raw.github.com/tmm1/graphite/master/examples/example-graphite-vhost.conf -O /etc/apache2/sites-available/graphite

# If you are running on a Debian, don't forget to replace in the vhost, the WSGISocketPrefix value by the following:
WSGISocketPrefix /var/run/apache2/wsgi

# Configuring graphite
cd /opt/graphite/conf/
cp graphite.wsgi.example graphite.wsgi
cp carbon.conf.example carbon.conf
cp storage-schemas.conf.example storage-schemas.conf

# Edit storage-schemas.conf in order to include a custom tweak provided by stats.d
[stats]
pattern = ^stats.*
retentions = 10:2160,60:10080,600:262974

[carbon]
pattern = ^carbon\.
retentions = 60:90d

[default_1min_for_1day]
pattern = .*
retentions = 60s:1d

# Create a vi storage-aggregation.conf 
vi storage-aggregation.conf

# Then copy paste in it the following parameters
[min]
pattern = \.min$
xFilesFactor = 0.1
aggregationMethod = min

[max]
pattern = \.max$
xFilesFactor = 0.1
aggregationMethod = max

[sum]
pattern = \.count$
xFilesFactor = 0
aggregationMethod = sum

[default_average]
pattern = .*
xFilesFactor = 0.3
aggregationMethod = average

# Let's go back to the installation process
cd /opt/graphite/webapp/graphite
python manage.py syncdb
chown -R www-data:www-data /opt/graphite/storage/

# Enabling graphite host
a2ensite graphite
/opt/graphite/bin/carbon-cache.py start
/etc/init.d/apache2 restart</code></pre>
</noscript>

If you want the full detail on the graphite, take a look at [the source][2] I used for the installations steps.

## Install [Stat.d][3]

<noscript>
  <pre><code class="language-shell shell">sudo apt-get update && apt-get install git-core curl build-essential openssl libssl-dev

# Don't forget to go to the location you want to install node in (like cd /home/) before running these commands
git clone https://github.com/joyent/node.git
cd node
git checkout v0.8.12

./configure --openssl-libpath=/usr/lib/ssl
make
make test
sudo make install
node -v
npm -v

cd ..
git clone https://github.com/etsy/statsd
cd statsd
cp exampleConfig.js local.js

# Edit local.js and make it looks like:
{
graphitePort: 2003
, graphiteHost: "localhost"
, port: 8125
}

# Then you need to run stats.d
apt-get install screen
screen node stats.js local.js

# Then press Ctrl + a + d in order to let run stats.js in background mode thanks to screen.
 </code></pre>
</noscript>

If you want the full detail, check out the [source][4].

# From now on, we have a running copy of both **Graphite** and **Stat.d** client.

You can access it with the URL you provided in your vhost

Now, let&#8217;s install the [StatsDClientBundle][5] in order to monitor our Symfony2 application

<noscript>
  <pre><code class="language-php php">// composer.json

"require": {
    # ..
    "liuggio/statsd-client-bundle": "&gt;=1.2",
    # ..
}

// After running php composer.phar update liuggio/statsd-client-bundle
// Enable the Bundle in AppKernel.php 

class AppKernel extends Kernel
 {
     public function registerBundles()
     {
         $bundles = array(
         // ...
            new Liuggio\StatsDClientBundle\LiuggioStatsDClientBundle(),
         // ...

// Then add the full configuration for the Bundle in app/config/config.yml

liuggio_stats_d_client:
  connection:
    host: localhost
    port: 8125
    fail_silently: true
  enable_collector: true #default is false
  collectors:
    liuggio_stats_d_client.collector.dbal: 'collect.query'
    liuggio_stats_d_client.collector.visitor: 'collect.visitor'
    liuggio_stats_d_client.collector.memory: 'collect.memory'
    liuggio_stats_d_client.collector.user: 'collect.user'
    liuggio_stats_d_client.collector.exception: 'collect.exception'</code></pre>
</noscript>

Note that we added the full configuration for the bundle which allow us to collect these usefull information such as logged users vs anonymous, memory usage.

# In the 2nd part, we&#8217;ll see how to monitor custom events in your Symfony2 app.

 [1]: https://github.com/liuggio/StatsDClientBundle
 [2]: http://linuxracker.com/2012/03/31/setting-up-graphite-server-on-debian-squeeze/
 [3]: https://github.com/etsy/statsd
 [4]: http://sekati.com/etc/install-nodejs-on-debian-squeeze
 [5]: https://github.com/liuggio/StatsDClientBundle/blob/master/Resources/doc/installation.md