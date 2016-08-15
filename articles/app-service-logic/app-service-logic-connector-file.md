<properties
	pageTitle="Utilisation du connecteur File dans des applications logiques | Microsoft Azure App Service"
	description="Comment créer et configurer le connecteur File ou une application API et l’utiliser dans une application logique d’Azure App Service"
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""
	services="logic-apps"
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="rajram"/>

# Utilisation et ajout du connecteur File dans votre application logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques.

Connectez-vous à un système de fichiers pour, entre autres, charger, télécharger vos fichiers sur un ordinateur hôte. Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données. Vous pouvez ajouter le connecteur File à votre flux d’entreprise et traiter les données dans le cadre de ce flux de travail dans une application logique.

Le connecteur File utilise le Gestionnaire de connexion hybride pour la connectivité hybride vers le système de fichiers hôte.

## Création d’un connecteur File pour votre application logique ##
Pour utiliser le connecteur File, vous devez d’abord créer une instance de l’application API de ce connecteur. Pour cela, procédez comme suit :

1.	Ouvrez Azure Marketplace à l’aide de l’option + NOUVEAU située à gauche du portail Azure.
2.	Accédez à « Marketplace > Apps API » et recherchez « Connecteur File ».
3.	Configurez le connecteur File comme suit : ![][1]

	- **Nom** : indiquez le nom du connecteur File.
	- **Paramètres du package**
		- **Dossier racine** : indiquez le chemin du dossier racine sur l'ordinateur hôte. Par exemple : D:\\FileConnectorTest
		- **Chaîne de connexion au Service Bus** : indiquez la chaîne de connexion au Service Bus. Vérifiez que l'espace de noms du Service Bus est de type Standard et NON Basique pour permettre l'utilisation de Service Bus Relays. Service Bus Relay est utilisé pour se connecter au Gestionnaire de connexion hybride.
	- **Plan App Service** : sélectionnez ou créez un plan App Service.
	- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
	- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où doit résider le connecteur.
	- **Abonnement** : choisissez un abonnement dans lequel vous souhaitez créer ce connecteur.
	- **Emplacement** : choisissez l’emplacement géographique dans lequel vous voulez déployer le connecteur.

4. Cliquez sur Créer. Un connecteur File est créé.

## Configurer le Gestionnaire de connexion hybride ##
Une fois l'instance de l'application API créée, accédez à son tableau de bord. Pour ce faire, cliquez sur Parcourir > Applications API > sélectionnez l’application API du connecteur File. À ce stade, le Gestionnaire de connexion hybride doit être configuré. Pour plus d'informations sur la configuration et le dépannage du Gestionnaire de connexion hybride, consultez la rubrique [Utilisation du Gestionnaire de connexion hybride].

## Utilisation du connecteur File dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur File comme action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le groupe de ressources qui contient le connecteur File. Suivez les instructions indiquées dans la rubrique [Création d’une application logique].

2.	Ouvrez « Déclencheurs et actions » dans l’application logique créée pour ouvrir le Concepteur d’applications logiques et configurez votre flux.

3.	Le connecteur File s’affiche dans la section « Applications API dans ce groupe de ressources » de la galerie située à droite.

4.	Vous pouvez déposer l’application API du connecteur File dans l’éditeur en cliquant sur « Connecteur File ». Le connecteur File expose un déclencheur et 4 actions :![][5]

6.	Chacun de ces éléments a des propriétés. L’image ci-dessous montre les propriétés du déclencheur et de l’action « Obtenir le fichier » : ![][6]

7. Pour être utilisables dans votre flux, le déclencheur et l'action doivent être configurés. De même, d’autres actions peuvent être configurées.

> [AZURE.NOTE] Le déclencheur de fichier supprimera le fichier lorsque celui-ci sera correctement lu à partir du dossier.

## API REST du connecteur File ##
Pour utiliser le connecteur hors d’une application logique, vous devez faire appel aux API REST exposées par celui-ci. Pour afficher ces définitions d’API, sélectionnez Parcourir -> Application API -> Connecteur File. Maintenant, cliquez sur le filtre Définition des API sous la section Résumé pour afficher toutes les API exposées par ce connecteur : ![][7]

Vous trouverez les détails de l’API dans la rubrique [Définition des API du connecteur File].

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l’ajouter à un flux d’entreprise à l’aide d’une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure Logic Apps avant d’ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Création d’une application logique]: app-service-logic-create-a-logic-app.md
[Définition des API du connecteur File]: https://msdn.microsoft.com/library/dn936296.aspx
[Utilisation du Gestionnaire de connexion hybride]: app-service-logic-hybrid-connection-manager.md

<!---HONumber=AcomDC_0803_2016-->