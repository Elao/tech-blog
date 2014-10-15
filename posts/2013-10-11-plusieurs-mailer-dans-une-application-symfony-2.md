Voici une petite astuce que j&#8217;ai découvert hier et que j&#8217;ai pensé utile de partager !

# Problématique

**Contexte :**

*   Votre application Symfony2 utilise un service tiers pour envoyer vos e-mails (comme [Mailjet][1] par exemple)
*   Votre application a besoin d&#8217;envoyer des e-mails de notifications aux administrateurs lorsque des erreurs se produisent (Erreurs 500 par exemple)

Imaginez qu&#8217;une erreur se produise sur l&#8217;une de vos pages et que vous ayez des centaines d&#8217;utilisateurs en train de visualiser cette même page. Pour chaque affichage de cette dernière, vous allez recevoir un e-mail de notification. Si ces e-mails passent par Mailjet par exemple, votre quota fondra à vue d’œil&#8230; Au mieux vous atteindrez une limite de quota et les mails suivants seront bloqués, au pire vous ferez un énorme hors forfait qui pourra vous couter très cher&#8230; <img src="http://old-blog.elao.dev/wp-includes/images/smilies/icon_wink.gif" alt="icon wink Plusieurs mailer dans une application Symfony 2" class="wp-smiley" title="Plusieurs mailer dans une application Symfony 2" /> 

# Solution proposée

La solution que je vous propose pour palier à cela est de créer 2 mailer utilisant 2 transports différents :

*   Les e-mails classiques destinés aux utilisateurs de votre application passeront par votre serveur SMTP
*   Les e-mails techniques destinés aux administrateurs de votre application seront envoyés via la fonction mail de votre PHP.

# Mise en œuvre de la solution

Pour cela rien de plus simple, créez plusieurs mailers dans votre app/config/config.yml :

```
# Swiftmailer Configuration
swiftmailer:
    default_mailer: default
    mailers:
        default:
            transport: smtp
            host:      in.mailjet.com
            username:  mylogin
            password:  mypassword
        notifier:
            transport: mail
```

Vous remarquerez que cela aura pour effet de créer 2 services d'envoi d'e-mails que vous pourrez ensuite utiliser dans votre application :

```
jlopes:/Volumes/Elao/workspace/myProject ./app/console container:debug | grep mailer
mailer                                            n/a       alias for swiftmailer.mailer.default
swiftmailer.mailer                                n/a       alias for swiftmailer.mailer.default
swiftmailer.mailer.default                        container Swift_Mailer
swiftmailer.mailer.default.plugin.messagelogger   container Swift_Plugins_MessageLogger
swiftmailer.mailer.default.transport              container Swift_Transport_EsmtpTransport
swiftmailer.mailer.notifier                       container Swift_Mailer
swiftmailer.mailer.notifier.plugin.messagelogger  container Swift_Plugins_MessageLogger
swiftmailer.mailer.notifier.transport             container Swift_Transport_MailTransport
...
````

Cette astuce reste très simple mais je n&#8217;ai rien trouvé dans la documentation officielle de Symfony qui l&#8217;expliquait clairement. Elle peut se révéler très utile lorsque vous utilisez le bundle **[ErrorNotifierBundle][2]**

Merci et à bientôt !

 [1]: https://fr.mailjet.com/
 [2]: https://github.com/Elao/ErrorNotifierBundle