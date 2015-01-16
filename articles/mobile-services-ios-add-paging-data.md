<properties urlDisplayName="Add paging to data" pageTitle="Ajout de la pagination de données (iOS) | Centre de développement mobile" metaKeywords="" description="Découvrez comment utiliser la pagination pour gérer la quantité de données renvoyées vers votre application iOS à partir de Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Affinage des requêtes Mobile Services au moyen de la pagination
W
[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Cette rubrique montre comment utiliser la pagination pour gérer la quantité de données renvoyée à votre application iOS par Azure Mobile Services. Dans le cadre de ce didacticiel, vous allez utiliser les propriétés de requête **fetchLimit** et **fetchOffset** sur le client pour demander des " pages " spécifiques de données.

<div class="dev-callout"><b>Remarque</b>
<p>Pour éviter tout dépassement de capacité dans les appareils mobiles clients, Mobile Services implémente une limite automatique du nombre de pages, qui autorise par défaut un maximum de 50 éléments par réponse. En spécifiant la taille de page, vous pouvez demander explicitement jusqu'à 1 000 éléments dans la réponse.</p>
</div>

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données]. Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données].

1. Dans Xcode, ouvrez le projet que vous avez modifié avec le didacticiel [Prise en main des données].

2. Appuyez sur le bouton **Exécuter** (Commande + R) pour générer le projet et démarrer l'application, puis tapez du texte dans la zone de texte et cliquez sur l'icône représentant un signe plus (  plus (**+**).

3. Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem.

4. Ouvrez le fichier QSTodoService.m et recherchez la méthode suivante :

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

5. Régénérez et démarrez l'application.

    Notez que seuls les trois premiers résultats de la table TodoItem sont affichés.

7. Mettez de nouveau à jour la méthode **refreshDataOnSuccess** en recherchant la ligne de code suivante :

        query.fetchOffset = 0;

   	Cette fois, affectez 3 à la valeur **query.fetchOffset**.

   	Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième " page " de données, dont la taille est de trois éléments.

    <div class="dev-callout"><b>Remarque</b>
    <p>Ce didacticiel utilise un scénario simplifié dans lequel les valeurs de pagination codées en dur sont définies pour les propriétés <strong>fetchOffset</strong> et <strong>fetchLimit</strong>. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'accéder aux pages précédentes et suivantes. Vous pouvez également définir   **query.includeTotalCount = YES** pour obtenir le nombre total d'éléments disponibles sur le serveur, avec les données paginées.</p>
    </div>

## <a name="next-steps"> </a>Étapes suivantes

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. Pour plus d'informations, consultez la rubrique Mobile Services suivante :

* [Prise en main de l'authentification]
  <br/>Découvrez comment authentifier les utilisateurs de votre application avec un compte Windows.

<!--
* [Get started with push notifications]
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.
-->

<!-- Anchors. -->

[Étapes suivantes]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-ios
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-ios
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-ios
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-ios

[Portail de gestion]: https://manage.windowsazure.com/
