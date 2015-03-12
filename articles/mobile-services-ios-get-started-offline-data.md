<properties 
	pageTitle="Utilisation de la synchronisation des données hors connexion dans Mobile Services (iOS) | Centre de développement mobile" 
	description="Découvrez comment utiliser Azure Mobile Services pour mettre en cache et synchroniser les données hors connexion dans votre application iOS" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="01/16/2015" 
	ms.author="krisragh,donnam"/>

# Prise en main de la synchronisation des données hors connexion dans Mobile Services

[AZURE.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]

Ce didacticiel traite de la fonctionnalité de synchronisation hors connexion de Mobile Services sur iOS. La synchronisation hors connexion permet aux utilisateurs finaux d'interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu'il n'existe aucune connexion réseau. Les modifications sont stockées dans une base de données locale. Une fois l'appareil de nouveau en ligne, ces modifications sont synchronisées avec le service distant.

La synchronisation hors connexion possède plusieurs utilisations potentielles :

* Améliorer la réactivité de l'application en mettant en cache les données de serveur localement sur l'appareil
* Rendre les applications résistantes à une connectivité réseau intermittente
* Permettre aux utilisateurs finaux de créer et de modifier des données même en l'absence d'accès au réseau, prenant ainsi en charge des scénarios avec une connectivité faible ou nulle
* Synchroniser des données sur plusieurs appareils et détecter des conflits lorsque le même enregistrement est modifié par deux appareils

>[AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'évaluation gratuite Azure</a>.

Si vous n'avez aucune expérience de Mobile Services, nous vous invitons à suivre d'abord le didacticiel [Prise en main de Mobile Services].

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Obtention de l'exemple d'application]
2. [Examen du code de synchronisation Mobile Services]
3. [Examen du modèle des données de base]
4. [Modification du comportement de synchronisation de l'application]
5. [Test de l'application]

## <a name="get-app"></a>Obtenir l'exemple d'application ToDo hors connexion

Dans le [référentiel d'exemples de Mobile Services sur GitHub], clonez le référentiel et ouvrez le projet [Exemple iOS hors connexion] dans Xcode.

### Kit de développement logiciel (SDK) Bêta
Pour ajouter la prise en charge hors connexion à une application existante, récupérez la dernière version du [Kit de développement logiciel (SDK) iOS Bêta](http://aka.ms/gc6fex).

## <a name="review-sync"></a>Examen du code de synchronisation Mobile Services

La synchronisation hors connexion d'Azure Mobile Services permet aux utilisateurs d'interagir avec une base de données locale quand le réseau n'est pas accessible. Pour pouvoir utiliser ces fonctionnalités dans votre application, vous initialisez le contexte de synchronisation `MSClient` et référencez un magasin local. Ensuite, vous référencez votre table par le biais de l'interface `MSSyncTable`.

Cette section décrit le code lié à la synchronisation hors connexion dans l'exemple.

1. Dans **QSTodoService.m**, vous notez que le type du membre `syncTable` est `MSSyncTable`. La synchronisation hors connexion utilise cette interface de table de synchronisation à la place de `MSTable`. Lorsqu'une table de synchronisation est utilisée, toutes les opérations vont au magasin local et sont synchronisées uniquement avec le service distant avec des opérations push et pull explicites.

    Pour obtenir une référence à une table de synchronisation, utilisez la méthode `syncTableWithName`. Pour supprimer la fonctionnalité de synchronisation hors connexion, utilisez plutôt `tableWithName`.

3. Avant de pouvoir effectuer des opérations de table, le magasin local doit être initialisé. Voici le code approprié dans la méthode `QSTodoService.init` :

        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
        
        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

    Cette opération crée un magasin local à l'aide de l'interface `MSCoreDataStore`, fournie dans le Kit de développement logiciel (SDK) Mobile Services. Vous pouvez aussi fournir un autre magasin local en implémentant le protocole `MSSyncContextDataSource`.

    Le premier paramètre de `initWithDelegate` est utilisé pour spécifier un gestionnaire de conflit. Étant donné que nous avons passé `nil`, nous allons récupérer le gestionnaire de conflits par défaut, qui échoue sur tous les conflits. Pour plus d'informations sur comment implémenter un gestionnaire de conflits personnalisé, consultez le didacticiel [Gestion des conflits liés à la prise en charge hors connexion de Mobile Services].

4. Les méthodes `pullData` et `syncData` effectuent l'opération de synchronisation réelle : `syncData` transmet d'abord les nouvelles modifications, puis appelle `pullData` pour obtenir les données à partir du service distant.

        -(void)syncData:(QSCompletionBlock)completion
        {
            // push all changes in the sync context, then pull new data
            [self.client.syncContext pushWithCompletion:^(NSError *error) {
                [self logErrorIfNotNil:error];
                [self pullData:completion];
            }];
        }

    Ensuite, la méthode `pullData` obtient de nouvelles données qui correspondent à une requête :

        -(void)pullData:(QSCompletionBlock)completion
        {
            MSQuery *query = [self.syncTable query];
            
            // Pulls data from the remote server into the local table.
            // We're pulling all items and filtering in the view
            // query ID is used for incremental sync
            [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
                [self logErrorIfNotNil:error];
                
                // Let the caller know that we have finished
                if (completion != nil) {
                    dispatch_async(dispatch_get_main_queue(), completion);
                }
            }];
        }   

    Dans `syncData`, nous avons d'abord appelé `pushWithCompletion` sur le contexte de synchronisation. Cette méthode est membre de `MSSyncContext` (plutôt que la table de synchronisation), car elle envoie par Push les modifications sur toutes les tables. Seuls les enregistrements qui ont été modifiés d'une certaine façon en local (par le biais d'opérations CUD) seront envoyés au serveur. Puis l'application d'assistance `pullData` est appelée, et appelle " MSSyncTable.pullWithQuery " pour récupérer les données distantes et les stocker dans la base de données locale. 

    Notez que dans cet exemple, l'opération push n'était pas strictement nécessaire. S'il existe des modifications en attente dans le contexte de synchronisation pour la table qui effectue une opération push, pull émet toujours d'abord un push. Mais si vous avez plus d'une table de synchronisation, il est préférable d'appeler explicitement push pour vous assurer que tout est cohérent dans les tables associées.

    La méthode `pullWithQuery` vous permet de spécifier une requête pour filtrer les enregistrements que vous souhaitez récupérer. Dans cet exemple, la requête récupère simplement tous les enregistrements de la table`TodoItem` distante.

    Le deuxième paramètre `pullWithQuery` est un ID de requête qui est utilisé pour la *synchronisation incrémentielle*. La synchronisation incrémentielle récupère uniquement les enregistrements modifiés depuis la dernière synchronisation, à l'aide de l'horodatage de l'enregistrement`UpdatedAt` (appelé `ms_updatedAt` dans le magasin local). L'ID de requête doit être une chaîne descriptive unique pour chaque requête logique de votre application. Pour désactiver la synchronisation incrémentielle, transmettez `nil` en tant qu'ID de la requête. Notez que cette opération peut ne pas être très efficace, dans la mesure où elle récupère tous les enregistrements de chaque opération d'extraction.

    >[AZURE.NOTE] Pour supprimer des enregistrements du magasin local de l'appareil lorsqu'ils ont été supprimés dans la base de données de votre service mobile, vous devez activer la fonction [Suppression réversible]. Sinon, votre application doit appeler périodiquement `MSSyncTable.purgeWithQuery` pour purger le magasin local.

5. Dans la classe `QSTodoService`, la méthode `syncData` est appelée après les opérations qui modifient les données, `addItem` et `completeItem`. Il est également appelé à partir de `QSTodoListViewController.refresh`, de sorte que l'utilisateur obtient les données les plus récentes chaque fois qu'il effectue le mouvement d'actualisation. L'application exécute également une synchronisation au démarrage, puisque `QSTodoListViewController.init` appelle `refresh`.

    Étant donné que `syncData` est appelée chaque fois que les données sont modifiées, cette application suppose que l'utilisateur est en ligne chaque fois qu'il modifie des données. Dans une autre section, nous mettrons à jour l'application afin que les utilisateurs puissent les modifier même lorsqu'ils sont hors connexion.

## <a name="review-core-data"></a>Examen du modèle des données de base

Lorsque vous utilisez un magasin de données de base hors connexion, vous devez définir certaines tables et certains champs dans le modèle de données. L'exemple d'application comprend déjà un modèle de données avec le format correct. Dans cette section, nous allons découvrir ces tables et comment elles sont utilisées.

- Ouvrez **QSDataModel.xcdatamodeld**. Quatre tables sont définies : trois sont utilisées par le Kit de développement logiciel (SDK) et la dernière par les éléments de la tâche :
      * MS_TableOperations : pour le suivi des éléments qui doivent être synchronisés avec le serveur.
      * MS_TableOperationErrors : pour le suivi des erreurs qui se produisent lors de la synchronisation hors connexion. 
      * MS_TableConfig : pour le suivi de la dernière mise à jour de la dernière opération de synchronisation pour toutes les opérations d'extraction.
      * TodoItem : pour le stockage des actions. Les colonnes système **ms_createdAt**, **ms_updatedAt** et **ms_version** sont des propriétés système facultatives. 

>[AZURE.NOTE] Le Kit de développement logiciel (SDK) Mobile Services réserve les noms de colonnes commençant par **`ms_`**. Vous ne devez pas utiliser ce préfixe sur des objets autres que les colonnes système. Dans le cas contraire, vos noms de colonne seront modifiés lors de l'utilisation du service distant.

- Lorsque vous utilisez la fonctionnalité de synchronisation hors connexion, vous devez définir les tables système comme illustré ci-dessous.

    ### Tables système

    **MS_TableOperations**

    ![][defining-core-data-tableoperations-entity]

    | Attribut   |    Type     |
    |----------- |   ------    |
    | id         | Entier 64   |
    | itemId     | Chaîne      |
    | propriétés | Binaires    |
    | table      | Chaîne      |
    | tableKind  | Entier 16   |

    <br>**MS_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

    | Attribut   |    Type     |
    |----------- |   ------    |
    | id         | Chaîne      |
    | operationID | Entier 64  |
    | propriétés | Binaires    |
    | tableKind  | Entier 16   |

    <br>**MS_TableConfig**

    ![][defining-core-data-tableconfig-entity]

    | Attribut   |    Type     |
    |----------- |   ------    |
    | id         | Entier 64   |
    | key        | Chaîne      |
    | keyType    | Entier 64   |
    | table      | Chaîne      |
    | value      | Chaîne      |

    ### Table de données

    ![][defining-core-data-todoitem-entity]

    **TodoItem**

    | Attribut     |  Type   | Remarque                                                | 
    |-----------   |  ------ | -----------------------------------------------------------|
    | id           | Chaîne  | clé primaire dans le magasin distant                      |
    | complete     | Booléen | champ d'action                                        |
    | text         | Chaîne  | champ d'action                                         |
    | ms_createdAt | Date    | *(facultatif)* mappé sur la propriété système __createdAt  |
    | ms_updatedAt | Date    | *(facultatif)* mappé sur la propriété système __updatedAt  |
    | ms_version   | Chaîne  | *(facultatif)* sert à détecter les conflits, mappé sur __version |


## <a name="setup-sync"></a>Modification du comportement de synchronisation de l'application

Dans cette section, vous allez modifier l'application afin qu'elle ne se synchronise pas au démarrage ou lors de l'insertion et de la mise à jour des éléments, mais uniquement lorsque le mouvement d'actualisation est effectué. 

1. Dans **QSTodoListViewController.m**, modifiez la méthode **viewDidLoad** pour supprimer l'appel à [self refresh] à la fin de la méthode. Maintenant, les données ne seront pas synchronisées avec le serveur au démarrage de l'application, mais seront remplacées par le contenu du magasin local.

2. Dans **QSTodoService.m**, modifiez la définition de `addItem` afin que la synchronisation ne se fasse qu'une fois l'élément inséré. Supprimez le bloc `self syncData` et remplacez-le par le code suivant :

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Modifiez la définition de `completeItem` comme illustré ci-dessus. Supprimez le bloc de `self syncData` et remplacez-le par ce qui suit :

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

## <a name="test-app"></a>Test de l'application

Dans cette section, vous allez désactiver le Wi-Fi dans le simulateur pour créer un scénario hors connexion. Lorsque vous ajoutez des éléments de données, ils sont placés dans le magasin local des données de base, mais ne sont pas synchronisés vers le service mobile.

1. Désactivez le Wi-Fi dans le simulateur iOS.

2. Ajoutez des tâches ou marquez-en certaines comme terminées. Fermez le simulateur (ou forcez la fermeture de l'application) et redémarrez. Vérifiez que vos modifications ont bien été rendues persistantes.

3. Affichez le contenu de la table TodoItem distante :
   - Pour le service principal JavaScript, sur le portail de gestion, cliquez sur l'onglet Données pour afficher le contenu de la table `TodoItem`.
   - Pour le serveur principal .NET, affichez le contenu de la table avec un outil SQL, par exemple SQL Server Management Studio ou un client REST, comme Fiddler ou Postman.

    Vérifiez que les nouveaux éléments n'ont *pas* été synchronisés avec le serveur :

4. Activez le Wi-Fi dans le simulateur iOS, puis effectuez le geste d'actualisation en tirant la liste des éléments vers le bas. Vous verrez un compteur de progression et le texte " Synchronisation... ".

5. Affichez de nouveau les données TodoItem. Les nouveaux éléments TodoItems et ceux modifiés doivent maintenant s'afficher.

## Résumé

Pour pouvoir prendre en charge les fonctionnalités hors connexion des services mobiles, nous avons utilisé l'interface  `MSSyncTable` et initialisé  `MSClient.syncContext` avec un magasin local. Dans ce cas, le magasin local était une base de données avec données de base. 

Lorsque vous utilisez un magasin de données de base local, vous devez définir certaines tables avec les [propriétés système correctes][Examen du modèle des données de base].

Les opérations CRUD normales pour les services mobiles fonctionnent comme si l'application était toujours connectée, mais toutes les opérations ont lieu sur base du magasin local.

Lorsque nous avons voulu synchroniser le magasin local avec le serveur, nous avons utilisé les méthodes  `MSSyncTable.pullWithQuery` et  `MSClient.syncContext.pushWithCompletion`.

*  Pour transmettre par push les modifications au serveur, nous appelons `Examen du modèle des données de base`. Cette méthode est membre de  `MSSyncContext` à la place de la table de synchronisation, car elle envoie par Push les modifications sur toutes les tables.

    Seuls les enregistrements qui ont été modifiés d'une certaine façon en local (par le biais d'opérations CUD) seront envoyés au serveur.
   
* Pour envoyer par pull les données d'une table sur le serveur vers l'application, nous avons appelé  `MSSyncTable.pullWithQuery`.

    Une opération Pull émet toujours d'abord une opération Push. Cela a pour but de garantir que toutes les tables dans le magasin local, ainsi que les relations, restent cohérentes.

    Notez que `pullWithQuery` peut servir à filtrer les données stockées sur le client, en personnalisant le paramètre `query`. 

* Pour activer la synchronisation incrémentielle, transmettez un ID de requête à `pullWithQuery`. L'ID de requête est utilisé pour stocker le dernier horodatage des résultats de la dernière opération d'extraction. L'ID de requête doit être une chaîne descriptive unique pour chaque requête logique de votre application. Si la requête possède un paramètre, alors la même valeur de paramètre fait partie de l'ID de requête.

    Si vous souhaitez désactiver la synchronisation incrémentielle, transmettez `nill` en tant qu'ID de la requête. Dans ce cas, tous les enregistrements seront récupérés à chaque appel à `pullWithQuery`, ce qui est assez inefficace.

* Pour supprimer des enregistrements du magasin local de l'appareil lorsqu'ils ont été supprimés dans la base de données de votre service mobile, vous devez activer la fonction [Suppression réversible]. Sinon, votre application doit régulièrement appeler `MSSyncTable.purgeWithQuery` pour supprimer les enregistrements de la base de données locale, s'ils ont été supprimés dans le service distant.


## Étapes suivantes

* [Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]

* [Utilisation de la suppression réversible dans Mobile Services][Suppression réversible]

## Ressources supplémentaires

* [Couverture Cloud : synchronisation hors connexion dans Azure Mobile Services]

* [Azure Friday : Les applications prenant en charge le mode hors connexion dans Azure Mobile Services] \(Remarque : les démonstrations sont destinées à Windows, mais la discussion sur cette fonctionnalité s'applique à toutes les plateformes\)

<!-- URLs. -->

[Obtention de l'exemple d'application]: #get-app
[Examen du modèle des données de base]: #review-core-data
[Examen du code de synchronisation Mobile Services]: #review-sync
[Modification du comportement de synchronisation de l'application]: #setup-sync
[Test de l'application]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png

[Aide de l'éditeur de modèle de données de base]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Création d'une connexion d'outlet]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Création d'une interface utilisateur]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Ajout d'un segue entre des scènes dans un storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Ajout d'une scène à un storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Données de base]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Téléchargez le Kit de développement ici]: http://aka.ms/Gc6fex
[Utilisation de la bibliothèque cliente Mobile Services pour iOS]: /fr-fr/documentation/articles/mobile-services-ios-how-to-use-client-library/
[Exemple iOS hors connexion]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Référentiel d'exemples de Mobile Services sur GitHub]: https://github.com/Azure/mobile-services-samples

[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-ios-get-started/
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-ios-get-started-data/
[Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]: /fr-fr/documentation/articles/mobile-services-ios-handling-conflicts-offline-data/
[Suppression réversible]: /fr-fr/documentation/articles/mobile-services-using-soft-delete/

[Couverture Cloud : synchronisation hors connexion dans Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday : Applications compatibles avec la synchronisation hors connexion dans Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/


<!--HONumber=42-->
