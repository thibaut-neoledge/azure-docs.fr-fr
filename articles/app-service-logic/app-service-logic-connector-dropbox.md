<properties
	pageTitle="Utilisation du connecteur Dropbox dans votre application logique dans Azure App Service"
	description="Prise en main du connecteur Dropbox dans une application logique dans App Service"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2015"
	ms.author="sameerch"/>

# Connecteur Dropbox

Le connecteur Dropbox vous permet de télécharger des fichiers vers ou depuis votre compte Dropbox.

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux.

## Déclencheurs et actions

Un déclencheur démarre une nouvelle instance en fonction d'un événement spécifique, comme l'arrivée d'un nouveau message. Une action correspond au résultat : par exemple, après la réception d'un nouveau message, le message est téléchargé vers Dropbox.

Le connecteur Dropbox peut être utilisé comme une action dans une application logique et prend en charge les données aux formats JSON et XML. Le connecteur Chatter dispose des déclencheurs et actions suivants :

Déclencheurs | Actions
--- | ---
Aucun | <ul><li>Supprimer le fichier</li><li>Obtenir le fichier</li><li>Télécharger le fichier</li><li>Répertorier les fichiers</li></ul>


## Création d'un connecteur Dropbox pour votre application logique
Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Recherchez « Connecteur Dropbox », sélectionnez-le et sélectionnez **Créer**.
3. Entrez le nom, le plan App Service et d'autres propriétés :
	![][1]
	- **Emplacement** : choisissez l’emplacement géographique dans lequel vous voulez déployer le connecteur.
	- **Abonnement** : choisissez un abonnement dans lequel vous souhaitez créer ce connecteur.
	- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où doit résider le connecteur.
	- **Plan App Service** : sélectionnez ou créez un plan d'hébergement web.
	- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
	- **Nom** : indiquez le nom de votre connecteur Dropbox.  
4. Sélectionnez **Créer**.


## Utilisation du connecteur Dropbox dans votre application logique
Une fois votre application API créée, vous pouvez utiliser le connecteur Dropbox comme une action pour votre application logique. Pour ce faire :

1.	Dans votre application logique, ouvrez **Déclencheurs et actions** pour ouvrir le concepteur d'applications logiques et configurer votre flux :
	![][3]
2.	Le connecteur Dropbox est répertorié dans la galerie :
	![][4]
3.	Sélectionnez le connecteur Dropbox pour l'ajouter automatiquement dans le concepteur. Sélectionnez **Autoriser**, entrez vos informations d'identification, puis sélectionnez **Autoriser** :
	![][5]
	![][6]
	![][7]

Vous pouvez maintenant utiliser le connecteur Dropbox dans le flux. Vous pouvez utiliser l'action Dropbox « Télécharger le fichier » pour télécharger un fichier dans votre compte Dropbox :

	![][8]
	![][9]

Configurez les propriétés d’entrée de l’action « Charger le fichier » comme suit :

- **Chemin du fichier** : spécifiez le chemin de destination Dropbox du fichier à charger. Exemple : Photos/image.png
- **Contenu** : spécifie le contenu du fichier à télécharger. Souvent, cette opération s'effectue à partir d'une étape précédente de l'application logique.
- **Encodage de transfert de contenu** : spécifiez none ou base64.
- **Remplacer** : spécifiez « true » pour remplacer le fichier existant.

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-dropbox/img1.PNG
[2]: ./media/app-service-logic-connector-dropbox/img2.PNG
[3]: ./media/app-service-logic-connector-dropbox/img3.png
[4]: ./media/app-service-logic-connector-dropbox/img4.png
[5]: ./media/app-service-logic-connector-dropbox/img5.PNG
[6]: ./media/app-service-logic-connector-dropbox/img6.PNG
[7]: ./media/app-service-logic-connector-dropbox/img7.PNG
[8]: ./media/app-service-logic-connector-dropbox/img8.PNG
[9]: ./media/app-service-logic-connector-dropbox/img9.PNG

<!---HONumber=August15_HO8-->