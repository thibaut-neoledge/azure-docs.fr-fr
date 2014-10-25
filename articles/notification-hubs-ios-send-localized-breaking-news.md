<properties linkid="develop-notificationhubs-tutorials-send-localized-breaking-news-iOS" urlDisplayName="Localized Breaking News" pageTitle="Notification Hubs Localized Breaking News Tutorial for iOS" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send localized breaking news notifications (iOS)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send localized breaking news to iOS devices" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Utilisation de Notification Hubs pour envoyer les dernières nouvelles localisées vers des appareils iOS

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS" class="current">iOS</a>
</div>

Cette rubrique montre comment utiliser la fonctionnalité de **modèle** d'Azure Notification Hubs pour diffuser des notifications relatives aux dernières nouvelles qui ont été localisées par langue et par appareil. Vous devez commencer ce didacticiel avec l'application Windows Store que vous avez créée dans le cadre du didacticiel [Utilisation de Notification Hubs pour envoyer les dernières nouvelles][]. Lorsque vous aurez terminé, vous pourrez vous inscrire aux catégories qui vous intéressent, spécifier une langue dans laquelle recevoir les notifications et recevoir uniquement des notifications Push pour les catégories sélectionnées dans cette langue.

Ce didacticiel vous familiarise avec les étapes de base pour activer ce scénario :

1.  [Concepts de modèle][]
2.  [Interface utilisateur de l'application][]
3.  [Création de l'application iOS][]
4.  [Envoi de notifications à partir de votre serveur principal][]

Ce scénario comporte deux parties :

-   L'application iOS permet aux appareils clients d'indiquer une langue et de s'abonner à différentes catégories de dernières nouvelles.

-   Le serveur principal diffuse les notifications à l'aide des fonctionnalités de **balise** et de **modèle** d'Azure Notification Hubs.

## Conditions préalables

Vous devez avoir suivi le didacticiel [Utilisation de Notifications Hubs pour envoyer les dernières nouvelles][Utilisation de Notification Hubs pour envoyer les dernières nouvelles] et avoir le code à disposition, car le présent didacticiel est basé sur ce code.

Vous avez également besoin de Visual Studio 2012.

## <a name="concepts"></a><span class="short-header">Concepts</span>Concepts de modèle

Dans le didacticiel [Utilisation de Notification Hubs pour envoyer les dernières nouvelles][], vous avez créé une application qui se sert de **balises** pour s'abonner aux notifications relatives à différentes catégories de nouvelles.
Cependant, de nombreuses applications sont destinées à plusieurs marchés et doivent donc être localisées. Cela signifie que le contenu des notifications proprement dites doit lui aussi être localisé et envoyé au bon ensemble d'appareils.
Dans cette rubrique, nous allons vous montrer comment utiliser la fonctionnalité de **modèle** de Notification Hubs pour facilement envoyer des notifications de dernières nouvelles localisées.

Remarque : pour envoyer des notifications localisées, vous pouvez notamment créer plusieurs versions de chaque balise. Par exemple, pour prendre en charge l'anglais, le français et le mandarin, nous aurions besoin de trois balises différentes pour les nouvelles internationales : « world\_en », « world\_fr » et « world\_ch ». Il faudrait ensuite que nous envoyions une version localisée des nouvelles internationales à chacune de ces balises. Dans cette rubrique, nous utilisons des modèles afin d'éviter la prolifération de balises et d'éliminer la nécessité d'envoyer plusieurs messages.

À un haut niveau, les modèles permettent de spécifier comment un appareil particulier reçoit une notification. Le modèle spécifie le format de charge utile exact en se référant aux propriétés qui font partie du message envoyé par le serveur principal de votre application. Aux fins de notre exemple, nous allons envoyer un message de paramètres régionaux contenant toutes les langues prises en charge :

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Ensuite, nous allons nous assurer que les appareils s'inscrivent avec un modèle qui se réfère à la bonne propriété. Par exemple, une application iOS qui souhaite s'abonner aux nouvelles françaises inscrira ce qui suit :

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Les modèles sont une fonctionnalité très puissante sur laquelle vous pouvez obtenir plus d'informations en lisant notre article [Recommandations relatives à Notification Hubs][]. Une référence à la langue d'expression du modèle se trouve dans notre guide [Service Bus Notification Hubs (applications iOS)][].

## <a name="ui"></a><span class="short-header">Interface utilisateur de l'application</span>Interface utilisateur de l'application

Nous allons maintenant modifier l'application de dernières nouvelles que vous avez créée à la rubrique [Utilisation de Notification Hubs pour envoyer les dernières nouvelles][] pour envoyer les dernières nouvelles localisées à l'aide de modèles.

Dans votre MainStoryboard\_iPhone.storyboard, ajoutez un contrôle segmenté avec les trois langues que nous prenons en charge : anglais, français et mandarin.

![][]

Puis, assurez-vous d'ajouter un IBOutlet dans votre ViewController.h comme indiqué ci-dessous :

![][1]

## <a name="building-client"></a><span class="building app">Interface utilisateur de l'application</span>Création de l'application iOS

Pour adapter vos applications clientes afin qu'elles puissent recevoir des messages localisés, vous devez remplacer vos inscriptions *natives* (c.-à-d. les inscriptions qui ne spécifient pas de modèle) par des inscriptions avec modèle.

1.  Dans Notification.h, ajoutez la méthode *retrieveLocale*, puis modifiez le magasin et les méthodes d'abonnement comme indiqué :

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

        - (NSSet*) retrieveCategories;

        - (int) retrieveLocale;

    Dans Notification.m, modifiez la méthode *storeCategoriesAndSubscribe* en ajoutant les paramètres régionaux et en les stockant dans les valeurs par défaut de l'utilisateur :

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];

            [self subscribeWithLocale: locale categories:categories completion:completion];
        }

    Puis, modifiez la méthode *subscribe* afin d'inclure les paramètres régionaux :

        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }

            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"newsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }

    Remarquez que nous utilisons à présent la méthode *registerTemplateWithDeviceToken* au lieu de *registerNativeWithDeviceToken*. Lorsque nous nous abonnons à un modèle, nous devons fournir le modèle json ainsi qu'un nom pour le modèle (car notre application peut vouloir s'abonner à différents modèles). Assurez-vous d'abonner vos catégories sous la forme de balises, car nous voulons nous assurer de recevoir les notifications pour ces nouvelles.

    Enfin, ajoutez une méthode pour extraire les paramètres régionaux des paramètres utilisateur par défaut :

        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            int locale = [defaults integerForKey:@"BreakingNewsLocale"];

            return locale < 0?0:locale;
        }

2.  Maintenant que nous avons modifié notre classe Notifications, nous devons nous assurer que notre paramètre ViewController utilise le nouveau paramètre UISegmentControl. Ajoutez la ligne suivante dans la méthode *viewDidLoad* pour garantir l'affichage des paramètres régionaux actuellement sélectionnés :

        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

    Puis, dans votre méthode *subscribe*, modifiez votre appel à *storeCategoriesAndSubscribe* comme suit :

        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

3.  Enfin, vous devez mettre à jour la méthode *didRegisterForRemoteNotificationsWithDeviceToken* dans votre AppDelegate.m afin que vous puissiez correctement actualiser votre abonnement lorsque votre application démarre. Modifiez votre appel sur la méthode *subscribe* de notifications comme suit :

        NSSet* categories = [notifications retrieveCategories];
        int locale = [notifications retrieveLocale];
        [notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

## <a name="send"></a><span class="short-header">Envoi de notifications localisées</span>Envoi de notifications localisées à partir de votre serveur principal

[WACOM.INCLUDE [notification-hubs-localized-back-end][]]

## Étapes suivantes

Pour plus d'informations sur l'utilisation des modèles, consultez :

-   [Notification des utilisateurs avec Notification Hubs : ASP.NET][]
-   [Notification des utilisateurs avec Notification Hubs : Mobile Services][]
-   [Recommandations relatives à Notification Hubs][]

Une référence relative à la langue d'expression du modèle se trouve dans [Notification Hubs, procédure pour iOS][Service Bus Notification Hubs (applications iOS)].

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows Store C#]: /fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/ "Windows Store C#"
  [iOS]: /fr-fr/documentation/articles/notification-hubs-ios-send-localized-breaking-news/ "iOS"
  [Utilisation de Notification Hubs pour envoyer les dernières nouvelles]: /fr-fr/manage/services/notification-hubs/breaking-news-ios
  [Concepts de modèle]: #concepts
  [Interface utilisateur de l'application]: #ui
  [Création de l'application iOS]: #building-client
  [Envoi de notifications à partir de votre serveur principal]: #send
  [Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/fr-fr/library/jj927170.aspx
  [Service Bus Notification Hubs (applications iOS)]: http://msdn.microsoft.com/fr-fr/library/jj927168.aspx
  []: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
  [1]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png
  [notification-hubs-localized-back-end]: ../includes/notification-hubs-localized-back-end.md
  [Notification des utilisateurs avec Notification Hubs : ASP.NET]: /fr-fr/manage/services/notification-hubs/notify-users-aspnet
  [Notification des utilisateurs avec Notification Hubs : Mobile Services]: /fr-fr/manage/services/notification-hubs/notify-users
