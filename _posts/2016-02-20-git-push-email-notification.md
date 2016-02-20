---
layout:     post
title:      Git Push Email Notification
date:       2016-02-20 02:56:17
author:     Teguh Suandi
summary:    Setup email notification when someone push to the repository
categories: Git
tags:
 - git
 - repositories
---

Open <code>post-receive</code> hook for selected repository.

In this case, we want to send push email notification when someone push update to <code>https://git.domain.com/repositories/repo/repo.git</code>.

Activate this line or add it if not exist:

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash">
. /usr/share/git-core/contrib/hooks/post-receive-email
		</code>
	</pre>
</div>

Next, you have to set where the notifications email will be sent. Open config file for the repository, in this case it is located at <code>/home/user/public_html/git.domain.com/repo/repo.git/config</code>.

Add this code to the config file (We will use git@domain.com as email sender for each git push email notifications):

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash">
[hooks]
  mailinglist = mail@domain.com, anothermail@domain.com
  envelopesender = git@domain.com
  showrev = "git show -C %s; echo"       
  emailprefix = "[GIT] "
		</code>
	</pre>
</div>