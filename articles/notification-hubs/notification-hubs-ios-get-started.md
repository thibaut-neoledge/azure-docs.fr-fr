<properties
	pageTitle="Prise en main d’Azure Notification Hubs"
	description="Découvrez comment utiliser Azure Notification Hubs pour envoyer des notifications Push."
	services="notification-hubs"
	documentationCenter="ios"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article" 
	ms.date="05/27/2015"
	ms.author="wesmc"/>

# Prise en main de Notification Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Vue d'ensemble

Cette rubrique montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application iOS. Le didacticiel vous apprend à créer une application iOS vide qui reçoit des notifications Push à l'aide du service de notification Push Apple (APN, Apple Push Notification). Une fois la création terminée, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre concentrateur de notification.

Ce didacticiel présente un scénario de diffusion simple utilisant les concentrateurs de notification.

##Configuration requise

Ce didacticiel requiert les composants requis suivants :

+ [Kit de développement logiciel (SDK) Mobile Services iOS]
+ [XCode 6][Install Xcode]
+ Un appareil compatible iOS 8 (ou version ultérieure)
+ Un abonnement au programme pour développeurs iOS

   >[AZURE.NOTE]En raison de la configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d'un simulateur.

Vous devez accomplir ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications iOS.

> [AZURE.NOTE]Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

[AZURE.INCLUDE [Notification Hubs - Activer les notifications Push Apple](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##Configuration de votre concentrateur de notification

Cette section vous guide dans la création et la configuration d'un nouveau concentrateur de notification avec le certificat push que vous avez créé. Si vous souhaitez utiliser un concentrateur de notification que vous avez déjà créé, vous pouvez ignorer les étapes 2 à 5.


1. Dans le trousseau d'accès, cliquez sur le nouveau certificat push que vous avez créé dans la catégorie **Certificats**. Cliquez sur **Exporter**, nommez le fichier, sélectionnez le format **.p12**, puis cliquez sur **Enregistrer**.

    ![][1]

	Notez le nom du fichier et l'emplacement du certificat exporté.

	>[AZURE.NOTE]Ce didacticiel permet de créer un fichier QuickStart.p12. Il est possible que le nom de votre fichier et son emplacement sont différents.

2. Connectez-vous au [portail de gestion Azure] et cliquez sur **+NOUVEAU** en bas de l'écran.

3. Cliquez sur **Services d'application**, puis sur **Service Bus**, **Concentrateur de notification**, **Création rapide**.

   	![][2]

4. Tapez un nom pour votre concentrateur de notification, sélectionnez la région souhaitée, puis cliquez sur **Créer un concentrateur de notification**.

   	![][3]

5. Cliquez sur l’espace de noms que vous venez de créer (en général ***le nom du concentrateur de notification*-ns**) pour ouvrir le tableau de bord.

   	![][4]

6. Cliquez sur l'onglet **Concentrateurs de notification** en haut de la page, puis cliquez sur celui que vous venez de créer.

   	![][5]

7. Cliquez sur l’onglet **Configurer** en haut, puis cliquez sur le bouton **Télécharger** dans les paramètres de notification Apple pour télécharger l'empreinte numérique du certificat. Sélectionnez ensuite le certificat **.p12** exporté précédemment ainsi que le mot de passe du certificat. Assurez-vous de choisir si vous souhaitez utiliser la **Production** (si vous souhaitez envoyer des notifications Push aux utilisateurs qui ont acheté votre application depuis le store) ou le service Push **Bac à sable (sandbox)** (au cours du développement).

   	![][6]

8. Sélectionnez l’onglet **Tableau de bord** en haut, puis cliquez sur **Afficher la chaîne de connexion**. Notez les deux chaînes de connexion.

   	![][7]

Votre Notification Hub est configuré pour APNS, et vous disposez des chaînes de connexion pour inscrire votre application et envoyer des notifications.

##Connexion de votre application au concentrateur de notification

1. Dans XCode, créez un projet iOS et sélectionnez le modèle **Single View Application**.

   	![][8]

2. Lorsque vous définissez les options de votre nouveau projet, assurez-vous d'utiliser les mêmes **nom de produit** et **ID d'organisation** que ceux utilisés lors de la définition de l'ID d'offre groupée sur le portail de développement Apple.

	![][11]

3. Sous **Cibles**, cliquez sur le nom de votre projet, cliquez sur l’onglet **Paramètres de génération**et développez **Identité de signature du code**, puis sous **Débogage**, sélectionnez le **profil d’identité de signature du code**. Basculez les **niveaux** de **Basique** à **Tous** et définissez le **profil de configuration** sur le profil que vous avez créé précédemment.

	Si vous ne voyez pas le nouveau profil de configuration que vous avez créé dans XCode, essayez d'actualiser les profils de votre identité de signature en cliquant sur **XCode** dans la barre de menus, puis en cliquant sur **Préférences**, **Compte**, **Afficher les détails**, puis en sélectionnant votre identité de signature et en cliquant sur le bouton Actualiser dans le coin inférieur droit.

   	![][9]

4. Téléchargez la **version 1.2.4** du [kit de développement logiciel (SDK) Mobile Services pour iOS] et décompressez le fichier. Dans XCode, avec le bouton droit de la souris sur votre projet et sélectionnez l’option **Ajouter des fichiers à** pour ajouter le dossier **WindowsAzureMessaging.framework** à votre projet XCode. Sélectionnez **Copier des éléments si nécessaire**, puis cliquez sur **Ajouter**.

   	![][10]

5. Ouvrez le fichier AppDelegate.h et ajoutez l’instruction d’importation suivante :

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. Dans le fichier AppDelegate.m, ajoutez le code suivant dans la méthode `didFinishLaunchingWithOptions` basée sur votre version d’iOS : Ce code enregistre le handle de l’appareil avec APNS :

	Pour iOS 8 :

	 	UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
												UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];

	Pour les versions d’iOS antérieures à 8 :

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


7. Dans le même fichier, ajoutez les méthodes suivantes et remplacez les espaces réservés des littéraux de chaîne par le *nom de votre concentrateur* et la chaîne *DefaultListenSharedAccessSignature* notée précédemment. Ce code transmet le jeton de l’appareil au concentrateur de notification de sorte que le concentrateur de notification puisse envoyer des notifications :

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<Enter your listen connection string>"
										notificationHubPath:@"<Enter your hub name>"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
				else {
				    [self MessageBox:@"Registration Status" message:@"Registered"];
				}
	    	}];
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}


8. Dans le même fichier, ajoutez la méthode suivante pour afficher une **UIAlert** si la notification est reçue alors que l’application est active :


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
		}

8. Générez et exécutez l'application sur votre appareil pour vérifier les échecs.

## Comment envoyer des notifications


Vous pouvez tester la réception de notifications dans votre application en envoyant des notifications dans le portail Azure à l'aide de l'onglet Déboguer du concentrateur de notification, comme indiqué dans l'écran ci-dessous.

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

![][31]

1. Dans XCode, ouvrez votre Main.storyboard et ajoutez les composants d'interface utilisateur suivants à partir de la bibliothèque d'objets pour permettre à l'utilisateur d'envoyer des notifications push à l'application.

	- Une étiquette sans texte d’étiquette. Elle sera utilisée pour signaler les erreurs d’envoi des notifications. La propriété **Lignes** doit être définie sur **0** de sorte que les marges droite et gauche ainsi que le haut de la vue soient autodimensionnés.
	- Un champ de texte avec du texte de l’**espace réservé** défini sur **Entrer un message de notification**. Limitez le champ juste en dessous de l'étiquette, comme illustré ci-dessous. Définissez le Contrôleur d'affichage comme délégué de sortie.
	- Un bouton intitulé **Envoyer une notification** limité juste en dessous de la zone de texte, de manière horizontale et centrée.

	La vue doit se présenter comme suit :

	![][32]


2. Ouvrez votre fichier ViewController.h et ajoutez les instructions `#import` et `#define` suivantes. Remplacez le littéral de chaîne d’espace réservé par votre chaîne de connexion *DefaultFullSharedAccessSignature* et le *nom de votre concentrateur*.


		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"


3. Ajoutez des sorties pour les zones de texte et d'étiquette connectées à votre affichage et mettez à jour votre définition `interface` pour prendre en charge `UITextFieldDelegate` et `NSXMLParserDelegate`. Ajoutez les trois déclarations de propriété ci-dessous pour aider le support à appeler l'API REST et à analyser la réponse.

	Votre fichier ViewController.h doit se présenter comme suit :

		#import <UIKit/UIKit.h>
		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"

		@interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
		{
			NSXMLParser *xmlParser;
		}

		// Make sure these outlets are connected to your UI by ctrl+dragging.
		@property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
		@property (weak, nonatomic) IBOutlet UILabel *sendResults;

		@property (copy, nonatomic) NSString *statusResult;
		@property (copy, nonatomic) NSString *currentElement;

		@end


4. Ouvrez ViewController.m et ajoutez le code suivant pour analyser votre chaîne de connexion *DefaultFullSharedAccessSignature*. Comme indiqué dans la [référence de l’API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx), ces informations analysées permettront de générer un jeton SAS pour l’en-tête de demande *Autorisation*.

		NSString *HubEndpoint;
		NSString *HubSasKeyName;
		NSString *HubSasKeyValue;

		-(void)ParseConnectionString
		{
			NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
			NSString *part;

			if ([parts count] != 3)
			{
				NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
					reason:@"Invalid full shared access connection string" userInfo:nil];

				@throw parseException;
			}

			for (part in parts)
			{
				if ([part hasPrefix:@"Endpoint"])
				{
					HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
				}
				else if ([part hasPrefix:@"SharedAccessKeyName"])
				{
					HubSasKeyName = [part substringFromIndex:20];
				}
				else if ([part hasPrefix:@"SharedAccessKey"])
				{
					HubSasKeyValue = [part substringFromIndex:16];
				}
			}
		}

5. Dans ViewController.m, mettez à jour la méthode `viewDidLoad` pour analyser la chaîne de connexion lors du chargement de la vue. Ajoutez également les méthodes utilitaires ci-dessous.


		- (void)viewDidLoad
		{
			[super viewDidLoad];
			[self ParseConnectionString];
		}

		-(NSString *)CF_URLEncodedString:(NSString *)inputString
		{
		   return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
				NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}





6. Dans ViewController.m, ajoutez le code suivant pour créer le jeton d'autorisation SAP qui est fourni dans l’en-tête *Autorisation* comme indiqué dans la [référence de l'API REST](http://msdn.microsoft.com/library/azure/dn495627.aspx).

		-(NSString*) generateSasToken:(NSString*)uri
		{
			NSString *targetUri;
			NSString* utf8LowercasedUri = NULL;
			NSString *signature = NULL;
			NSString *token = NULL;

			@try
			{
				// Add expiration
				uri = [uri lowercaseString];
				utf8LowercasedUri = [self CF_URLEncodedString:uri];
				targetUri = [utf8LowercasedUri lowercaseString];
				NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
				int expiresInMins = 60; // 1 hour
				expiresOnDate += expiresInMins * 60;
				UInt64 expires = trunc(expiresOnDate);
				NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

				// Get an hmac_sha1 Mac instance and initialize with the signing key
				const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
				const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
				unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
				CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
				NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
				signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

				// construct authorization token string
				token = [NSString stringWithFormat:@"SharedAccessSignature sr=%@&sig=%@&se=%qu&skn=%@",
					targetUri, signature, expires, HubSasKeyName];
			}
			@catch (NSException *exception)
			{
				[self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
			}
			@finally
			{
				if (utf8LowercasedUri != NULL)
					CFRelease((CFStringRef)utf8LowercasedUri);
				if (signature != NULL)
				CFRelease((CFStringRef)signature);
			}

			return token;
		}


7. Utilisez la commande **Ctrl + déplacer** depuis le bouton **Envoyer une notification** vers ViewController.m pour ajouter une action pour l’événement **Touch Down** qui exécute l'appel de l'API REST à l’aide du code suivant.

		- (IBAction)SendNotificationMessage:(id)sender
		{
			self.sendResults.text = @"";
			[self SendNotificationRESTAPI];
		}

		- (void)SendNotificationRESTAPI
		{
		    NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

			// Apple Notification format of the notification message
		    NSString *json = [NSString stringWithFormat:@"{"aps":{"alert":"%@"}}",
								self.notificationMessage.text];

			// Construct the messages REST endpoint
			NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
												HUBNAME, API_VERSION]];

			// Generated the token to be used in the authorization header.
			NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

			//Create the request to add the APNS notification message to the hub
			NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
			[request setHTTPMethod:@"POST"];
			[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

			// Signify apple notification format
			[request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

			//Authenticate the notification message POST request with the SaS token
			[request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

			//Add the notification message body
			[request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

			// Send the REST request
		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
				completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
			{
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (error || httpResponse.statusCode != 200)
		        {
		            NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
		        }
				if (data != NULL)
				{
		        	xmlParser = [[NSXMLParser alloc] initWithData:data];
		        	[xmlParser setDelegate:self];
		       		[xmlParser parse];
		    	}
		    }];
		    [dataTask resume];
		}


8. Dans ViewController.m, ajoutez la méthode déléguée suivante pour prendre en charge la fermeture du clavier pour la zone de texte. Utilisez la commande **Ctrl + déplacer** depuis la zone de texte vers l’icône du contrôleur d'affichage dans le concepteur d'interface pour définir le contrôleur d'affichage comme sortie déléguée.

		//===[ Implement UITextFieldDelegate methods ]===

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
			[textField resignFirstResponder];
			return YES;
		}


9. Dans ViewController.m, ajoutez les méthodes déléguées suivantes pour prendre en charge l'analyse de la réponse à l'aide de `NSXMLParser`.

		//===[ Implement NSXMLParserDelegate methods ]===

		-(void)parserDidStartDocument:(NSXMLParser *)parser
		{
		    self.statusResult = @"";
		}

		-(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
			namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
			attributes:(NSDictionary *)attributeDict
		{
		    NSString * element = [elementName lowercaseString];
		    NSLog(@"*** New element parsed : %@ ***",element);

		    if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
		    {
		        self.currentElement = element;
		    }
		}

		-(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
		{
		    self.statusResult = [self.statusResult stringByAppendingString:
		        [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
		}

		-(void)parserDidEndDocument:(NSXMLParser *)parser
		{
			// Set the status label text on the UI thread
			dispatch_async(dispatch_get_main_queue(),
			^{
				[self.sendResults setText:self.statusResult];
			});
		}



10. Générez le projet et vérifiez s’il présente des erreurs.



Vous trouverez toutes les charges de notification possibles dans le [Guide de programmation des notifications locales et Push] d'Apple.



##Test de votre application

Pour tester les notifications push sur iOS, vous devez déployer l'application sur un périphérique. Vous ne pouvez pas envoyer de notifications push Apple avec le simulateur iOS.

1. Exécutez l'application, vérifiez l’exécution des enregistrements, puis appuyez sur **OK**.

	![][33]

2. Sélectionnez la zone de texte pour entrer un message de notification. Appuyez sur le bouton **Envoyer** du clavier ou sur le bouton **Envoyer notification** de l’affichage pour envoyer le message de notification.

	![][34]

3. La notification est envoyée à tous les appareils abonnés à la notification.

	![][35]

Si vous rencontrez des problèmes ou si vous avez des recommandations d’amélioration de ce didacticiel pour les lecteurs, laissez-nous un commentaire dans la section Disqus ci-dessous.


##Étapes suivantes

Dans cet exemple simple, vous avez envoyé des notifications à tous vos appareils iOS. Afin de cibler des utilisateurs spécifiques, consultez le didacticiel [Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs], et, si vous souhaitez segmenter vos utilisateurs par groupes d'intérêt, vous pouvez lire [Utilisation de Notification Hubs pour envoyer les dernières nouvelles]. Découvrez plus en détail comment utiliser Notification Hubs dans [Recommandations relatives à Notification Hubs].



<!-- Images. -->

[1]: ./media/notification-hubs-ios-get-started/notification-hubs-export-cert-p12.png
[2]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal.png
[3]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal2.png
[4]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal.png
[5]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal2.png
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-connection-strings.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-debug-hub-ios.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Kit de développement logiciel (SDK) Mobile Services iOS]: http://go.microsoft.com/fwLink/?LinkID=266533
[kit de développement logiciel (SDK) Mobile Services pour iOS]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[portail de gestion Azure]: https://manage.windowsazure.com/
[Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Utilisation de Notification Hubs pour envoyer les dernières nouvelles]: notification-hubs-ios-send-breaking-news.md

[Guide de programmation des notifications locales et Push]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
 

<!---HONumber=July15_HO5-->