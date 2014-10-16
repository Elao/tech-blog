
Hi guys !

I just figured out how to handle system path customization in an &#8220;OSX way&#8221;.  
From here to now I used to add my path customization into system profile (**/etc/profile**) or my local profile (**~/.bash_profile**) and my **.bashrc** file (wich is a common meaning on a UNIX system).

**<span style="text-decoration: underline;">For example:</span>**

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="kw3">export</span> <span class="re2">PATH</span>=<span class="re1">$PATH</span>:<span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>zend<span class="sy0">/</span>bin
  </div>
</div>

But, I came across some postings whereby file **/etc/paths** and directory **/etc/paths.d** are described as the places where search path action takes place now on OSX.

Fact is in Leopard, Apple has introduced a new mechanism for managing your system path, and we no longer have to modify those files.  
Instead, we just have to put a simple text file containing a path entry (or entries) into **/etc/paths.d/**.

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="re5">-rw-rw-r--</span> <span class="nu0">1</span> root wheel 19B Sep <span class="nu0">17</span> <span class="nu0">2009</span> <span class="kw2">git</span><br /> <span class="re5">-rw-r--r--</span> <span class="nu0">1</span> root wheel 60B Jan <span class="nu0">23</span> <span class="nu0">14</span>:<span class="nu0">39</span> sublime
  </div>
</div>

**<span style="text-decoration: underline;">For example:</span>**

We can create a file named &#8220;git&#8221; wich contain path to git binaries:

*Content of /etc/paths.d/git*

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    <span class="sy0">/</span>usr<span class="sy0">/</span>local<span class="sy0">/</span>git<span class="sy0">/</span>bin
  </div>
</div>