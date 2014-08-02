<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Prise en main des données dans Mobile Services
==============================================

[Windows Store C#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

Cette rubrique montre comment utiliser Azure Mobile Services pour tirer parti des données dans une application iOS. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

**Remarque**

Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de récupérer des données à partir d'une application iOS à l'aide d'Azure. À cet effet, elle vous guide tout au long des nombreuses étapes exécutées automatiquement dans le démarrage rapide Mobile Services. Si vous n'avez aucune expérience de Mobile Services, nous vous invitons à suivre d'abord le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started-ios).

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement du projet d'application iOS](#download-app)
2.  [Création du service mobile](#create-service)
3.  [Ajout d'une table de données pour le stockage](#add-table)
4.  [Mise à jour de l'application pour utiliser Mobile Services](#update-app)
5.  [Test de l'application avec Mobile Services](#test-app)

Ce didacticiel nécessite le [Kit de développement logiciel (SDK) Mobile Services pour iOS](https://go.microsoft.com/fwLink/p/?LinkID=266533), [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) et iOS 5.0 ou ultérieur.

**Remarque**

Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F).

Téléchargement du projetTéléchargement du projet GetStartedWithData
-------------------------------------------------------------------

Ce didacticiel est basé sur l'[application GetStartedWithData](http://go.microsoft.com/fwlink/p/?LinkId=268622), qui est une application Android. L'interface utilisateur de cette application est identique à l'application générée par le démarrage IOS de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1.  Téléchargez l'[exemple d'application](http://go.microsoft.com/fwlink/p/?LinkId=268622) GetStartedWithData.

2.  Dans Xcode, ouvrez le projet téléchargé et étudiez le fichier QSTodoService.m.

   	Huit commentaires **// TODO** indiquent les étapes à suivre pour que cette application fonctionne avec votre service mobile.

3.  Appuyez sur le bouton **Exécuter** (ou Cmd+R) pour régénérer le projet et démarrer l'application.

4.  Dans l'application, tapez du texte dans la zone de texte, puis cliquez sur le bouton **+**.

   	![](./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png)

   	Le texte enregistré est affiché dans la liste ci-dessous.

Création d'un service mobileCréation d'un service mobile dans le portail de gestion
-----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Ajout d'une nouvelle tableAjout d'une nouvelle table au service mobile
----------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

Mise à jour de l'applicationMise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données
-------------------------------------------------------------------------------------------------------------------

Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale.

1.  Si vous n'avez pas encore installé le [Kit de développement logiciel (SDK) Mobile Services pour iOS](https://go.microsoft.com/fwLink/p/?LinkID=266533), faites-le maintenant.

2.  Dans l'Explorateur de projets dans Xcode, ouvrez les fichiers TodoService.m et TodoService.h situés dans le dossier Quickstart, puis ajoutez l'instruction import suivante :

         #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3.  Dans le fichier ToDoService.h, recherchez la ligne de code de commentaire suivante :

        // Créez un commentaire de propriété MSClient dans la déclaration #interface de TodoService. 

   	À la suite de ce commentaire, ajoutez la ligne de code suivante :

        @property (nonatomic, strong)   MSClient *client;

   	Cela permet de créer une propriété qui représente le client MSClient qui se connecte au service

4.  Dans le fichier TodoService.m, recherchez la ligne de code de commentaire suivante :

        // Créez une propriété MSTable pour tous les éléments. 

   	À la suite de ce commentaire, ajoutez la ligne de code suivante dans la déclaration @interface :

        @property (nonatomic, strong)   MSTable *table;

   	Cela permet de créer une représentation de propriété pour la table de votre service mobile.

5.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

6.  Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Manage keys** et notez la valeur de **Clé de l'application**.

   	![](./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png)

  	Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

1.  Revenez dans Xcode, ouvrez le fichier TodoService.m et recherchez la ligne de code de commentaire suivante :

         // Initialisez le client Mobile Service avec votre URL et clé.

    À la suite de ce commentaire, ajoutez la ligne de code suivante :

         self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    Cela permet de créer une instance du client Mobile Services.

2.  Remplacez les valeurs **APPURL** et **APPKEY** de ce code par l'URL et la clé d'application du service mobile que vous avez acquis à l'étape 6.

3.  Recherchez la ligne de code de commentaire suivante :

         // Créez une instance MSTable pour nous autoriser à utiliser la table TodoItem.

    À la suite de ce commentaire, ajoutez la ligne de code suivante :

         self.table = [self.client tableWithName:@"TodoItem"];

    Cela permet de créer l'instance de table TodoItem.

4.  Recherchez la ligne de code de commentaire suivante :

 	    // Créez un prédicat qui recherche les éléments pour lesquels complete a la valeur false dans la méthode refreshDataOnSuccess.

    À la suite de ce commentaire, ajoutez la ligne de code suivante :

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    Cela permet de créer une requête qui renvoie toutes les tâches non terminées.

1.  Recherchez la ligne de code de commentaire suivante :

        // Interrogez la table TodoItem et mettez à jour la propriété des éléments avec les résultats provenant du service.

	Remplacez ce commentaire et l'appel de bloc **completion** suivant par le code ci-dessous :

        // Interrogez la table TodoItem et mettez à jour la propriété des éléments avec les résultats provenant du service
        [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error) 
        {
           self.items = [results mutableCopy];
           completion();
        }]; 

1.  Recherchez la méthode **addItem**, puis remplacez le corps de la méthode par le code suivant :

        // Insérez l'élément dans la table TodoItem et ajoutez-le au tableau d'éléments à la fin de l'opération
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];
         
            // Informez l'appelant de la fin de l'opération
            completion(index);
        }];

    Ce code envoie une requête d'insertion au service mobile.

2.  Recherchez la méthode **completeItem**, puis remplacez le corps de la méthode par le code suivant :

        // Mettez à jour l'élément dans la table TodoItem table et supprimez-le du tableau d'éléments à la fin de l'opération
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // TODO
            // Actualisez l'index en cas de modification de la liste
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
        }]; 

	Ce code supprime les éléments TodoItems une fois ceux-ci terminés.

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.

Test de l'applicationTest de l'application avec votre nouveau service mobile
----------------------------------------------------------------------------

1.  Dans Xcode, sélectionnez un émulateur vers lequel effectuer le déploiement (via iPhone ou iPad), appuyez sur le bouton **Exécuter** (ou Cmd+R) pour régénérer le projet et démarrer l'application.

   	Cela permet d'exécuter votre client Azure Mobile Services, généré à partir du Kit de développement logiciel (SDK) pour iOS, qui effectue des requêetes sur les éléments auprès de votre service mobile.

2.  Comme précédemment, tapez du texte dans la zone de texte, puis cliquez sur le bouton **+**.

   	Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3.  Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur le service mobile.

4.  Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

   	![](./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png)
  
   	La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

Cela conclut le didacticiel **Prise en main des données** pour iOS.

Étapes suivantes
----------------

Ce didacticiel a présenté les bases de l'activation d'une application iOS pour utiliser les données dans Mobile Services.

Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Validation et modification des données avec des scripts](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination](/en-us/develop/mobile/tutorials/add-paging-to-data-ios)
    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez ces autres didacticiels iOS :

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-ios)
    En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-ios)
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application avec Mobile Services.


