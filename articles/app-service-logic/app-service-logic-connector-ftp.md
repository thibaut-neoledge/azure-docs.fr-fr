<properties
	pageTitle="Utilisation du connecteur FTP dans des applications logiques | Microsoft Azure App Service"
	description="Comment créer et configurer le connecteur FTP ou une application API et l'utiliser dans une application logique d’Azure App Service"
	authors="rajram"
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
	ms.date="03/16/2016"
	ms.author="rajram"/>

# Utilisation et ajout du connecteur FTP à votre application logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques. Pour la version de schéma 2015-08-01-preview, cliquez sur [API FTP](../connectors/connectors-create-api-ftp.md).

Connectez-vous à un serveur FTP pour transférer des données ou des fichiers. Les principales fonctionnalités du connecteur FTP sont :

- Extraction de fichiers du serveur FTP à la demande
- Exécution d'interrogations basées sur une planification configurable
- Interrogation du serveur FTP et déclenchement du flux logique en fonction des nouveaux documents sur le serveur FTP
- Spécification du serveur FTP en tant qu'adresse IP, port, mot de passe et nom d'hôte
- Possibilité d'exécuter des envois à la demande
- Possibilité de supprimer des fichiers sur le serveur FTP à la demande

Vous pouvez ajouter le connecteur FTP à votre flux d’entreprise et traiter les données dans le cadre de ce flux de travail dans une application logique.

## Créer un connecteur FTP
Pour créer un connecteur FTP, procédez comme indiqué ci-dessous.
- Lancer le portail Azure
- Ouvrez Azure Marketplace à l’aide de +Nouveau (au bas de la page) -> Web + mobile --> Azure Marketplace : ![Lancez Azure Marketplace][1]

- Cliquez sur API Apps.
- Recherchez FTP et sélectionnez le connecteur FTP : ![Sélectionnez le connecteur FTP][2]

- Cliquez sur Créer.
- Dans le panneau du connecteur FTP qui s'ouvre, fournissez les données suivantes : ![Créez le connecteur FTP][3]

- **Emplacement** : choisissez l’emplacement géographique dans lequel vous voulez déployer le connecteur.
- **Abonnement** : choisissez un abonnement dans lequel vous souhaitez créer ce connecteur.
- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où doit résider le connecteur.
- **Plan d’hébergement web** : sélectionnez ou créez un plan d’hébergement web.
- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
- **Nom** : donnez un nom à votre connecteur FTP.
- **Paramètres du package**
	- **Adresse du serveur** : spécifiez le nom ou l’adresse IP du serveur FTP.
	- **Nom d’utilisateur** : spécifiez le nom d’utilisateur pour la connexion au serveur FTP.
	- **Mot de passe** : spécifiez le mot de passe pour la connexion au serveur FTP.
	- **Dossier racine** : indiquez le chemin du dossier racine.
	- **Utiliser le mode binaire** : spécifiez true pour utiliser le mode de transfert binaire, false pour ASCII.
	- **Utiliser SSL** : spécifiez true pour utiliser FTP sur un canal SSL/TLS sécurisé.
	- **Port du serveur** : spécifiez le numéro de port du serveur FTP.
- Cliquez sur Créer. Un nouveau connecteur FTP est créé.

## Utiliser le connecteur FTP dans une application logique
Une fois le connecteur FTP créé, il peut être consommé à partir du flux.

Créez un flux via +Nouveau -> Web+Mobile -> LogicApp. Fournissez les métadonnées pour le flux, y compris le groupe de ressources : ![Créer une application logique][4]

Cliquez sur *Déclencheurs et actions*. Le concepteur de flux s'ouvre : ![Concepteur de flux d’application logique vide][5]

Le connecteur FTP peut être utilisé en tant que déclencheur et action.

### Déclencheur
Dans le concepteur de flux vide, cliquez sur le connecteur FTP dans le panneau droit de la galerie : ![Cliquez sur Déclencheur FTP][6]

Le connecteur FTP a un déclencheur - « File Available (Read then Delete) ». Ce déclencheur :

- recherche de nouveaux fichiers dans le chemin du dossier ;
- instancie le flux logique à chaque nouveau fichier ;
- supprime le fichier du chemin du dossier une fois le flux logique instancié.

Cliquez sur le déclencheur « File Available (Read then Delete) »: ![Entrées de base du déclencheur FTP][7]

Les entrées vous aident à configurer un chemin de dossier spécifique à interroger selon une fréquence planifiée. Les entrées de base sont :
- Fréquence : spécifie la fréquence de l'interrogation FTP.
- Intervalle : spécifie l'intervalle de la fréquence planifiée.
- Chemin du dossier : spécifie le chemin du dossier sur le serveur FTP.
- Type de fichier : spécifie si le type de fichier est binaire ou texte.

Cliquez sur les ellipses « ... » pour voir les entrées avancées : ![Entrées de base du déclencheur FTP][8]

Les entrées avancées sont :
- Masque de fichier : spécifie le masque de fichier pendant l'interrogation.
- Masque de fichier à exclure : spécifie un masque de fichier à exclure pendant l'interrogation.

Indiquez les entrées et cochez la case pour terminer leur configuration : ![Entrées de base du déclencheur FTP][9]

Notez que le déclencheur FTP configuré présente à la fois des paramètres d'entrée configurés ainsi que des paramètres de sortie.

#### Utilisation de la sortie du déclencheur FTP dans les actions consécutives
La sortie du déclencheur FTP peut servir d'entrée à certaines autres actions dans le flux.

Vous pouvez cliquer sur « ... » dans la boîte de dialogue d'entrée d'une action et sélectionner directement la sortie FTP dans la zone de liste déroulante.

Vous pouvez également écrire une expression directement dans la zone d'entrée de l'action. L'expression du flux à référencer dans la sortie du déclencheur FTP est donnée ci-dessous :

	@triggers('ftpconnector').outputs.body.Content

### Actions
Cliquez sur le connecteur FTP dans le panneau droit. Le connecteur FTP répertorie les actions prises en charge : ![Liste des actions FTP][10]

Le connecteur FTP prend en charge les actions suivantes :

- **Obtenir un fichier** : obtient le contenu d’un fichier spécifique.
- **Télécharger un fichier** : télécharge un fichier vers le chemin du dossier FTP.
- **Supprimer un fichier** : supprime un fichier du chemin du dossier FTP.
- **Répertorier les fichiers** : répertorie tous les fichiers dans le chemin du dossier FTP.

Prenons un exemple : Télécharger un fichier. Cliquez sur Télécharger un fichier.

Les entrées de base sont affichées en premier : ![Entrées de base de l’action Télécharger un fichier][11]


- **Contenu** : spécifie le contenu du fichier à télécharger.
- **Encodage de transfert de contenu** : spécifiez none ou base64.
- **Chemin du fichier** : spécifie le chemin du fichier à télécharger.

Cliquez sur ... pour voir les entrées avancées : ![Entrées de base de l’action Télécharger un fichier][12]


- **Ajouter si le fichier existe** : True ou False. Quand cette entrée est activée, les données sont ajoutées au fichier s'il existe. Quand elle est désactivée, le fichier est remplacé s'il existe.
- **Dossier temporaire** : facultatif. Si cette propriété est définie, l’adaptateur charge le fichier dans le Chemin du dossier temporaire. Une fois le chargement terminé, le fichier est déplacé vers le dossier spécifié dans Chemin du dossier. Le Chemin du dossier temporaire doit résider sur le même disque physique que le Chemin du dossier pour que l’opération de déplacement soit rapide. Le dossier temporaire n’est utilisable que lorsque la propriété Ajouter à la fin si existant est désactivée.

Indiquez les entrées et cochez la case pour terminer leur configuration : ![Action de télécharger un fichier configurée][13]

Le paramètre « Chemin d'accès du fichier » est défini sur :

	@concat('/Output/',triggers().outputs.body.FileName)

Notez que l'action de téléchargement de fichier FTP configurée montre à la fois les paramètres d'entrée et les paramètres de sortie.

#### Utilisation des sorties des actions précédentes en tant qu'entrée de l'action FTP
Notez que dans la capture d'écran configurée, la valeur du contenu est définie par une expression.

	@triggers().outputs.body.Content


Vous pouvez définir n'importe quelle valeur souhaitée. Il s'agit simplement d'un exemple. L'expression prend la sortie du déclencheur de l'application logique et l'utilise comme contenu du fichier à télécharger. Supposons que vous vouliez utiliser la sortie d'une action précédente, par exemple l'action transform. Dans ce cas, l'expression serait :

	@actions('transformservice').outputs.body.OutputXML

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure Logic Apps avant d'ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-ftp/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-ftp/SelectFTPConnector.PNG
[3]: ./media/app-service-logic-connector-ftp/CreateFTPConnector.PNG
[4]: ./media/app-service-logic-connector-ftp/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-ftp/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-ftp/ChooseFTPTrigger.PNG
[7]: ./media/app-service-logic-connector-ftp/BasicInputsFTPTrigger.PNG
[8]: ./media/app-service-logic-connector-ftp/AdvancedInputsFTPTrigger.PNG
[9]: ./media/app-service-logic-connector-ftp/ConfiguredFTPTrigger.PNG
[10]: ./media/app-service-logic-connector-ftp/ListOfFTPActions.PNG
[11]: ./media/app-service-logic-connector-ftp/BasicInputsUploadFile.PNG
[12]: ./media/app-service-logic-connector-ftp/AdvancedInputsUploadFile.PNG
[13]: ./media/app-service-logic-connector-ftp/ConfiguredUploadFile.PNG
 

<!---HONumber=AcomDC_0803_2016-->