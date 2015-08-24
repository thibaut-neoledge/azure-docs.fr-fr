<properties 
	pageTitle="Prise en main de Mobile Analytics | Microsoft Azure" 
	description="Prenez en main Mobile Analytics." 
	documentationCenter="ios" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="mahender"/>

# Prise en main de Mobile Analytics (Capptain)

Dans ce didacticiel, vous allez ajouter des capacités analytiques à votre application avec [Capptain]. Mobile Analytics vous permet de déterminer comment les utilisateurs interagissent avec votre application et quelles sont les sections qui génèrent le plus d'activité. Pour commencer à obtenir ces données, vous allez associer à votre application le Kit de développement logiciel (SDK) Capptain.


>[AZURE.NOTE]Propriété de Microsoft, Capptain.com propose des analyses pour les applications mobiles gratuitement jusqu'à 100 000 utilisateurs actifs mensuels pour les clients d'Azure Mobile Services niveau Standard. Si vous souhaitez tirer parti de cette offre, contactez-nous à l'adresse mobileservices@microsoft.com pour obtenir d'autres instructions. Le didacticiel ci-dessous récapitule les fonctionnalités et les possibilités de Capptain.com, et fournit des instructions pour leur utilisation.


Ce didacticiel vous familiarise avec ces étapes de base :

1. [Initialisation du Kit de développement logiciel (SDK) Capptain]
2. [Surcharge d'UIViewController]

Ce didacticiel requiert les éléments suivants :

* un compte [Capptain] ;
* une application [Mobile Services niveau Standard].

## <a name="initialize"></a>Initialisation du Kit de développement logiciel (SDK) Capptain

1. Accédez à la page **Détails de l'application** de votre application enregistrée dans Capptain. Sélectionnez l'onglet SDK et téléchargez le package.

2. Dans XCode, ajoutez le Kit de développement logiciel (SDK) Capptain à votre projet en cliquant avec le bouton droit sur votre projet et en sélectionnant « Ajouter des fichiers à... ». Choisissez le dossier CapptainSDK.

3. Sélectionnez votre projet. Sous l'onglet **Créer des phases**, sélectionnez **Lier binaire avec bibliothèques** et ajoutez les infrastructures suivantes :
    * AdSupport.framework - définissez ce lien comme facultatif
    * SystemConfiguration.framework
    * CoreTelephony.framework
    * CFNetwork.framework
    * CoreLocation.framework
    * libxml2.dylib

    >[AZURE.NOTE]L'infrastructure AdSupport peut être supprimée. Capptain en a besoin pour collecter l'IDFA. Il est cependant possible de désactiver la collecte de l'IDFA pour se conformer à la politique Apple concernant cet ID.

4. Dans le fichier d'implémentation du délégué de l'application, importez l'agent Capptain.


        #import "CapptainAgent.h"


5. Dans `applicationDidFinishLaunching:` ou `application:didFinishLaunchingWithOptions:`, initialisez l'agent Capptain en transmettant `registerapp:identifiedby:` avec votre identifiant d'application et votre clé SDK.

         - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [CapptainAgent registerApp:@"YOUR_APPID" identifiedBy:@"YOUR_SDK_KEY"];
          [...]
        }

## <a name="instrument"></a>Surcharge d'UIViewController

1. Recherchez tous les enfants de `UIViewController` dans votre projet et assurez-vous que chacun hérite plutôt de `CapptainViewController`.

        #import <UIKit/UIKit.h>
        #import "CapptainViewController.h"

        // formerly @interface Tab1ViewController : UIViewController<UITextFieldDelegate>
        @interface Tab1ViewController : CapptainViewController<UITextFieldDelegate> {
          UITextField* myTextField1;
          UITextField* myTextField2;
        }

        @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
        @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

2. Recherchez tous les enfants de `UITableViewController` dans votre projet et assurez-vous que chacun hérite plutôt de `CapptainTableViewController`.

    Votre application est maintenant configurée pour envoyer les données d'analyse à Capptain.

## Étapes suivantes
Si vous voulez en savoir plus les possibilités offertes par Capptain pour votre application, consultez le site [http://www.capptain.com](http://www.capptain.com)

<!-- Anchors. -->
[Initialisation du Kit de développement logiciel (SDK) Capptain]: #initialize
[Surcharge d'UIViewController]: #instrument


<!-- URLs. -->
[Capptain]: http://www.capptain.com
[Mobile Services niveau Standard]: /pricing/details/mobile-services/
 

<!---HONumber=August15_HO7-->