---
layout:     post
title:      How Do I Manage Repositories
date:       2016-02-20 01:30:17
author:     Teguh Suandi
summary:    Useful resource to centralize repositories
categories: Git
tags:
 - git
 - repositories
---

<h3>Location</h3>
The repositories directory is located at <code>/home/user/public_html/git.domain.com/</code>.

<h3>Directory structure</h3>
Each project consist three different repositories (<code>master</code>, <code>beta</code> and <code>live</code>). Master repository will be named using project name. so I use this structure:

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash"> 
home
  user
    public_html
      git.domain.com
        projectname
          projectname.git
          beta.git
          live.git
        anotherprojectname
          anotherprojectname.git
          beta.git
          live.git
		</code>
	</pre>
</div>

<h3>Creating a repository</h3>
Create at least three git repositories for each project (<code>projectname.git</code>, <code>beta.git</code> and <code>live.git</code>). Also create sub domains for each repository because each repository will represent different deploy url.

<h4>Master</h4>
<code>projectname.git</code> is where the developers clone the repository (or you can say this is the "master repository for each project"), it has deploy hook to the domain under domain.com. The deploy url for <code>projectname.git</code> will be something like <code>http://projectname.master.domain.com</code>.

Create a folder named <code>projectname.git</code> under <code>projectname</code> directory and run git init to initialize an empty git repository <code>git init --bare</code>.

Next, activate the <code>post-update</code> and <code>post-receive</code> hook for <code>projectname.git</code>. We can use the existing hook files provided by git.

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash">
# These command executed from /home/user/public_html/git.user.com/projectname/projectname.git

mv hooks/post-update.sample hooks/post-update
chmod a+x hooks/post-update

mv hooks/post-receive.sample hooks/post-receive
chmod a+x hooks/post-receive
		</code>
	</pre>
</div>

We don't want to do anything else for <code>post-update</code> hook except to activate it. But for <code>post-receive</code> hook, we have to update the code a bit for automatic deployment.

Put this on <code>post-receive</code> hook:

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash">
git --work-tree=/home/user/public_html/projectname.master.domain.com --git-dir=/home/user/public_html/git.domain.com/projectname/projectname.git checkout -f
		</code>
	</pre>
</div>

<h4>Beta</h4>
<code>beta.git</code> is used for beta testing, it also has deploy hook but to <code>http://projectname.beta.domain.com</code>. Deploying to beta is executed not by the developers.

Create a folder named <code>beta.git</code> under <code>projectname</code> directory and run git init to initialize an empty git repository <code>git init --bare</code>.

Next, you need to activate the <code>post-update</code> and <code>post-receive</code> hook for <code>beta.git</code>. We can use the existing hook files provided by git.

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash">
# These command executed from /home/user/public_html/git.domain.com/projectname/beta.git

mv hooks/post-update.sample hooks/post-update
chmod a+x hooks/post-update

mv hooks/post-receive.sample hooks/post-receive
chmod a+x hooks/post-receive
		</code>
	</pre>
</div>

We also don't want to do anything else for <code>post-update</code> hook except to activate it. But for <code>post-receive</code> hook, we have to update the code a bit for automatic deployment.

Add this on <code>post-receive</code> hook:

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash">
git --work-tree=/home/user/public_html/projectname.beta.domain.com --git-dir=/home/user/public_html/git.domain.com/projectname/beta.git checkout -f
		</code>
	</pre>
</div>

For deploying from <code>projectname.git</code> to the beta url, you have to add git remote to the <code>beta.git</code> from <code>projectname.git</code>.

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash">
# This command executed from /home/user/public_html/git.domain.com/projectname/projectname.git
git remote add beta ../beta.git
		</code>
	</pre>
</div>

To deploy from <code>projectname.git</code>, simply run command <code>git push beta master</code> from <code>/home/user/public_html/git.domain.com/projectname/projectname.git</code>.

<h4>Live</h4>
<code>live.git</code> is used for live version when all bugs was solved during the beta testing. <code>live.git</code> has deploy hook to <code>http://projectname.domain.com</code>. Deploying from <code>projectname.git</code> to <code>live.git</code> also not executed by the developers. Someone that has access to the server with SSH should do this.

Create a folder named <code>live.git</code> under <code>projectname</code> directory and run git init to initialize an empty git repository <code>git init --bare</code>.

Next, you need to activate the <code>post-update</code> and <code>post-receive</code> hook for <code>live.git</code>. We can use the existing hook files provided by git.

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash">
# These command executed from /home/user/public_html/git.domain.com/projectname/live.git

mv hooks/post-update.sample hooks/post-update
chmod a+x hooks/post-update

mv hooks/post-receive.sample hooks/post-receive
chmod a+x hooks/post-receive
		</code>
	</pre>
</div>

Put this on post-receive hook:

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash">
git --work-tree=/home/user/public_html/projectname.domain.com --git-dir=/home/user/public_html/git.domain.com/projectname/live.git checkout -f
		</code>
	</pre>
</div>

For deploying from <code>projectname.git</code> to the live url, you also need to add git remote to the <code>live.git</code> from <code>projectname.git</code>.

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash">
# This command executed from /home/user/public_html/git.domain.com/projectname/projectname.git
git remote add live ../live.git
		</code>
	</pre>
</div>  

To deploy from <code>projectname.git</code>, simply run command <code>git push live master</code> from <code>/home/user/public_html/git.domain.com/projectname/projectname.git</code>.