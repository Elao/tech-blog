Ça peut être pratique d'être informé quand votre application crash sur un mobile. Mais le mieux ça serait d'avoir la pile d'appels non ? Pour le débug y a pas mieux.

Mais comment le savoir et avoir ces infos ?

Des erreurs tout le monde en fait, des bugs il y en a toujours, mais c'est notre devoir de les éradiquer. L'idée est de se pointer au carrefour où tous les bugs passent et de les attraper au vol.

Du coup, on va donc se déclarer apte à recevoir des exceptions, la passer dans un script de traitement / mise en forme d'erreur et envoyer le rapport (par mail ou par requête http asynchrone peu importe).

Je vous passe les détails sur le script de traitement de comment il marche en interne, pour me focaliser sur "Comment mettre ça en place ?".

### On se déclare apte à prendre les bugs

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    // Je prends les bugs, et je les passe à une méthode du script
    NSSetUncaughtExceptionHandler(&amp;UncaughtExceptionHandler);
    // Je dis au script, attrape ce que tu peux (SIGABRT, SIGQUIT etc)
    SetupUncaughtSignals();
    // Là c'est notre code pour envoyer les erreurs !
    [self sendBugsIfPresent];

    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    // Override point for customization after application launch.
    UIViewController *viewController1 = [[FirstViewController alloc] initWithNibName:@"FirstViewController" bundle:nil];
    UIViewController *viewController2 = [[SecondViewController alloc] initWithNibName:@"SecondViewController" bundle:nil];
    self.tabBarController = [[UITabBarController alloc] init];
    self.tabBarController.viewControllers = [NSArray arrayWithObjects:viewController1, viewController2, nil];
    self.window.rootViewController = self.tabBarController;
    [self.window makeKeyAndVisible];

    return YES;
}
```

### On regarde si un rapport d'erreur est dispo, on envoie

Ici, on a fait le choix de proposer à l'utilisateur de l'envoyer par mail, du coup dans un premier temps, on lui affiche une `UIAlertView`, puis en fonction de son choix, on prépare un mail.

Veuillez noter l'utilisation d'une variable "globale" `GBug` qui contiendra dans une chaine toute le rapport d'erreur. C'est le script de traitement qui déclare cette variable.

```
- (void)sendBugsIfPresent
{
    NSError *err;
    NSString *path = GetBugReport();

    GBug = [NSString stringWithContentsOfFile:path encoding:NSUTF8StringEncoding error:&amp;err];
    if (GBug == nil) return;
    [[NSFileManager defaultManager] removeItemAtPath:path error:&amp;err];

    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Unexpected exception"
                                                    message:@"An unexpected exception was caught the last time this program ran. Send the developer a bug report by e-mail?"
                                                   delegate:self
                                          cancelButtonTitle:@"Cancel"
                                          otherButtonTitles:@"Send Report",nil];
    [alert show];
}

- (void)alertView:(UIAlertView *)alertView didDismissWithButtonIndex:(NSInteger)buttonIndex
{
    if (buttonIndex == 1) {
        /*
         *  Send an e-mail with the specified title.
         */

        NSMutableString *url = [NSMutableString stringWithCapacity:4096];
        [url appendString:@"mailto:bugs@yourdomain.com?subject=Bug%20Report&amp;body="];
        [url appendString:[GBug stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding]];
        [[UIApplication sharedApplication] openURL:[NSURL URLWithString:url]];
    }

}
```

### Le script de traitement

Bien sûr tout le code suivant est à mettre dans un fichier `notif.c` ou autre, puis il faut l'inclure dans `AppDelegate.m`. C'est lui s'occupe de tout.

```
#include <execinfo.h>

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
	[buffer appendFormat:@"si_signo    %d\n",info->si_signo];
	[buffer appendFormat:@"si_code     %d\n",info->si_code];
	[buffer appendFormat:@"si_value    %d\n",info->si_value];
	[buffer appendFormat:@"si_errno    %d\n",info->si_errno];
	[buffer appendFormat:@"si_addr     0x%08lX\n",info->si_addr];
	[buffer appendFormat:@"si_status   %d\n",info->si_status];
	[buffer appendString:@"Stack trace:\n\n"];
	for (i = 0; i < len; ++i) {
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

	for (i = 0; i < len; ++i) {
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
	for (i = 0; i < len; ++i) {
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
	for (i = 0; i < len; ++i) {
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
```

Notez que le script va mettre dans un fichier de l'application `bug.txt` le dernier rapport. Il y a donc un risque d'écrasement si plusieurs rapports non générés, mais non envoyés tout de suite.Il y a donc un petit chantier de développement pour rendre ce système moins couillon.
Sauvegarde en bdd local ? Fichier indexé par la date ? C'est à vous de voir !