<properties pageTitle="Ajout de la pagination de données (HTML 5) | Centre de développement mobile" description="Découvrez comment utiliser la pagination pour gérer la quantité de données renvoyées vers votre application HTML à partir de Mobile Services." services="mobile-services" documentationCenter="" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"/>

# Affinage des requêtes Mobile Services au moyen de la pagination

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Cette rubrique montre comment utiliser la pagination pour gérer la quantité de données renvoyée à votre application HTML par Azure Mobile Services. Dans ce didacticiel, vous utiliserez les méthodes de requête **Take** et **Skip** sur le client pour demander des " pages " spécifiques de données.

> [AZURE.NOTE] Pour éviter tout dépassement de capacité dans les appareils mobiles clients, Mobile Services implémente une limite automatique du nombre de pages, qui autorise par défaut un maximum de 50 éléments par réponse. En spécifiant la taille de page, vous pouvez demander explicitement jusqu'à 1 000 éléments dans la réponse.

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données]. Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données]. 

1. Exécutez l'un des fichiers de commandes suivants à partir du sous-dossier **server** du projet que vous avez modifié dans le didacticiel [Prise en main des données].

	+ **launch-windows** (Pour les ordinateurs Windows) 
	+ **launch-mac.command** (Pour les ordinateurs Mac OS X)
	+ **launch-linux.sh** (Pour les ordinateurs Linux)

	> [AZURE.NOTE] Sur un ordinateur Windows, appuyez sur la touche " R " lorsque PowerShell vous demande de confirmer l'exécution du script. Vous pouvez recevoir un avertissement de votre navigateur web vous recommandant de ne pas exécuter le script, car il a été téléchargé depuis Internet. Lorsque cela se produit, vous devez demander au navigateur de continuer à charger le script.

	Un serveur Web démarre sur votre ordinateur local pour héberger l'application.

1. Dans un navigateur Web, accédez à <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>, puis tapez du texte dans **Ajouter une nouvelle tâche** et cliquez sur **Ajouter**.

3. Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem. 

2. Dans le fichier app.js, remplacez la définition de la variable `query` dans la méthode **refreshTodoItems** par la ligne de code suivante :

       
        var query = todoItemTable.where({ complete: false }).take(3);

  	Cette requête, lorsqu'elle est exécutée, renvoie les trois premiers éléments marqués comme terminés.

3. Revenez au navigateur Web et rechargez la page.

  	Notez que seuls les trois premiers résultats de la table TodoItem sont affichés. 

4. (Facultatif) Affichez l'URI de la requête envoyée au service mobile en utilisant un logiciel d'inspection des messages, tel que les outils destinés aux développeurs de navigateurs ou [Fiddler]. 

   	Notez que la méthode **take(3)** a été convertie en option de requête **$top=3** dans l'URI de requête.

5. Mettez de nouveau à jour la requête avec le code suivant :
            
        var query = todoItemTable.where({ complete: false }).skip(3).take(3);

3. Revenez au navigateur Web et rechargez la page.

   	Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième " page " de données, dont la taille est de trois éléments.

    > [AZURE.NOTE] Ce didacticiel s'appuie sur un scénario simplifié dans lequel les valeurs de pagination codées en dur sont transmises aux méthodes **Take** et **Skip**. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'accéder aux pages précédentes et suivantes.  Vous pouvez également appeler la méthode **includeTotalCount** pour obtenir le nombre total d'éléments disponibles sur le serveur, avec les données paginées.

6. (Facultatif) Là aussi, affichez l'URI de la requête envoyée au service mobile. 

   	Notez que la méthode **skip(3)** a été convertie en option de requête **$skip=3** dans l'URI de requête.

## <a name="next-steps"> </a>Étapes suivantes

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. À présent, apprenez à authentifier les utilisateurs de votre application en consultant le didacticiel [Prise en main de l'authentification]. Obtenez plus d'informations sur Mobile Services avec HTML/JavaScript dans le [Guide de fonctionnement Mobile Services avec HTML/JavaScript]

<!-- Anchors. -->

[Étapes suivantes]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-html
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-html
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-html


[Portail de gestion]: https://manage.windowsazure.com/
[Guide de fonctionnement de Mobile Services avec HTML/JavaScript]: /fr-fr/develop/mobile/how-to-guides/work-with-html-js-client



<!--HONumber=42-->
