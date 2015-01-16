<properties urlDisplayName="Add paging to data" pageTitle="Ajout de la pagination aux données (Xamarin Android) | Centre de développement mobile" metaKeywords="" description="Découvrez comment utiliser la pagination pour gérer la quantité de données renvoyées vers votre application Xamarin Android à partir de Mobile Services."  services="mobile-services" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="donnam" />

# Affinage des requêtes Mobile Services au moyen de la pagination

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]


Cette rubrique vous présente l'utilisation de la pagination pour gérer la quantité de données renvoyées vers votre application Xamarin.Android à partir d'Azure Mobile Services. Dans ce didacticiel, vous allez utiliser les méthodes de requête **Take** et **Skip** sur le client pour demander des " pages " spécifiques de données.

<div class="dev-callout"><b>Remarque</b>
<p>Pour éviter tout dépassement de capacité dans les appareils mobiles clients, Mobile Services implémente une limite automatique du nombre de pages, qui autorise par défaut un maximum de 50 éléments par réponse. En spécifiant la taille de page, vous pouvez demander explicitement jusqu'à 1 000 éléments dans la réponse.</p>
</div>

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données]. Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données]. 

1. Dans Xamarin Studio, ouvrez le projet que vous avez créé en suivant le didacticiel [Prise en main des données].

2. Cliquez sur **Exécuter** pour démarrer l'application, puis entrez un texte dans la zone de texte et cliquez sur le bouton **Ajouter**.

3. Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem. 

4. Dans le fichier **TodoActivity.cs**, remplacez la requête LINQ existante dans **RefreshItemsFromTableAsync** par la suivante :

		var list = await todoTable.Where(item => item.Complete == false)
						          .Take(3)
			                      .ToListAsync();

	  Cette requête renvoie les trois premiers éléments qui ne sont pas marqués comme terminés.

5. Régénérez et démarrez l'application. 
   
    Notez que seuls les trois premiers résultats de la table TodoItem sont affichés. 

6. (Facultatif) Affichez l'URI de la demande envoyée au service mobile en utilisant un logiciel d'inspection des messages, tel que les outils destinés aux développeurs de navigateurs ou [Fiddler]. 

   	La méthode `take(3)` a été convertie en option de requête `$top=3` dans l'URI de requête.

7. Mettez à jour la requête LINQ dans la méthode **RefreshItemsFromTableAsync** une nouvelle fois avec la requête suivante :
            
			var list = await todoTable.Where(item => item.Complete == false)
							          .Skip(3)
				                      .Take(3)
                 				      .ToListAsync();

   	Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième " page " de données, dont la taille est de trois éléments.

    <div class="dev-callout"><b>Remarque</b>
    <p>Ce didacticiel s'appuie sur un scénario simplifié dans lequel les valeurs de pagination codées en dur sont transmises aux méthodes <strong>Take</strong> et <strong>Skip</strong>. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'accéder aux pages précédentes et suivantes. Vous pouvez également appeler la méthode <strong>IncludeTotalCount</strong> pour obtenir le nombre de tous les éléments disponibles sur le serveur, ainsi que les données paginées.</p>
    </div>

8. (Facultatif) Là aussi, affichez l'URI de la requête envoyée au service mobile. 

   	Notez que la méthode `Skip(3)` a été convertie en option de requête `$skip=3` dans l'URI de requête.

## <a name="next-steps"> </a>Étapes suivantes

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. Pour plus d'informations sur les rubriques Mobile Services suivantes :

* [Prise en main de l'authentification]
  <br/>Découvrez comment authentifier les utilisateurs de votre application avec un compte Windows.
 
* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

<!-- Anchors. -->

[Étapes suivantes]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-xamarin-android
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Portail de gestion]: https://manage.windowsazure.com/

