## The needs:

Working on our latest project, we had to deal with browser compatibility: there is some browsers the app doesn't support and some others that are only partially supported.

*   From an incompatible browser, you can’t use the app at all. Instead you are required to download a more recent browser.
*   From a partially compatible browser, the app informs you that you would have a better experience with a more recent browser.

So we needed a service that could answer the question:

> Is the client using an unsupported or a partially supported browser?

## How the Elao Browser Detector Bundle works:

1.  Through configuration, you let the bundle know which browsers are not supported and which browsers are only partially supported.
2.  The bundle registers a listener on the «kernel.request» event.
3.  On each (master) request, the listener catches the «user-agent» header from the request and transmits it to the BrowserDetector service.
4.  The BrowserDetector service gets detailed information about the browser using the get_browser PHP function (based on the browscap.ini file) and stores it in a Browser instance.
5.  The BrowserDetector service resolves compatibility information by comparing the Browser instance to your configuration and stores it.

That’s it, that’s all it does. The BrowserDetector is now yours to work with! From this point, the BrowserDetector won’t do any more resolving: you can simply access its test results through several helpful getters.

## Sample implementation:

### Using the Browser Detector in our project:

We first add the bundle to our project, through composer:

```
# composer.json
"elao/browser-detector": "2.3.*-dev"
```

We activate the bundle:

```
# AppKernel.php
new Elao\\BrowserDetectorBundle\\ElaoBrowserDetectorBundle()
```

And we configure the bundle so that we support Internet Explorer from IE 8 but only IE 9 or later is fully supported:

```
# config.yml
elao_browser_detector:
    browsers:
        partially_compatible:
            "IE": "&lt;9"
        incompatible:
            "IE": "&lt;8"
```

### Detecting incompatible browser:

Now what we want to do on every request is to check that the browser has been identified as compatible and if it wasn&#8217;t, display an error page.

So let’s create our custom kernel listener named BrowserListener and register it:

We just need to listen to the *kernel.request* event for now.

Now we want to know if the client is using an incompatible browser, and if so, prevent the app from going any further and return a response to the client:

**Note:** Returning a Response at this point of the Symfony request handling flow will jump directly to the *kernel.response event*, which means the controller that would normally handle this request will not be called.

We could also choose to return a RedirectResponse, just like that:

```
$event-&gt;setResponse(
        new RedirectResponse($this-&gt;router-&gt;generate('incompatible_browser'))
    );
```

**Note:** We&#8217;re using the &#8220;router&#8221; service here to generate the url from a route name. We would need to add this service to our service declaration before we can use it.

### What about partially supported browsers?

Let’s say we want to add a notice on the top of pages when the client is using a partially compatible browser.

Here we just want to alter the response that has been built by our app (regardless of which controller did it) by injecting some HTML code inside it (just like the WebProfilerBundle adds the Web Debug Toolbar in the response).

To do that we’ll be listening to the kernel.response event:

We update our service declaration:

```
<service id="acme.demo.browserlistener" class="Acme\DemoBundle\Listener\BrowserListener">;
        <!-- ... -->
        <argument type="service" id="templating" />
        <tag name="kernel.event_listener" event="kernel.response" method="onKernelResponse"/>
    </service>
```

**Note:** We just added the Twig &#8220;templating&#8221; service to our service declaration because we&#8217;ll need it to render the notice&#8217;s html code.

Then we&#8217;ll work on the onKernelResponse method to add the notice before the body closing tag and update the response:

**Note:** We added the html code of the notice just before the body closing tag, we&#8217;ll probably rely on CSS to make it a top-fixed box.

Since we modify the content of the response for a given request depending on a the *User-Agent* header of the request, we provide two different version of the resource for the same URL. Well, that&#8217;s not very cool: some reverse proxy could cache the version including the compatibility notice and provide everyone with this version!

We might want to take care of that issue by using the Vary HTTP Cache Headers:

```
if ($this->browserDetector->isPartiallyCompatible()) {/* ... */}
    $response->headers->add(array('Vary' => 'User-Agent'));
```

**Note:** We need to add the header to every Response, not only the ones that we modified: so we do that outside of the &#8220;if&#8221; <img src="/wp-includes/images/smilies/icon_wink.gif" alt="icon wink The BrowserDetectorBundle: working with the Kernel events" class="wp-smiley" title="The BrowserDetectorBundle: working with the Kernel events" /> 

### Conclusion:

And, Voila! We have a listener that intercepts or modifies the response according to our needs. It&#8217;s clean: We didn&#8217;t wrote a single line of this logic in our controllers, it&#8217;s totally separate from other parts of our app. And it can be enabled or disabled easily.

## More about the Elao BrowserDetector Bundle

The ElaoBrowserDetectorBundle <a href="http://github.com/Elao/ElaoBrowserDetectorBundle" target="_blank">can be found here</a>.

The full source of the sample implementation <a href="https://gist.github.com/Tom32i/80843019f42a2e710356" target="_blank">is available right there</a>

<label for="comment"><strong>Any questions or suggestions? Comment!</strong></label>