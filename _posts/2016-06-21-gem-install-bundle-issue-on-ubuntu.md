---
layout: post
title:  ' "gem install bundler" issue on Ubuntu 16.04 '
categories: [ubuntu, ruby, bundler]
---

Every story or issue has a start.

I somehow believed github pages would be cool thing to do and so I decided to give it a shot. Everything went smooth by following the link [https://pages.github.com/](https://pages.github.com/). Then I wanted to try something advance. So I dived into this topic [Setting up your GitHub Pages site locally with Jekyll](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/). The fist step is to install `Ruby` and `Bundler`. Since my Ubuntu 16.04 already has Ruby 2.3.1 installed, I went straight to Step 4 in Section "Requirements" and I unfortunately got an error:


```
$gem install bundler
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /var/lib/gems/2.3.0 directory.
```

Since it is obviously a permission issue, I tried `sudo`:

```
$sudo gem install bundler
```
Bingo, there is no issue anymore. It turned out I laughed too early. When I followed the guide to install other commands, I got other permission issues and `sudo` wont help. So anyway, `sudo` is not a solution.

**Solution:**

Then I happened to run across the official `Ruby` installing guide [https://gorails.com/setup/ubuntu/16.04](https://gorails.com/setup/ubuntu/16.04). I decided to have a re-install. To do that, I uninstalled the Ruby 2.3.1 that already exists on my Ubuntu 16.04. Then following the following commands to re-insatll Ruby 2.3.1.


```
sudo apt-get update
sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties libffi-dev

cd
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
exec $SHELL

git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
exec $SHELL

rbenv install 2.3.1
rbenv global 2.3.1
ruby -v
```

The last step is to install Bundler:

```
gem install bundler
rbenv rehash
```
Since here, you wont see any issue any more by following [Setting up your GitHub Pages site locally with Jekyll](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/). Enjoy your Github pages!
