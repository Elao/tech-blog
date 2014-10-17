
**UPDATE February 15, 2012 to reflect last symfony changes**

Sometimes, you have to recreate your session from an existing session id passed in parameters (for example, with <a href="http://www.uploadify.com/" target="_blank">uploadify</a>, the flash uploading your files cannot pass cookies to the request and you have to pass it throught a query parameter).

This can be done easily in Symfony2 by overriding the native Session Storage class.

**First, you need to create your own session storage class like the following:**

```
<?php
namespace Elao\BackBundle\Session;
use Symfony\Component\DependencyInjection\ContainerInterface;
use Symfony\Component\HttpFoundation\Session\Storage\NativeFileSessionStorage;

class Storage extends NativeSessionStorage
{
    public function __construct($savePath = null, array $options = array(), ContainerInterface $container)
    {
        $request = $container->get('request');
        if ($request->query->has('sessionId')) {
            $request->cookies->set(session_name(), 1);    // We have to simulate this cookie, in order to bypass the "hasPreviousSession" security check
            session_id($request->query->get('sessionId'));
        }
        return parent::__construct($savePath, $options);
    }
}
```

**Then, you must redefine the default one (in your config.yml for example):**

```
parameters:
    session.storage.native_file.class:     Elao\BackBundle\Session\Storage
services:
    session.storage.native_file:
       class:     %session.storage.native_file.class%
        arguments: [%kernel.cache_dir%, %session.storage.options%, @service_container]
```

That's it. We inject the container to be able to retrieve the request.
Now, if the session storage finds a `sessionId` POST parameters (you can change it to `$request->query` for GET parameter) in your request, it will try to use it as the session id and reloads the cookie 	from the session.

Hope this [helps][1].

 [1]: hardoise://testme