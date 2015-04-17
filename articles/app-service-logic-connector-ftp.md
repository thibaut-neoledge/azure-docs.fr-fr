<properties 
	pageTitle="Connecteur FTP"
	description="Prise en main du connecteur FTP"
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
	ms.date="03/05/2015"
	ms.author="rajeshramabathiran"/>

# Connecteur FTP

## Vue d'ensemble
Le connecteur FTP vous permet de déplacer des données depuis/vers un serveur FTP. Les principales fonctionnalités du connecteur FTP sont :

- Extraction de fichiers du serveur FTP à la demande
- Exécution d'interrogations basées sur une planification configurable
- Interrogation du serveur FTP et déclenchement du flux logique en fonction des nouveaux documents sur le serveur FTP
- Spécification du serveur FTP en tant qu'adresse IP, port, mot de passe et nom d'hôte
- Possibilité d'exécuter des envois à la demande
- Possibilité de supprimer des fichiers sur le serveur FTP à la demande

## Créer un connecteur FTP
Pour créer un connecteur FTP, procédez comme indiqué ci-dessous.
- Lancer le portail Azure
- Ouvrez Azure Marketplace à l'aide de +Nouveau (au bas de la page) -> Web+Mobile --> Azure Marketplace.

![Launch Azure Marketplace][1]
- Cliquez sur API Apps.
- Recherchez FTP et sélectionnez le connecteur FTP.

![Select FTP Connector][2]
- Cliquez sur Créer.
- Dans le panneau du connecteur FTP qui s'ouvre, fournissez les données suivantes.

![Create FTP Connector][3]

- **Emplacement** : choisissez l'emplacement géographique dans lequel vous voulez déployer le connecteur.
- **Abonnement** : choisissez un abonnement dans lequel vous voulez que ce connecteur soit créé.
- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources dans lequel doit résider le connecteur.
- **Plan d'hébergement web** : sélectionnez ou créez un plan d'hébergement web.
- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
- **Nom** : donnez un nom à votre connecteur FTP.
- **Paramètres du package** 
	- **Adresse du serveur** : spécifiez le nom ou l'adresse IP du serveur FTP.
	- **Nom d'utilisateur** : spécifiez le nom d'utilisateur pour la connexion au serveur FTP.
	- **Mot de passe** : spécifiez le mot de passe pour la connexion au serveur FTP.
	- **Dossier racine** : spécifiez un chemin de dossier racine.
	- **Utiliser le mode binaire** : spécifiez true pour utiliser le mode de transfert binaire, false pour ASCII.
	- **Utiliser SSL** : spécifiez true pour utiliser FTP sur un canal SSL/TLS sécurisé.
	- **Port du serveur** : spécifiez le numéro de port du serveur FTP.
- Cliquez sur Créer. Un nouveau connecteur FTP est créé.

## Utiliser le connecteur FTP dans une application logique
Une fois le connecteur FTP créé, il peut être consommé à partir du flux.

Créez un flux via +Nouveau -> Web+Mobile -> LogicApp. Fournissez les métadonnées pour le flux, y compris le groupe de ressources.

![Create Logic App][4]

Cliquez sur  *triggers and actions*. Le concepteur de flux s'ouvre.

![Logic App empty flow designer][5]

Le connecteur FTP peut être utilisé en tant que déclencheur et action. 

### Déclencheur
Dans le concepteur de flux vide, cliquez sur le connecteur FTP dans le panneau droit de la galerie.

![Choose FTP Trigger][6]

Le connecteur FTP a un seul déclencheur : TriggerOnFileAvailable. Il se déclenche chaque fois qu'un nouveau fichier est téléchargé vers un dossier spécifique sur le serveur FTP.

Cliquez sur le déclencheur  'TriggerOnFileAvailable'.

![Basic inputs FTP Trigger][7]

Les entrées vous aident à configurer un chemin de dossier spécifique à interroger selon une fréquence planifiée. Les entrées de base sont :
- Fréquence : spécifie la fréquence de l'interrogation FTP.
- Intervalle : spécifie l'intervalle de la fréquence planifiée.
- Chemin du dossier : spécifie le chemin du dossier sur le serveur FTP.
- Type de fichier : spécifie si le type de fichier est binaire ou texte.

Cliquez sur ... pour voir les entrées avancées. 

![Basic inputs FTP Trigger][8]

Les entrées avancées sont :
- Masque de fichier : spécifie le masque de fichier pendant l'interrogation.
- Masque de fichier à exclure : spécifie un masque de fichier à exclure pendant l'interrogation.

Indiquez les entrées et cliquez sur la coche pour terminer leur configuration.

![Basic inputs FTP Trigger][9]

Notez que le déclencheur FTP configuré présente à la fois des paramètres d'entrée configurés, ainsi que des paramètres de sortie. 

Une fois l'application logique créée, le déclencheur FTP : 


- recherche de nouveaux fichiers dans le chemin du dossier ;
- instancie le flux logique à chaque nouveau fichier ;
- supprime le fichier du chemin du dossier une fois le flux logique instancié.

#### Utilisation de la sortie du déclencheur FTP dans les actions consécutives
La sortie du déclencheur FTP peut servir d'entrée à certaines autres actions dans le flux. 

Vous pouvez cliquer sur + dans la boîte de dialogue d'entrée d'une action et sélectionner directement la sortie FTP dans la zone de liste déroulante.

Vous pouvez également écrire une expression directement dans la zone d'entrée de l'action. L'expression du flux à référencer dans la sortie du déclencheur FTP est donnée ci-dessous.

	@triggers('ftpconnector').outputs.body.Content

### Actions
Cliquez sur le connecteur FTP dans le panneau droit. Le connecteur FTP répertorie les actions prises en charge.

![List of FTP Actions][10]

Le connecteur FTP prend en charge quatre actions. Les voici :

- **Obtenir un fichier** : obtient le contenu d'un fichier spécifique.
- **Télécharger un fichier** : télécharge un fichier vers le chemin du dossier FTP.
- **Supprimer un fichier** : supprime un fichier du chemin du dossier FTP.
- **Répertorier les fichiers** : répertorie tous les fichiers dans le chemin du dossier FTP.

Prenons un exemple : Télécharger un fichier. Cliquez sur Télécharger un fichier.

Les entrées de base sont affichées en premier.

![Basic inputs of Upload File action][11]


- **Contenu** : spécifie le contenu du fichier à télécharger.
- **Encodage de transfert de contenu** : spécifie none ou base64.
- **Chemin du fichier** : spécifie le chemin du fichier à télécharger.

Cliquez sur ... pour voir les entrées avancées.

![Basic inputs of Upload File action][12]


- **Ajouter si le fichier existe** : activez ou désactivez  'Append If Exist'. Quand cette entrée est activée, les données sont ajoutées au fichier s'il existe. Quand elle est désactivée, le fichier est remplacé s'il existe.
- **Dossier temporaire** : facultatif. S'il est fourni, l'adaptateur télécharge le fichier vers le  'Temporary Folder Path' et une fois que le téléchargement est effectué, le fichier est déplacé dans  'Folder Path'. Le  'Temporary Folder Path' doit se trouver sur le même disque physique que le  'Folder Path' pour garantir une opération de déplacement atomique. Le dossier temporaire peut être utilisé uniquement quand la propriété  'Append If Exist' est désactivée.

Indiquez les entrées et cliquez sur la coche pour terminer leur configuration.

![Configured Upload File action][13]

Notez que l'action de téléchargement de fichier FTP configurée montre à la fois les paramètres d'entrée et les paramètres de sortie.

#### Utilisation des sorties des actions précédentes en tant qu'entrée de l'action FTP
Notez que dans la capture d'écran configurée, la valeur du contenu est définie par une expression.

	@triggers().outputs.body.Content


Vous pouvez définir n'importe quelle valeur souhaitée. Il s'agit simplement d'un exemple. L'expression prend la sortie du déclencheur de l'application logique et l'utilise comme contenu du fichier à télécharger. Supposons que vous vouliez utiliser la sortie d'une action précédente, par exemple l'action transform. Dans ce cas, l'expression serait :

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-FTP/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-FTP/SelectFTPConnector.PNG
[3]: ./media/app-service-logic-connector-FTP/CreateFTPConnector.PNG
[4]: ./media/app-service-logic-connector-FTP/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-FTP/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-FTP/ChooseFTPTrigger.PNG
[7]: ./media/app-service-logic-connector-FTP/BasicInputsFTPTrigger.PNG
[8]: ./media/app-service-logic-connector-FTP/AdvancedInputsFTPTrigger.PNG
[9]: ./media/app-service-logic-connector-FTP/ConfiguredFTPTrigger.PNG
[10]: ./media/app-service-logic-connector-FTP/ListOfFTPActions.PNG
[11]: ./media/app-service-logic-connector-FTP/BasicInputsUploadFile.PNG
[12]: ./media/app-service-logic-connector-FTP/AdvancedInputsUploadFile.PNG
[13]: ./media/app-service-logic-connector-FTP/ConfiguredUploadFile.PNG

<!--HONumber=49-->