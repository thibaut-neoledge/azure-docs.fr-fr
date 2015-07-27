<properties
	pageTitle="Créer une application logique"
	description="Découvrez comment créer une application logique de base"
	authors="stepsic-microsoft-com"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/23/2015"
	ms.author="stepsic"/>

#Créer une application logique
Cette rubrique illustre comment se familiariser avec les [applications logiques App Services](app-service-logic-what-are-logic-apps.md) en quelques minutes. Nous allons suivre un flux de travail qui vous permet d'envoyer un ensemble de tweets qui vous intéressent vers un dossier Dropbox.

Pour activer ce scénario, vous aurez besoin des éléments suivants :

- Un abonnement Azure
- un compte Twitter ;
- un compte Dropbox.

<!--- TODO: Add try it now information here -->

##Obtention de vos connecteurs

Tout d'abord, vous devez créer les deux connecteurs que vous utiliserez : [**Connecteur Dropbox**](app-service-logic-connector-dropbox.md) et [**Connecteur Twitter**](app-service-logic-connector-twitter.md). Pour les créer :

0. Connectez-vous au portail Azure.

1. Cliquez sur [**Marketplace** ](https://portal.azure.com/#blade/HubsExtension/GalleryFeaturedMenuItemBlade/selectedMenuItemId/apiapps) dans l'écran d'accueil et recherchez **Twitter** (ou [cliquez ici](https://portal.azure.com/#create/microsoft_com.TwitterConnector.0.2.2)).

2. Sélectionnez Connecteur Twitter et cliquez sur le bouton Créer. Vous obtiendrez une vue pour tous vos paramètres. Vous pouvez conserver le nom **Connecteur Twitter**.

3. Tapez un nom de plan dans **Créer un plan App Service**.

	>[AZURE.NOTE]Les étapes de cette section partent du principe que vous créez un nouveau plan App Service. Si vous utilisez un plan App Service existant, cliquez sur **Sélectionner un plan existant**, sélectionnez votre plan existant, puis passez à la dernière étape de cette section. Vous avez besoin d'un plan pour héberger toutes vos applications.

4.  Sélectionnez un **Niveau de tarification** pour votre nouveau plan.

	>[AZURE.NOTE]Par défaut, seuls les plans recommandés pour les applications logiques sont affichés. Cliquez sur **Afficher tout** pour voir tous les plans disponibles. Quand vous exécutez une application logique au niveau Gratuit, vous pouvez uniquement l'exécuter toutes les heures et utiliser jusqu'à 1000 actions par mois.

5. Créez un **Groupe de ressources** pour votre flux.

	Les groupes de ressources agissent comme des conteneurs pour vos applications. Toutes les ressources pour votre application résideront dans le même groupe de ressources.

6. Si vous avez plusieurs abonnements Azure, choisissez celui que vous utiliserez.

7. Choisissez l’**Emplacement** où exécuter votre application logique.

	![Vue Créer une application API](./media/app-service-logic-create-a-logic-app/gallery.png)

8. Cliquez sur **Create**. L'étape de déploiement peut prendre une minute ou deux.

9. Répétez cette procédure avec [Dropbox](https://portal.azure.com/#create/microsoft_com.DropboxConnector.0.2.2).

##Démarrage de l'application logique

Maintenant, vous devez créer une application logique :

1. Cliquez sur le bouton **+ Nouveau** en bas à gauche de l’écran, développez **Web + Mobile**, puis cliquez sur **Application logique**.

 	Cela affiche la vue Créer une application logique, dans laquelle vous fournissez certains paramètres de base pour commencer.

	![Vue Créer une application logique](./media/app-service-logic-create-a-logic-app/createlogicapp.png)

2. Dans **Nom**, tapez un nom explicite pour votre application logique.

3. Choisissez le **Plan App Service** que vous avez utilisé lors de la création de vos connecteurs. L'emplacement, l'abonnement et le groupe de ressources doivent être choisis automatiquement pour vous.

Les paramètres de base sont définis, mais ne cliquez pas encore sur **Créer**. Ensuite, vous allez ajouter des déclencheurs et des actions à votre flux de travail.

## Ajout d'un déclencheur

Les déclencheurs sont les éléments qui permettent à votre application logique de s'exécuter. Vous allez maintenant ajouter un déclencheur de périodicité, qui démarre votre flux de travail selon une planification prédéfinie.

1. Toujours dans la vue **Créer une application logique**, cliquez sur **Déclencheurs et actions**.

	Cela affiche un concepteur plein écran qui affiche vos flux. Sur le côté droit se trouve une liste de tous les services qui peuvent avoir des déclencheurs.

2. Dans la section supérieure, cliquez sur **Périodicité**.

	Cela ajoute une zone où vous pouvez spécifier les paramètres de périodicité.

	![Périodicité](./media/app-service-logic-create-a-logic-app/recurrence.png)


4.  Choisissez une **Fréquence** et un **Intervalle** de périodicité (par exemple une fois par heure), puis cliquez sur la coche verte.

À présent, vous allez ajouter une action au flux.

## Ajout d'une action Twitter

Les actions correspondent à ce que fait votre flux de travail. Vous pouvez avoir un nombre quelconque d'actions et les organiser pour que les informations d'une action soient passées à la suivante.

1. Dans le volet de droite, recherchez **Connecteur Twitter**, puis cliquez dessus.


2. Une fois le connecteur chargé, cliquez sur le bouton **Autoriser**, connectez-vous à votre compte Twitter, puis cliquez sur **Autoriser l’application**.

	Ce paramètre accorde au connecteur l'accès à votre compte Twitter. Une liste d'opérations possibles fournies par le connecteur Twitter est affichée.

	![Actions](./media/app-service-logic-create-a-logic-app/actions.png)

3. Cliquez sur **Rechercher des tweets** puis, dans **Spécifier une requête**, tapez quelque chose comme `#MicrosoftAzure` et cliquez sur la coche verte.

	![Recherche Twitter](./media/app-service-logic-create-a-logic-app/twittersearch.png)

Le connecteur Twitter fait désormais partie du flux de travail.

## Ajout d'une action Dropbox et création de l'application

L'étape finale consiste à ajouter une action qui charge un tweet vers un fichier Dropbox.

1. Dans le volet de droite, cliquez sur **Connecteur Dropbox**.

2. Une fois la configuration terminée, cliquez sur le bouton **Autoriser**, connectez-vous à votre compte Dropbox, puis cliquez sur **Autoriser**.

	![Autoriser le connecteur Dropbox](./media/app-service-logic-create-a-logic-app/authorize.png)

	Ce paramètre accorde au connecteur l'accès à votre compte Dropbox. Une liste d'opérations possibles fournies par le connecteur Dropbox est affichée.

4. Cliquez sur **Télécharger un fichier**.

	Cela affiche les paramètres du connecteur Dropbox, que vous devez définir pour transmettre les données de la recherche Twitter vers Dropbox.

	![Dropbox](./media/app-service-logic-create-a-logic-app/dropbox.png)

3. Dans le champ **Chemin d’accès**, tapez `/tweet.txt`.

4. Dans le champ **Contenu**, cliquez sur le bouton `...`, puis sur l’option **Texte de tweet**.

	Ceci entre la valeur `@first(body('twitterconnector')).TweetText` dans la zone de texte. Cette valeur générée contient les éléments suivants :

	Partie de contenu | Description
	------------------------------------------ | ------------
	 `@` | Indique que vous entrez une fonction plutôt qu'une valeur réelle.
	`actions('twitterconnector').outputs.body` | Obtient les tweets qui ont été retournés par la requête du connecteur Twitter.
	`first()` | L'action de recherche de tweets retourne une liste, mais vous souhaitez uniquement télécharger un fichier.
	`.TweetText` | Sélectionne la propriété du message tweet.

5. Cliquez sur la coche verte pour enregistrer les paramètres du connecteur.

5. Maintenant que la conception est terminée, cliquez sur **Mode Code** en haut à gauche du concepteur. Notez qu’il s’agit du code JSON qui définit le flux de travail que vous venez de créer dans le concepteur. Nous examinerons ce code en détail dans la [rubrique suivante][Use logic app features].

6. Cliquez sur le bouton **OK** en bas de l’écran, puis sur le bouton **Créer**.

	Cela crée la nouvelle application logique.

## Gestion de votre application logique après la création

Votre application logique est maintenant opérationnelle. Chaque fois que le flux de travail planifié s’exécute, il vérifie la présence de tweets avec le hashtag spécifique. Quand il trouve un tweet correspondant, il le place dans votre dossier Dropbox. Pour finir, vous allez découvrir comment désactiver l'application ou analyser ses performances.

1. Cliquez sur **Parcourir** sur le côté gauche de l’écran et sélectionnez **Applications logiques**.

2. Cliquez sur la nouvelle application logique que vous venez de créer pour afficher l'état actuel et des informations générales.

3. Pour modifier votre nouvelle application logique, cliquez sur **Déclencheurs et actions**.

5. Pour désactiver l’application, cliquez sur **Désactiver** dans la barre de commandes.

En moins de cinq minutes, vous avez réussi à configurer une application logique simple exécutée dans le cloud. Pour en savoir plus sur l’utilisation des fonctionnalités des applications logiques, consultez [Utiliser les fonctionnalités des applications logiques]. Pour en savoir plus sur les définitions d'application logique, consultez la rubrique [Créer des définitions d'application logique](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Use logic app features]: app-service-logic-use-logic-app-features.md
[Utiliser les fonctionnalités des applications logiques]: app-service-logic-use-logic-app-features.md
 

<!---HONumber=July15_HO3-->