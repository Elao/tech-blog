
Ça peut être pratique d&#8217;être informé quand votre application crash sur un mobile. Mais le mieux ça serait d&#8217;avoir la pile d&#8217;appels non ? Pour le débug y a pas mieux.

Mais comment le savoir et avoir ces infos ?

<!--more-->

Des erreurs tout le monde en fait, des bugs il y en a toujours, mais c&#8217;est notre devoir de les éradiquer. L&#8217;idée est de se pointer au carrefour où tous les bugs passent et de les attraper au vol.

Du coup, on va donc se déclarer apte à recevoir des exceptions, la passer dans un script de traitement / mise en forme d&#8217;erreur et envoyer le rapport (par mail ou par requête http asynchrone peu importe).

Je vous passe les détails sur le script de traitement de comment il marche en interne, pour me focaliser sur &#8220;Comment mettre ça en place ?&#8221;.

# On se déclare apte à prendre les bugs

<pre id="line1"><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">BOOL</span><span class="br0">&#41;</span>application<span class="sy0">:</span><span class="br0">&#40;</span>UIApplication <span class="sy0">*</span><span class="br0">&#41;</span>application didFinishLaunchingWithOptions<span class="sy0">:</span><span class="br0">&#40;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSDictionary_Class/"><span class="kw5">NSDictionary</span></a> <span class="sy0">*</span><span class="br0">&#41;</span>launchOptions<br />
    <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; <span class="co2">// Je prends les bugs, et je les passe à une méthode du script</span><br />
    &nbsp; &nbsp; NSSetUncaughtExceptionHandler<span class="br0">&#40;</span><span class="sy0">&</span>amp;UncaughtExceptionHandler<span class="br0">&#41;</span>;<br />
    &nbsp; &nbsp; <span class="co2">// Je dis au script, attrape ce que tu peux (SIGABRT, SIGQUIT etc)</span><br />
    &nbsp; &nbsp; SetupUncaughtSignals<span class="br0">&#40;</span><span class="br0">&#41;</span>;<br />
    &nbsp; &nbsp; <span class="co2">// Là c'est notre code pour envoyer les erreurs !</span><br />
      &nbsp; <span class="br0">&#91;</span>self sendBugsIfPresent<span class="br0">&#93;</span>;<br />
    <br />
    &nbsp; &nbsp; self.window <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>UIWindow alloc<span class="br0">&#93;</span> initWithFrame<span class="sy0">:</span><span class="br0">&#91;</span><span class="br0">&#91;</span>UIScreen mainScreen<span class="br0">&#93;</span> bounds<span class="br0">&#93;</span><span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; <span class="co2">// Override point for customization after application launch.</span><br />
    &nbsp; &nbsp; UIViewController <span class="sy0">*</span>viewController1 <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>FirstViewController alloc<span class="br0">&#93;</span> initWithNibName<span class="sy0">:</span><span class="co3">@</span><span class="st0">"FirstViewController"</span> bundle<span class="sy0">:</span><span class="kw2">nil</span><span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; UIViewController <span class="sy0">*</span>viewController2 <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>SecondViewController alloc<span class="br0">&#93;</span> initWithNibName<span class="sy0">:</span><span class="co3">@</span><span class="st0">"SecondViewController"</span> bundle<span class="sy0">:</span><span class="kw2">nil</span><span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; self.tabBarController <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>UITabBarController alloc<span class="br0">&#93;</span> init<span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; self.tabBarController.viewControllers <span class="sy0">=</span> <span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/"><span class="kw5">NSArray</span></a> arrayWithObjects<span class="sy0">:</span>viewController1, viewController2, <span class="kw2">nil</span><span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; self.window.rootViewController <span class="sy0">=</span> self.tabBarController;<br />
    &nbsp; &nbsp; <span class="br0">&#91;</span>self.window makeKeyAndVisible<span class="br0">&#93;</span>;<br />
    <br />
    &nbsp; &nbsp; <span class="kw1">return</span> <span class="kw2">YES</span>;<br />
    <span class="br0">&#125;</span>
  </div>
</div>

</pre>

#  On regarde si un rapport d&#8217;erreur est dispo, on envoie

Ici, on a fait le choix de proposer à l&#8217;utilisateur de l&#8217;envoyer par mail, du coup dans un premier temps, on lui affiche une UIAlertView, puis en fonction de son choix, on prépare un mail.

Veuillez noter l&#8217;utilisation d&#8217;une variable &#8220;globale&#8221; GBug qui contiendra dans une chaine toute le rapport d&#8217;erreur. C&#8217;est le script de traitement qui déclare cette variable.

<pre id="line1"><div class="codecolorer-container objc vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="objc codecolorer">
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span>sendBugsIfPresent<br />
    <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; <a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSError_Class/"><span class="kw5">NSError</span></a> <span class="sy0">*</span>err;<br />
    &nbsp; &nbsp; <a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> <span class="sy0">*</span>path <span class="sy0">=</span> GetBugReport<span class="br0">&#40;</span><span class="br0">&#41;</span>;<br />
    <br />
    &nbsp; &nbsp; GBug <span class="sy0">=</span> <span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/"><span class="kw5">NSString</span></a> stringWithContentsOfFile<span class="sy0">:</span>path encoding<span class="sy0">:</span>NSUTF8StringEncoding error<span class="sy0">:&</span>amp;err<span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span>GBug <span class="sy0">==</span> <span class="kw2">nil</span><span class="br0">&#41;</span> <span class="kw1">return</span>;<br />
    &nbsp; &nbsp; <span class="br0">&#91;</span><span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSFileManager_Class/"><span class="kw5">NSFileManager</span></a> defaultManager<span class="br0">&#93;</span> removeItemAtPath<span class="sy0">:</span>path error<span class="sy0">:&</span>amp;err<span class="br0">&#93;</span>;<br />
    <br />
    &nbsp; &nbsp; UIAlertView <span class="sy0">*</span>alert <span class="sy0">=</span> <span class="br0">&#91;</span><span class="br0">&#91;</span>UIAlertView alloc<span class="br0">&#93;</span> initWithTitle<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Unexpected exception"</span><br />
    &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; message<span class="sy0">:</span><span class="co3">@</span><span class="st0">"An unexpected exception was caught the last time this program ran. Send the developer a bug report by e-mail?"</span><br />
    &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;delegate<span class="sy0">:</span>self<br />
    &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; cancelButtonTitle<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Cancel"</span><br />
    &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; otherButtonTitles<span class="sy0">:</span><span class="co3">@</span><span class="st0">"Send Report"</span>,<span class="kw2">nil</span><span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; <span class="br0">&#91;</span>alert show<span class="br0">&#93;</span>;<br />
    <span class="br0">&#125;</span><br />
    <br />
    <span class="sy0">-</span> <span class="br0">&#40;</span><span class="kw4">void</span><span class="br0">&#41;</span>alertView<span class="sy0">:</span><span class="br0">&#40;</span>UIAlertView <span class="sy0">*</span><span class="br0">&#41;</span>alertView didDismissWithButtonIndex<span class="sy0">:</span><span class="br0">&#40;</span>NSInteger<span class="br0">&#41;</span>buttonIndex<br />
    <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span>buttonIndex <span class="sy0">==</span> <span class="nu0">1</span><span class="br0">&#41;</span> <span class="br0">&#123;</span><br />
    &nbsp; &nbsp; &nbsp; &nbsp; <span class="coMULTI">/*<br />
    &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;*&nbsp; Send an e-mail with the specified title.<br />
    &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;*/</span><br />
    <br />
    &nbsp; &nbsp; &nbsp; &nbsp; <a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSMutableString_Class/"><span class="kw5">NSMutableString</span></a> <span class="sy0">*</span>url <span class="sy0">=</span> <span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSMutableString_Class/"><span class="kw5">NSMutableString</span></a> stringWithCapacity<span class="sy0">:</span><span class="nu0">4096</span><span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#91;</span>url appendString<span class="sy0">:</span><span class="co3">@</span><span class="st0">"mailto:bugs@yourdomain.com?subject=Bug%20Report&amp;body="</span><span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#91;</span>url appendString<span class="sy0">:</span><span class="br0">&#91;</span>GBug stringByAddingPercentEscapesUsingEncoding<span class="sy0">:</span>NSUTF8StringEncoding<span class="br0">&#93;</span><span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#91;</span><span class="br0">&#91;</span>UIApplication sharedApplication<span class="br0">&#93;</span> openURL<span class="sy0">:</span><span class="br0">&#91;</span><a href="http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSURL_Class/"><span class="kw5">NSURL</span></a> URLWithString<span class="sy0">:</span>url<span class="br0">&#93;</span><span class="br0">&#93;</span>;<br />
    &nbsp; &nbsp; <span class="br0">&#125;</span><br />
    <br />
    <span class="br0">&#125;</span>
  </div>
</div>

</pre>

# Le script de traitement

Bien sûr tout le code suivant est à mettre dans un fichier &#8220;notif.c&#8221; ou autre, puis il faut l&#8217;inclure dans AppDelegate.m. C&#8217;est lui s&#8217;occupe de tout.

<noscript>
  <pre><code class="language- ">

#include &lt;execinfo.h&gt; 

/************************************************************************/
/*																		*/
/*	Globals																*/
/*																		*/
/************************************************************************/

static NSString *GBug;

/************************************************************************/
/*																		*/
/*	Crash Reporter														*/
/*																		*/
/************************************************************************/

static NSString *GetBugReport()
{
	NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
	NSString *dir = [paths objectAtIndex:0];
	return [dir stringByAppendingPathComponent:@"bug.txt"];
}


void SignalHandler(int sig, siginfo_t *info, void *context)
{
	void *frames[128];
	int i,len = backtrace(frames, 128);
	char **symbols = backtrace_symbols(frames,len);
    
	/*
	 *	Now format into a message for sending to the user
	 */
    
	NSMutableString *buffer = [[NSMutableString alloc] initWithCapacity:4096];
    
	NSBundle *bundle = [NSBundle mainBundle];
	[buffer appendFormat:@"PComp version %@ build %@\n\n",
     [bundle objectForInfoDictionaryKey:@"CFBundleVersion"],
     [bundle objectForInfoDictionaryKey:@"CIMBuildNumber"]];
	[buffer appendString:@"Uncaught Signal\n"];
	[buffer appendFormat:@"si_signo    %d\n",info-&gt;si_signo];
	[buffer appendFormat:@"si_code     %d\n",info-&gt;si_code];
	[buffer appendFormat:@"si_value    %d\n",info-&gt;si_value];
	[buffer appendFormat:@"si_errno    %d\n",info-&gt;si_errno];
	[buffer appendFormat:@"si_addr     0x%08lX\n",info-&gt;si_addr];
	[buffer appendFormat:@"si_status   %d\n",info-&gt;si_status];
	[buffer appendString:@"Stack trace:\n\n"];
	for (i = 0; i &lt; len; ++i) {
		[buffer appendFormat:@"%4d - %s\n",i,symbols[i]];
	}
    
	/*
	 *	Get the error file to write this to
	 */
    
	NSError *err;
	[buffer writeToFile:GetBugReport() atomically:YES encoding:NSUTF8StringEncoding error:&err];
	NSLog(@"Error %@",buffer);
	exit(0);
}

static void SetupUncaughtSignals()
{
	struct sigaction mySigAction;
	mySigAction.sa_sigaction = SignalHandler;
	mySigAction.sa_flags = SA_SIGINFO;
    
	sigemptyset(&mySigAction.sa_mask);
	sigaction(SIGQUIT, &mySigAction, NULL);
	sigaction(SIGILL, &mySigAction, NULL);
	sigaction(SIGTRAP, &mySigAction, NULL);
	sigaction(SIGABRT, &mySigAction, NULL);
	sigaction(SIGEMT, &mySigAction, NULL);
	sigaction(SIGFPE, &mySigAction, NULL);
	sigaction(SIGBUS, &mySigAction, NULL);
	sigaction(SIGSEGV, &mySigAction, NULL);
	sigaction(SIGSYS, &mySigAction, NULL);
	sigaction(SIGPIPE, &mySigAction, NULL);
	sigaction(SIGALRM, &mySigAction, NULL);
	sigaction(SIGXCPU, &mySigAction, NULL);
	sigaction(SIGXFSZ, &mySigAction, NULL);
}


/*	UncaughtExceptionHandler
 *
 *		Handle uncaught exceptions
 */

static void UncaughtExceptionHandler(NSException *exception)
{
	/*
	 *	Extract the call stack
	 */
    
	NSArray *callStack = [exception callStackReturnAddresses];
	int i,len = [callStack count];
	void **frames = malloc(sizeof(void *) * len);
    
	for (i = 0; i &lt; len; ++i) {
		frames[i] = (void *)[[callStack objectAtIndex:i] unsignedIntegerValue];
	}
	char **symbols = backtrace_symbols(frames,len);
    
	/*
	 *	Now format into a message for sending to the user
	 */
    
	NSMutableString *buffer = [[NSMutableString alloc] initWithCapacity:4096];
    
	NSBundle *bundle = [NSBundle mainBundle];
	[buffer appendFormat:@"PComp version %@ build %@\n\n",
     [bundle objectForInfoDictionaryKey:@"CFBundleVersion"],
     [bundle objectForInfoDictionaryKey:@"CIMBuildNumber"]];
	[buffer appendString:@"Uncaught Exception\n"];
	[buffer appendFormat:@"Exception Name: %@\n",[exception name]];
	[buffer appendFormat:@"Exception Reason: %@\n",[exception reason]];
	[buffer appendString:@"Stack trace:\n\n"];
	for (i = 0; i &lt; len; ++i) {
		[buffer appendFormat:@"%4d - %s\n",i,symbols[i]];
	}
    
	/*
	 *	Get the error file to write this to
	 */
    
	NSError *err;
	[buffer writeToFile:GetBugReport() atomically:YES encoding:NSUTF8StringEncoding error:&err];
	NSLog(@"Error %@",buffer);
	exit(0);
}

/*	TerminateHandler
 *
 *		C++ exception terminate handler
 */

void TerminateHandler(void)
{
	void *frames[128];
	int i,len = backtrace(frames, 128);
	char **symbols = backtrace_symbols(frames,len);
    
	/*
	 *	Now format into a message for sending to the user
	 */
    
	NSMutableString *buffer = [[NSMutableString alloc] initWithCapacity:4096];
    
	NSBundle *bundle = [NSBundle mainBundle];
	[buffer appendFormat:@"PComp version %@ build %@\n\n",
     [bundle objectForInfoDictionaryKey:@"CFBundleVersion"],
     [bundle objectForInfoDictionaryKey:@"CIMBuildNumber"]];
	[buffer appendString:@"Uncaught C++ Exception\n"];
	[buffer appendString:@"Stack trace:\n\n"];
	for (i = 0; i &lt; len; ++i) {
		[buffer appendFormat:@"%4d - %s\n",i,symbols[i]];
	}
    
	/*
	 *	Get the error file to write this to
	 */
    
	NSError *err;
	[buffer writeToFile:GetBugReport() atomically:YES encoding:NSUTF8StringEncoding error:&err];
	NSLog(@"Error %@",buffer);
	exit(0);
}

</code></pre>
</noscript>

Notez que le script va mettre dans un fichier de l&#8217;application &#8220;bug.txt&#8221; le dernier rapport. Il y a donc un risque d&#8217;écrasement si plusieurs rapports non générés, mais non envoyés tout de suite.Il y a donc un petit chantier de développement pour rendre ce système moins couillon.  
Sauvegarde en bdd local ? Fichier indexé par la date ? C&#8217;est à vous de voir !