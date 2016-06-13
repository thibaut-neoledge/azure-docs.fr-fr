<properties
	pageTitle="Intégrer un serveur SAP local dans Azure App Service | Microsoft Azure"
	description="Découvrez comment intégrer un serveur SAP local"
	authors="rajeshramabathiran"
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
	ms.date="05/31/2016"
	ms.author="sameerch"/>


# Intégrer un serveur SAP local

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques.

Avec le [connecteur SAP](app-service-logic-connector-sap.md), vous pouvez connecter des applications web, mobiles et logiques Azure App Services à votre serveur SAP existant. Vous pouvez ainsi appeler des RFC, BAPI, tRFC, et envoyer des Idoc au serveur SAP, même s’il se trouve derrière votre pare-feu local.

Si vous disposez d’un serveur SAP local, utilisez un écouteur hybride pour établir la connectivité avec le connecteur SAP comme indiqué ci-après :

![Flux de connectivité hybride][1]

Bien qu’un connecteur SAP dans le cloud ne puisse pas se connecter directement à un serveur SAP derrière un pare-feu en local, vous pouvez utiliser l’écouteur hybride pour combler l’écart. Pour ce faire, vous hébergez un point de terminaison relais qui permet au connecteur d’établir une connectivité sécurisée avec le serveur SAP.


## Différentes façons d'intégrer SAP
Les actions suivantes sont prises en charge :

- Appel de RFC
- Appel de tRFC
- Appel de BAPI
- Envoi d'IDoc

## Configuration requise
Les bibliothèques clientes spécifiques à SAP doivent se trouver sur l'ordinateur client où l'écouteur hybride est installé et en cours d'exécution. Les détails précis sont indiqués dans la section intitulée **Pour l’adaptateur SAP** du [Guide d’installation du Pack adaptateurs BizTalk][9].


## Créer un connecteur SAP
1. Connectez-vous au portail Azure.
2. Sélectionnez **Nouveau**.
3. Dans le panneau de création, sélectionnez **Calculer** > **Azure Marketplace**.
4. Dans le panneau Marketplace, sélectionnez **API Apps** et recherchez SAP dans la barre de recherche : ![Application API du connecteur SAP][2]
5. Sélectionnez le **Connecteur SAP** publié par Microsoft.
6. Dans le panneau du connecteur SAP, sélectionnez **Créer**.
7. Dans le nouveau panneau qui s'ouvre, entrez les informations suivantes :  
	1. **Emplacement** : choisissez l’emplacement géographique où vous souhaitez déployer le connecteur.
	2. **Abonnement** : choisissez un abonnement dans lequel vous souhaitez créer ce connecteur.
	3. **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où doit résider le connecteur.
	4. **Plan d’hébergement web** : sélectionnez ou créez un plan d’hébergement web.
	5. **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
	6. **Nom** : entrez un nom pour votre connecteur SAP.
	7. **Paramètres du package**
		- **Nom du serveur** : entrez le nom du serveur SAP. Par exemple, « ServeurSAP » ou « ServeurSAP.mondomaine.com ».
		- **Nom d’utilisateur** : entrez un nom d’utilisateur valide pour vous connecter au serveur SAP.
		- **Mot de passe** : entrez un mot de passe pour vous connecter au serveur SAP.
		- **Numéro du système** : entrez le numéro du système du serveur d’applications SAP.
		- **Langue** : entrez la langue d’ouverture de session, par exemple « EN ». Si aucune valeur n'est entrée, « EN » est utilisé par défaut.
		- **Local** : indiquez si votre serveur SAP est local, derrière un pare-feu ou non. Si la valeur est TRUE, vous devez installer un agent écouteur sur un serveur qui peut accéder à votre serveur SAP. Pour installer cet agent, accédez à la page de résumé de votre application API et sélectionnez Connexion hybride.
		- **Chaîne de connexion Service Bus** : entrez ce paramètre si votre serveur SAP est local. Il doit s’agir d’une chaîne de connexion d’espace de noms Service Bus valide.
		- **RFC** : entrez les RFC dans SAP qui sont autorisés à être appelés par le connecteur.
		- **TRFC** : entrez les TRFC dans SAP qui sont autorisés à être appelés par le connecteur.
		- **BAPI** : entrez les BAPI dans SAP qui sont autorisés à être appelés par le connecteur.
		- **IDOC** : entrez les IDOC dans SAP qui peuvent être envoyés par le connecteur.
	8. Sélectionnez Sélectionner. Après quelques minutes, votre connecteur SAP est créé.


## Installer l'écouteur hybride
Accédez au connecteur SAP que vous avez créé via **Parcourir** > **API Apps** > *nom de votre connecteur*

Dans le panneau du connecteur, notez que l'état de la connexion hybride est En attente. Sélectionnez Connexion hybride. Le panneau Connexion hybride s'ouvre :

![Panneau Connexion hybride][3]

Copiez la chaîne de configuration de passerelle principale. Vous l’utiliserez ultérieurement dans le cadre de la configuration de l’écouteur hybride.

Sélectionnez le lien **Télécharger et configurer**. Le programme d’installation ClickOnce s’ouvre :

![Installation ClickOnce de la connexion hybride][4]

Sélectionnez **Installer**, puis entrez la chaîne de configuration de passerelle principale que vous avez copiée précédemment :

![Chaîne de connexion d’écoute du relais][5]

Sélectionnez **Installer** pour terminer l’installation du Gestionnaire de connexions hybrides :

![Installation du gestionnaire de connexion hybride en cours][6]

![Installation du gestionnaire de connexion hybride terminée][7]

## Valider la connexion hybride
Accédez au connecteur SAP que vous avez créé via **Parcourir** > **API Apps** > *nom de votre connecteur*

Dans le panneau du connecteur, notez que l’état de la connexion hybride est *En attente* :

![Connexion hybride - connectée][8]


## Utilisation du connecteur SAP dans des applications logiques
Une fois le connecteur SAP créé, vous pouvez l’utiliser dans votre flux de travail d’application logique. Pour ce faire, créez une application logique par le biais de **Nouveau** > **Applications logiques** > **Créer**. Entrez les métadonnées de l’application logique, y compris le groupe de ressources.

Sélectionnez **Déclencheurs et actions**. Le concepteur de flux de travail d’applications logiques s’ouvre.

Sélectionnez le connecteur SAP dans le volet de droite et sélectionnez une action sous l'onglet Actions.

> [AZURE.NOTE] La liste des actions est basée sur la configuration que vous avez entrée lors de la création du connecteur SAP.

Pour l'action sélectionnée, les paramètres d'entrée et de sortie sont affichés. Vous pouvez entrer les entrées de l'action et utiliser la sortie de l'action actuelle dans d'autres applications API, éventuellement pour prendre des décisions supplémentaires dans le flux de travail.

<!--Image references-->
[1]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectivityFlow.PNG
[2]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.APIApp.PNG
[3]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.PNG
[4]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.PNG
[5]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.RelayInformation.PNG
[6]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.InProgress.PNG
[7]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.Completed.PNG
[8]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.HybridConnection.Connected.PNG
[9]: http://www.microsoft.com/download/details.aspx?id=35552

<!---HONumber=AcomDC_0601_2016-->