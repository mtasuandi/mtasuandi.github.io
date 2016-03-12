---
layout:     post
title:      Htaccess Redirect www to non-www https
date:       2016-03-11 18:28:17
author:     Teguh Suandi
summary:    Generic htaccess settings to redirect www to non-www with https
categories: Regex
tags:
 - regex
 - mod rewrite
 - apache
 - redirect
 - url rewriting
---

.htaccess settings to redirect www to non-www with https:

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash">
RewriteEngine On
RewriteBase /
RewriteCond %{HTTP_HOST} ^www\.(.*)$ [NC]
RewriteRule ^(.*)$ https://%1/$1 [R=301,L]
		</code>
	</pre>
</div>

Reference:
<ul>
  <li><a href="http://stackoverflow.com/a/1270281" target="_blank">http://stackoverflow.com/a/1270281</a></li>
</ul>