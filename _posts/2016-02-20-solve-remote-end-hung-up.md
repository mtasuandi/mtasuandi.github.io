---
layout:     post
title:      Solve The Remote End Hung Up Unexpectedly
date:       2016-02-20 02:50:17
author:     Teguh Suandi
summary:    Handling error fatal the remote end hung up unexpectedly
categories: Git
tags:
 - git
 - repositories
 - troubleshooting
---

When trying to push commit to the git repository and there's an error message like:

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash">
suandi@mtasuandi:/opt/lampp/htdocs/repos/repo$ git push origin master
Username for 'https://git.domain.com': teguh
Password for 'https://teguh@git.domain.com': 
Counting objects: 6669, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (6334/6334), done.
Writing objects: 100% (6668/6668), 6.43 MiB | 76.00 KiB/s, done.
Total 6668 (delta 1690), reused 0 (delta 0)
error: RPC failed; result=22, HTTP code = 500
fatal: The remote end hung up unexpectedly
fatal: The remote end hung up unexpectedly
Everything up-to-date
		</code>
	</pre>
</div>

The error appear because git client try to send large amount of data. Increasing the post buffer limit for the git client can solve the issue.

Go to directory where you clone the git repository and then execute <code>git config http.postBuffer 524288000</code>.

Verify if the configuration is really updated:

<div class="highlight">
	<pre>
		<code class="language-bash" data-lang="bash">
suandi@mtasuandi:/opt/lampp/htdocs/repos/repo$ cat .git/config
[core]
  repositoryformatversion = 0
  filemode = true
  bare = false
  logallrefupdates = true
[remote "origin"]
  url = https://git.domain.com/repositories/repo/repo.git
  fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
  remote = origin
  merge = refs/heads/master
[http]
  postBuffer = 524288000
		</code>
	</pre>
</div>