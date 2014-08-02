<properties linkid="develop-mobile-tutorials-add-paging-to-data-ios" urlDisplayName="Add paging to data" pageTitle="Add paging to data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your iOS app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="" solutions="" manager="" editor="" />

Affinage des requêtes Mobile Services au moyen de la pagination
===============================================================

> [AZURE.SELECTOR-LIST (Platform | Backend )]
>  - [(Windows Store C\# | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data.md)
>  - [(Windows Store C\# | JavaScript)](mobile-services-windows-store-dotnet-add-paging-data.md)
>  - [(Windows Store JavaScript | .NET)](mobile-services-dotnet-backend-windows-store-javascript-add-paging-data.md)
>  - [(Windows Store JavaScript | JavaScript)](mobile-services-windows-store-javascript-add-paging-data.md)
>  - [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-add-paging-data)
>  - [(Windows Phone | JavaScript)](mobile-services-windows-phone-add-paging-data) 
>  - [(iOS | JavaScript)](mobile-services-ios-add-paging-data) 
>  - [(Android | JavaScript)](mobile-services-android-add-paging-data) 
>  - [(HTML | .NET)](mobile-services-html-add-paging-data) 
>  - [(Xamarin iOS | .NET)](partner-xamarin-mobile-services-ios-add-paging-data) 
>  - [(Xamarin Android | .NET)](partner-xamarin-mobile-services-android-add-paging-data)

Cette rubrique montre comment utiliser la pagination pour gérer la quantité de données renvoyée à votre application iOS par Azure Mobile Services. Dans le cadre de ce didacticiel, vous allez utiliser les méthodes de requête **fetchLimit** et **fetchOffset** sur le client pour demander des « pages » spécifiques de données.

**Remarque**

Pour éviter tout dépassement de capacité dans les appareils mobiles clients, Mobile Services implémente une limite automatique du nombre de pages, qui autorise par défaut un maximum de 50 éléments par réponse. En spécifiant la taille de page, vous pouvez demander explicitement jusqu'à 1 000 éléments dans la réponse.

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-ios). Avant de commencer ce didacticiel, vous devez effectuer au moins le premier didacticiel relatif à l'utilisation de séries de données, intitulé [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-ios).

1.  Dans Xcode, ouvrez le projet que vous avez modifié avec le didacticiel [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-ios).

2.  Appuyez sur le bouton **Exécuter** (Commande + R) pour générer le projet et démarrer l'application, puis tapez un texte dans la zone de texte et cliquez sur l'icône représentant un signe plus (**+**).

3.  Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem.

4.  Ouvrez le fichier QSTodoService.m et recherchez la méthode suivante :

         - (void)refreshDataOnSuccess:(QSCompletionBlock)completion

      Remplacez le corps de la méthode entière par le code suivant. 

         // Créez un prédicat qui recherche les éléments actifs dans lesquels complete a la valeur false
         NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

         // Récupérez l'instance MSQuery de MSTable avec le prédicat que vous venez de créer.
         MSQuery * query = [self.table queryWithPredicate:predicate];
            
         query.includeTotalCount = YES; // Demandez le nombre total d'éléments

         // Commencez par le premier élément et récupérez trois éléments seulement
         query.fetchOffset = 0;
         query.fetchLimit = 3;

         // Appelez l'instance MSQuery directement, au lieu d'utiliser les méthodes d'assistance de MSTable.
         [query readWithCompletion:^(NSArray *results, NSInteger totalCount, NSError *error) {
                
             [self logErrorIfNotNil:error];
             if (!error)
             {
                 // Enregistrez le nombre total.
                 NSLog(@"Total item count: %@",[NSString stringWithFormat:@"%zd", (ssize_t) totalCount]);            
             }
            
             items = [results mutableCopy];
            
             // Informez l'appelant de la fin de l'opération
             completion();
         }];

     Cette requête renvoie les trois premiers éléments qui ne sont pas marqués comme terminés. 

5.  Régénérez et démarrez l'application.

    Notez que seuls les trois premiers résultats de la table TodoItem sont affichés.

6.  Mettez de nouveau à jour la méthode **refreshDataOnSuccess** en recherchant la ligne de code suivante :

         query.fetchOffset = 0;

      Cette fois, définissez la valeur **query.fetchOffset** sur 3. 

      Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième « page » de données, dont la taille est de trois éléments.

    **Remarque**

    Ce didacticiel utilise un scénario simplifié dans lequel les valeurs de pagination codées en dur sont définies pour les propriétés **fetchOffset** et **fetchLimit**. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'accéder aux pages précédentes et suivantes. Vous pouvez également définir **query.includeTotalCount = YES** pour obtenir le nombre total d'éléments disponibles sur le serveur, avec les données paginées.

Étapes suivantes
----------------

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. Pour plus d'informations, consultez la rubrique Mobile Services suivante :

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-ios)
    <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

