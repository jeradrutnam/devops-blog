---
layout: post
title: ""
description: ""
modified: 2013-06-29
category: []
tags: []

---

<section id="table-of-contents" class="toc">
  <header>
    <h3 class="delta">Contents</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

## HTML Elements

Below is just about everything you'll need to style in the theme. Check the source code to see the many embedded elements within paragraphs.

# Heading 1

## Heading 2

### Heading 3

#### Heading 4

##### Heading 5

Heading 6

### Body text

Lorem ipsum dolor sit amet, test link adipiscing elit. **This is strong**. Nullam dignissim convallis est. Quisque aliquam.

![Smithsonian Image]({{ site.url }}/images/3953273590_704e3899d5_m.jpg)
{: .pull-right}

*This is emphasized*. Donec faucibus. Nunc iaculis suscipit dui. 53 = 125. Water is H2O. Nam sit amet sem. Aliquam libero nisi, imperdiet at, tincidunt nec, gravida vehicula, nisl. The New York Times (That’s a citation). Underline.Maecenas ornare tortor. Donec sed tellus eget sapien fringilla nonummy. Mauris a ante. Suspendisse quam sem, consequat at, commodo vitae, feugiat in, nunc. Morbi imperdiet augue quis tellus.

HTML and CSS are our tools. Mauris a ante. Suspendisse quam sem, consequat at, commodo vitae, feugiat in, nunc. Morbi imperdiet augue quis tellus. Praesent mattis, massa quis luctus fermentum, turpis mi volutpat justo, eu volutpat enim diam eget metus.

### Blockquotes

> Lorem ipsum dolor sit amet, test link adipiscing elit. Nullam dignissim convallis est. Quisque aliquam.

## List Types

### Ordered Lists

1. Item one
   1. sub item one
   2. sub item two
   3. sub item three
2. Item two

### Unordered Lists

* Item one
* Item two
* Item three

## Code Snippets

{% highlight css %}
#container {
  float: left;
  margin: 0 -240px 0 0;
  width: 100%;
}
{% endhighlight %}


* Sartorial hoodie 
* Labore viral forage
* Tote bag selvage 
* DIY exercitation et id ugh tumblr church-key

[^1]: Texture image courtesty of [Lovetextures](http://www.lovetextures.com/)


### Figures (for images or video)

#### One Up

<figure>
	<a href="http://farm9.staticflickr.com/8426/7758832526_cc8f681e48_b.jpg"><img src="http://farm9.staticflickr.com/8426/7758832526_cc8f681e48_c.jpg"></a>
	<figcaption><a href="http://www.flickr.com/photos/80901381@N04/7758832526/" title="Morning Fog Emerging From Trees by A Guy Taking Pictures, on Flickr">Morning Fog Emerging From Trees by A Guy Taking Pictures, on Flickr</a>.</figcaption>
</figure>

#### Two Up

Apply the `half` class like so to display two images side by side that share the same caption.

{% highlight html %}
<figure class="half">
	<img src="/images/image-filename-1.jpg">
	<img src="/images/image-filename-2.jpg">
	<figcaption>Caption describing these two images.</figcaption>
</figure>
{% endhighlight %}

And you'll get something that looks like this:

<figure class="half">
	<a href="http://placehold.it/1200x600.jpg"><img src="http://placehold.it/600x300.jpg"></a>
	<a href="http://placehold.it/1200x600.jpg"><img src="http://placehold.it/600x300.jpg"></a>
	<figcaption>Two images.</figcaption>
</figure>

#### Three Up

Apply the `third` class like so to display three images side by side that share the same caption.

{% highlight html %}
<figure class="third">
	<img src="/images/image-filename-1.jpg">
	<img src="/images/image-filename-2.jpg">
	<img src="/images/image-filename-3.jpg">
	<figcaption>Caption describing these three images.</figcaption>
</figure>
{% endhighlight %}

And you'll get something that looks like this:

<figure class="third">
	<img src="http://placehold.it/600x300.jpg">
	<img src="http://placehold.it/600x300.jpg">
	<img src="http://placehold.it/600x300.jpg">
	<figcaption>Three images.</figcaption>
</figure>

* Creating a Ruby on Rails app with a specific version of the Rails gem
### Operating System and Machine Type



#### Pass 3: Install Selected CYGWIN Packages

**NOTE:** This is only a list of packages that I go through and check for 
install.

* Database
  * \+ libsqlite3-devl
  * \+ libsqlite3_0
  * \+ sqlite3
* Editors
  * \+ gvim
  * \+ nano
  * \+ vim
  * \+ xemacs
* Graphics
  * \+ ImageMagick
* Net
  * \+ curl
  * \+ inetutils
  * \+ openssh
* Shells
  * \+ mintty ([http://code.google.com/p/mintty/](http://code.google.com/p/mintty/))
  * \+ zsh



Install `rspec` & `rspec-rails`  
rspec: [http://github.com/rspec/rspec](http://github.com/rspec/rspec)  
rspec-rails: [http://github.com/rspec/rspec-rails](http://github.com/rspec/rspec-rails)


I use Jekyll for this site so I need to install the gems that I need.  
jekyll: [http://github.com/mojombo/jekyll](http://github.com/mojombo/jekyll)  
rdiscount: [http://github.com/rtomayko/rdiscount](http://github.com/rtomayko/rdiscount)  
RedCloth: [http://redcloth.org/](http://redcloth.org/)  



Install Pygment for Jekyll install code highlighting  
Pygments: [http://pygments.org/](http://pygments.org/)

*Rails*  
rails.vim: [http://www.vim.org/scripts/script.php?script_id=1567](http://www.vim.org/scripts/script.php?script_id=1567)


**Setup `mintty` Terminal and Defaults**

`.bash_profile`

{% highlight bash %}
# base-files version 3.9-3

# To pick up the latest recommended .bash_profile content,
# look in /etc/defaults/etc/skel/.bash_profile

# Modifying /etc/skel/.bash_profile directly will prevent
# setup from updating it.

# The copy in your home directory (~/.bash_profile) is yours, please
# feel free to customise it to create a shell
# environment to your liking.  If you feel a change
# would be benifitial to all, please feel free to send
# a patch to the cygwin mailing list.

# ~/.bash_profile: executed by bash for login shells.

# source the system wide bashrc if it exists
if [ -e /etc/bash.bashrc ] ; then
  source /etc/bash.bashrc
fi

# source the users bashrc if it exists
if [ -e "${HOME}/.bashrc" ] ; then
  source "${HOME}/.bashrc"
fi

# Set PATH so it includes user's private bin if it exists
if [ -d "${HOME}/bin" ] ; then
  PATH=${HOME}/bin:${PATH}
fi

# Custom Paths
JAVA_HOME=/cygdrive/d/src/jdk1.6.0_21

# Set PATH
PATH=$JAVA_HOME/bin:$PATH
export PATH
{% endhighlight %}

![Smithsonian Image]({{ site.url }}/images/galera/add-node.png)
{: .pull-right}

[^1]: Texture image courtesty of [Lovetextures](http://www.lovetextures.com/)