<properties linkid="develop-mobile-tutorials-add-paging-to-data-ios" urlDisplayName="Add paging to data" pageTitle="Add paging to data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your iOS app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Affinage des requêtes Mobile Services au moyen de la pagination

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS" class="current">iOS</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-android" title="Android" class="current">Android</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

Cette rubrique montre comment utiliser la pagination pour gérer la quantité de données renvoyée à votre application iOS par Azure Mobile Services. Dans le cadre de ce didacticiel, vous allez utiliser les méthodes de requête **fetchLimit** et **fetchOffset** sur le client pour demander des « pages » spécifiques de données.

<div class="dev-callout"><b>Remarque</b>
<p>Pour &eacute;viter tout d&eacute;passement de capacit&eacute; dans les appareils mobiles clients, Mobile Services impl&eacute;mente une limite automatique du nombre de pages, qui autorise par d&eacute;faut un maximum de 50&nbsp;&eacute;l&eacute;ments par r&eacute;ponse. En sp&eacute;cifiant la taille de page, vous pouvez demander explicitement jusqu'&agrave; 1&nbsp;000&nbsp;&eacute;l&eacute;ments dans la r&eacute;ponse.</p>
</div>

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données][Prise en main des données]. Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données][Prise en main des données].

1.  Dans Xcode, ouvrez le projet que vous avez modifié avec le didacticiel [Prise en main des données][Prise en main des données].

2.  Appuyez sur le bouton **Exécuter** (Commande + R) pour générer le projet et démarrer l'application, puis tapez un texte dans la zone de texte et cliquez sur l'icône représentant un signe plus (**+**).

3.  Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem.

4.  Ouvrez le fichier QSTodoService.m et recherchez la méthode suivante :

        - (void)refreshDataOnSuccess:(QSCompletionBlock)completion

    Remplacez le corps de la méthode entière par le code suivant.

        // Create a predicate that finds active items in which complete is false
        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

        // Retrieve the MSTable's MSQuery instance with the predicate you just created.
        MSQuery * query = [self.table queryWithPredicate:predicate];

        query.includeTotalCount = YES; // Request the total item count

        // Start with the first item, and retrieve only three items
        query.fetchOffset = 0;
        query.fetchLimit = 3;

        // Invoke the MSQuery instance directly, rather than using the MSTable helper methods.
        [query readWithCompletion:^(NSArray *results, NSInteger totalCount, NSError *error) {

            [self logErrorIfNotNil:error];
            if (!error)
            {
                // Log total count.
                NSLog(@"Total item count: %@",[NSString stringWithFormat:@"%zd", (ssize_t) totalCount]);            
            }

            items = [results mutableCopy];

            // Let the caller know that we finished
            completion();
        }];

    Cette requête renvoie les trois premiers éléments qui ne sont pas marqués comme terminés.

5.  Régénérez et démarrez l'application.

    Notez que seuls les trois premiers résultats de la table TodoItem sont affichés.

6.  Mettez de nouveau à jour la méthode **refreshDataOnSuccess** en recherchant la ligne de code suivante :

        query.fetchOffset = 0;

    Cette fois, définissez la valeur **query.fetchOffset** sur 3.

    Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième « page » de données, dont la taille est de trois éléments.

    <div class="dev-callout"><b>Remarque</b>
<p>Ce didacticiel utilise un sc&eacute;nario simplifi&eacute; dans lequel les valeurs de pagination cod&eacute;es en dur sont d&eacute;finies pour les propri&eacute;t&eacute;s <strong>fetchOffset</strong> et <strong>fetchLimit</strong>. Dans une application r&eacute;elle, vous pouvez utiliser des requ&ecirc;tes semblables &agrave; celles indiqu&eacute;es plus haut avec un contr&ocirc;le pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'acc&eacute;der aux pages pr&eacute;c&eacute;dentes et suivantes. Vous pouvez &eacute;galement d&eacute;finir **query.includeTotalCount = YES** pour obtenir le nombre total d'&eacute;l&eacute;ments disponibles sur le serveur, avec les donn&eacute;es pagin&eacute;es.</p>
</div>

## <a name="next-steps"> </a>Étapes suivantes

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. Pour plus d'informations, consultez la rubrique Mobile Services suivante :

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-ios
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-ios
