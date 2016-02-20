---
layout:     post
title:      How To Setup Git Smart HTTP
date:       2016-02-20 02:30:17
author:     Teguh Suandi
summary:    Guide step by step to setup git smart http transport
categories: Git
tags:
 - git
 - repositories
---

When working with git repositories, there are some protocol that can be used to manage the repository. We can use SSH or HTTP/HTTPS protocol. SSH is actually great way if there are only few trusted developers who work on the project, but sometimes it has an issue like firewall blocked by some Internet Service Provider and of course, we don’t want to give all the developers access to the server. So, using the HTTP/HTTPS protocol or it can be named as “Smart HTTP Protocol” is great way to replace SSH. The benefit using the HTTP/HTTPS is of course we can use Secure Sockets Layer certificate to secure the connection.

This page will describe how to configure the Git Smart HTTP Protocol. The server is running on CentOS 6.6.

The target from this configuration is, to use <code>https://git.domain.com</code> for the repositories. The final git repository url is something like <code>https://git.domain.com/repositories/project/project.git</code>. So, you may need to create sub domain first.

<h3>Server Specifications</h3>
To make the Smart HTTP works, make sure you have these following requirements on the server:
<ul>
	<li>Git version is v.1.6.6 or higher</li>
	<li>Apache 2.x with these mod enabled<ul><li>mod_cgi</li><li>mod_alias</li><li>mod_env</li><li>mod_rewrite</li></ul></li>
</ul>

To check installed modules on your apache/httpd (Apache and Httpd is actually the same, Apache is the name used in Ubuntu and Httpd used in CentOS), you can simply run command <code>httpd -M</code>.

<h3>Setting up Smart HTTP</h3>
We have to modify httpd configuration to setting up Smart HTTP. Go find your <code>httpd.conf</code> and open it. For CentOS, it usually placed at <code>/etc/httpd/conf/httpd.conf</code>. It’s better to backup your <code>httpd.conf</code> before doing any modification.

In some tutorial, its better to include our additional httpd configuration from an external file instead of modify directly the <code>httpd.conf</code> file, because if you rebuild the apache (let say using EasyApache), you may lost your configuration and of course that would be trouble in the future.

Open <code>httpd.conf</code>, you can use vim for this and include this <code>git.conf</code> script. You may need to create that <code>git.conf</code> file manually. And this is the configuration for <code>git.conf</code>:

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash">
<LocationMatch "^/repositories/.*$">
   AuthType Basic
   AuthName "Private Git Repositories"
   AuthUserFile /home/user/.htpasswds/.htpasswd
   Require valid-user
</LocationMatch>
ScriptAlias /repositories /home/user/public_html/bin/git-http-backend
		</code>
	</pre>
</div>

Make sure to replace the <code>user</code> with the real user in the server.

I had SuExec enabled when configure the Smart HTTP on the server, so I had to put another configuration (executed bash file) on the web directory (it will not working if the file was located outside the public_html directory).

This is the configuration for file <code>git-http-backend</code>:

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash">
#!/bin/bash
PATH_INFO=$SCRIPT_URL
GIT_PROJECT_ROOT=/home/user/public_html/git.domain.com
export GIT_HTTP_EXPORT_ALL=true
/usr/libexec/git-core/git-http-backend
		</code>
	</pre>
</div>

Make sure the <code>git-http-backend</code> file is executable, you can run <code>chmod a+x git-http-backend</code> from <code>/home/user/public_html/bin</code>.

Don’t forget to restart the <code>apache/httpd</code>.

<h3>Authentication</h3>
We will use <code>htpasswd</code> as the authentication method, so let say we save the <code>.htpasswd</code> file at <code>/home/user/.htpasswds/.htpasswd</code>. You may need to place it anywhere.

To create the <code>.htpasswd</code> file, we can use <code>htpasswd</code> command that already available. If the file is not created yet, you can use command <code>htpasswd -c /home/user/.htpasswds/.htpasswd userone</code>.

If you get an error like <code>"htpasswd command not found"</code>, you have to use full path to the <code>htpasswd</code>. It usually placed at <code>/usr/local/apache/bin/htpasswd</code>.

So, you can run the command like <code>/usr/local/apache/bin/htpasswd -c /home/user/.htpasswds/.htpasswd userone</code>.

After the <code>.htpasswd</code> file is created and you want to add more user to it. You can run command <code>htpasswd /home/user/.htpasswds/.htpasswd usertwo</code>.

<h3>Troubleshooting</h3>
If there’s something wrong, maybe the repositories clone doesn’t work or push isn’t allowed. Here are some suggestion to solve:
<ul>
	<li>Make sure all directories under git.domain.com not owned by root user.</li>
	<li>When you trying to push and there’s error <code>"not allowed"</code> message, you may need to run <code>git config –file config http.receivepack true</code> for each created git repository.</li>
	<li>Make sure the <code>post-update</code> and <code>post-receive</code> hook is executable.</li>
</ul>