
**UPDATE February 15, 2012 to reflect last symfony changes**

Sometimes, you have to recreate your session from an existing session id passed in parameters (for example, with <a href="http://www.uploadify.com/" target="_blank">uploadify</a>, the flash uploading your files cannot pass cookies to the request and you have to pass it throught a query parameter).
This can be done easily in Symfony2 by overriding the native Session Storage class.

First, you need to create your own session storage class like the following:

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="kw2"><?php</span><br />     <span class="kw2">namespace</span> Elao\BackBundle\Session<span class="sy0">;</span><br />     <span class="kw2">use</span> Symfony\Component\DependencyInjection\ContainerInterface<span class="sy0">;</span><br />     <span class="kw2">use</span> Symfony\Component\HttpFoundation\Session\Storage\NativeFileSessionStorage<span class="sy0">;</span><br /> <br />     <span class="kw2">class</span> Storage <span class="kw2">extends</span> NativeSessionStorage<br />     <span class="br0">&#123;</span><br />         <span class="kw2">public</span> <span class="kw2">function</span> __construct<span class="br0">&#40;</span><span class="re0">$savePath</span> <span class="sy0">=</span> <span class="kw4">null</span><span class="sy0">,</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a> <span class="re0">$options</span> <span class="sy0">=</span> <a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> ContainerInterface <span class="re0">$container</span><span class="br0">&#41;</span><br />         <span class="br0">&#123;</span><br />             <span class="re0">$request</span> <span class="sy0">=</span> <span class="re0">$container</span><span class="sy0">-></span><span class="me1">get</span><span class="br0">&#40;</span><span class="st_h">'request'</span><span class="br0">&#41;</span><span class="sy0">;</span><br />             <span class="kw1">if</span> <span class="br0">&#40;</span><span class="re0">$request</span><span class="sy0">-></span><span class="me1">query</span><span class="sy0">-></span><span class="me1">has</span><span class="br0">&#40;</span><span class="st_h">'sessionId'</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="br0">&#123;</span><br />                 <span class="re0">$request</span><span class="sy0">-></span><span class="me1">cookies</span><span class="sy0">-></span><span class="me1">set</span><span class="br0">&#40;</span><a href="http://www.php.net/session_name"><span class="kw3">session_name</span></a><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="sy0">,</span> <span class="nu0">1</span><span class="br0">&#41;</span><span class="sy0">;</span>    <span class="co1">// We have to simulate this cookie, in order to bypass the "hasPreviousSession" security check</span><br />                 <a href="http://www.php.net/session_id"><span class="kw3">session_id</span></a><span class="br0">&#40;</span><span class="re0">$request</span><span class="sy0">-></span><span class="me1">query</span><span class="sy0">-></span><span class="me1">get</span><span class="br0">&#40;</span><span class="st_h">'sessionId'</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="sy0">;</span><br />             <span class="br0">&#125;</span><br />             <span class="kw1">return</span> parent<span class="sy0">::</span>__construct<span class="br0">&#40;</span><span class="re0">$savePath</span><span class="sy0">,</span> <span class="re0">$options</span><span class="br0">&#41;</span><span class="sy0">;</span><br />         <span class="br0">&#125;</span><br />     <span class="br0">&#125;</span>
  </div>
</div>

Then, you must redefine the default one (in your config.yml for example):

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co4">parameters</span>:<span class="co3"><br />     session.storage.native_file.class</span><span class="sy2">: </span>     Elao\BackBundle\Session\Storage<span class="co4"><br /> services</span>:<span class="co4"><br />     session.storage.native_file</span><span class="sy2">:<br /> </span>        class:     <span class="co2">%session.storage.native_file.class%</span><span class="co3"><br />         arguments</span><span class="sy2">: </span><span class="br0">&#91;</span><span class="co2">%kernel.cache_dir%, %session.storage.options%, @service_container]</span>
  </div>
</div>

That's it. We inject the container to be able to retrieve the request.
Now, if the session storage finds a "**sessionId**" POST parameters (you can change it to "$request->query" for GET parameter) in your request, it will try to use it as the session id and reloads the cookie from the session.

Hope this [helps][1].

 [1]: hardoise://testme