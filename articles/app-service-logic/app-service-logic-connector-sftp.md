<properties
	pageTitle="Utilisation du connecteur SFTP dans des applications logiques | Microsoft Azure App Service"
	description="Comment créer et configurer le connecteur SFTP ou une application API et l'utiliser dans une application logique d’Azure App Service"
	authors="anuragdalmia"
	manager="erikre"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="sameerch"/>

# Prise en main du connecteur SFTP et ajout de celui-ci à votre application logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques. Pour la version de schéma 2015-08-01-preview, cliquez sur [API SFTP](../connectors/create-api-sftp.md).

Utilisez le connecteur SFTP pour déplacer des données vers ou depuis un serveur SFTP. Vous pouvez télécharger des fichiers dans les deux sens et obtenir la liste des fichiers sur un serveur SFTP.

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. Vous pouvez ajouter le connecteur SFTP à votre flux de travail professionnel et traiter les données dans le cadre de ce flux de travail dans une application logique.

## Création d’un connecteur SFTP pour votre application logique ##
Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Recherchez « Connecteur SFTP », sélectionnez-le et sélectionnez **Créer**.
3. Configurez le connecteur SFTP comme suit : ![][1]
	- **Emplacement**: choisissez l’emplacement géographique dans lequel vous voulez déployer le connecteur.
	- **Abonnement**: choisissez un abonnement dans lequel vous souhaitez créer ce connecteur.
	- **Groupe de ressources**: sélectionnez ou créez un groupe de ressources où doit résider le connecteur.
	- **Plan d’hébergement web**: sélectionnez ou créez un plan d’hébergement web.
	- **Niveau de tarification**: choisissez un niveau de tarification pour le connecteur.
	- **Nom**: indiquez le nom de votre connecteur SFTP.
	- **Paramètres du package**
		- **Adresse du serveur**: indiquez le nom ou l’adresse IP du serveur SFTP
		- **Accepter toute clé d’hôte de serveur SSH**: détermine si n’importe quelle clé d’hôte publique SSH issue du serveur doit être acceptée. Si ce paramètre est désactivé, la clé d’hôte est comparée à la clé spécifiée dans la propriété « Empreinte de la clé d’hôte du serveur SSH ».
		- **Clé d’hôte du serveur SSH**: spécifiez l’empreinte de la clé d’hôte publique du serveur SSH (*facultatif*).
		- **Dossier racine**: indiquez le chemin d’accès du dossier racine. Si rien n’est indiqué, le dossier racine sera indiqué par défaut.
		- **Chiffrement**: indiquez le chiffrement (*facultatif*).
		- **Port du serveur**: spécifiez le numéro de port du serveur SFTP.
4. Cliquez sur Créer. Un connecteur SFTP est créé.

5. Accédez à l’application API tout juste créée en sélectionnant Parcourir -> API Apps -> <Name of the API App just created>. Le composant « Sécurité » apparaît comme non configuré : ![][2]
6. Cliquez sur le composant « Sécurité » pour configurer la sécurité (Nom d’utilisateur, Mot de passe, Clé privée, Mot de passe du fichier PPK) du connecteur SFTP. Sélectionnez l’onglet d’autorisation « Mot de passe », « Clé privée » ou « Multifacteur » dans Sécurité et spécifiez les propriétés requises : ![][3] ![][4] ![][5]  
6. Une fois la configuration de sécurité enregistrée, vous pouvez créer une application logique dans le même groupe de ressources pour utiliser le connecteur SFTP.

## Utilisation du connecteur SFTP dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur SFTP comme déclencheur ou action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le même groupe de ressources qui contient le connecteur SFTP : ![][6]
2.	Ouvrez « Déclencheurs et actions » pour ouvrir le concepteur d’applications logiques et configurer votre flux : ![][7]
3.	Le connecteur SFTP s’affiche dans la section « Applications API dans ce groupe de ressources » de la galerie située à droite : ![][8]
4.	Vous pouvez déposer l’application API du connecteur SFTP dans l’éditeur en cliquant sur « Connecteur SFTP ».

5.	Vous pouvez maintenant utiliser le connecteur SFTP dans le flux. Vous pouvez utiliser le fichier récupéré du déclencheur SFTP (« TriggerOnFileAvailable ») dans d’autres actions du flux.

	> [AZURE.IMPORTANT] Le déclencheur SFTP « TriggerOnFileAvailable » supprime le fichier récupéré après le traitement de celui-ci.

6.	Configurez les propriétés d’entrée du déclencheur SFTP comme suit :

	- **Chemin du dossier**: spécifiez le chemin du dossier dans lequel récupérer les fichiers.
	- **Type du fichier : texte ou binaire**: sélectionnez le type du fichier.
	- **Masque de fichiers**: spécifiez le masque à appliquer pour récupérer les fichiers. '*' récupère tous les fichiers dans le dossier spécifié.
	- **Masque de fichiers à exclure**: spécifiez le masque à appliquer pour exclure des fichiers. Si la propriété « Masque de fichiers » est également définie, la propriété « Masque de fichiers à exclure » est appliquée en premier.


	![][9]  
	![][10]

7.	De même, vous pouvez utiliser les actions SFTP dans le flux. Vous pouvez utiliser l’action « Charger le fichier » pour charger un fichier sur le serveur SFTP. Configurez les propriétés d’entrée de l’action « Charger le fichier » comme suit :

	- **Contenu**: spécifie le contenu du fichier à télécharger.
	- **Encodage de transfert de contenu**: spécifiez none ou base64.
	- **Chemin du fichier**: spécifiez le chemin du fichier à charger.
	- **Remplacer**: spécifiez « true » pour remplacer le fichier existant.
	- ****Ajouter à la fin si existant **: spécifiez « true » ou « false ». Lorsque vous spécifiez « true », les données sont ajoutées à la fin du fichier (s’il existe). Lorsque vous spécifiez « false », le fichier est remplacé (s’il existe).
	- **Dossier temporaire**: si cette propriété est définie, l’adaptateur charge le fichier dans le « Chemin du dossier temporaire ». Une fois le chargement terminé, le fichier est déplacé vers le dossier spécifié dans « Chemin du dossier ». Le Chemin du dossier temporaire doit résider sur le même disque physique que le Chemin du dossier pour que l’opération de déplacement soit rapide. Le dossier temporaire n’est utilisable que lorsque la propriété Ajouter à la fin si existant est désactivée.

	![][11]  
	![][12]

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure Logic Apps avant d'ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-sftp/img1.PNG
[2]: ./media/app-service-logic-connector-sftp/img2.PNG
[3]: ./media/app-service-logic-connector-sftp/img3.PNG
[4]: ./media/app-service-logic-connector-sftp/img4.PNG
[5]: ./media/app-service-logic-connector-sftp/img5.PNG
[6]: ./media/app-service-logic-connector-sftp/img6.PNG
[7]: ./media/app-service-logic-connector-sftp/img7.png
[8]: ./media/app-service-logic-connector-sftp/img8.png
[9]: ./media/app-service-logic-connector-sftp/img9.PNG
[10]: ./media/app-service-logic-connector-sftp/img10.PNG
[11]: ./media/app-service-logic-connector-sftp/img11.PNG
[12]: ./media/app-service-logic-connector-sftp/img12.PNG

<!---HONumber=AcomDC_0224_2016-->