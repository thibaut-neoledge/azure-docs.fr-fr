<properties 
   pageTitle="Connecteur d'objet blob de stockage Azure" 
   description="Prise en main du connecteur d'objet blob de stockage Azure" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/18/2015"
   ms.author="rajram"/>
   
# Connecteur d'objet blob de stockage Azure

## Vue d'ensemble
Le connecteur d'objet blob de stockage Azure vous permet de télécharger et supprimer des objets blob dans un conteneur d'objets blob.

## Créer un connecteur d'objet blob de stockage Azure
Pour créer un nouveau connecteur de stockage Azure, procédez comme indiqué ci-dessous.
- Lancer le portail Azure
- Ouvrez Azure Marketplace à l'aide de +Nouveau (au bas de la page) -> Web+Mobile --> Azure Marketplace.

![Launch Azure Marketplace][1]
- Cliquez sur API Apps.
- Recherchez _Blob_, puis sélectionnez le connecteur d'objet blob de stockage Azure.

![Select Azure Storage Blob Connector][2]
- Cliquez sur Créer.
- Dans le panneau du connecteur d'objet blob de stockage Azure qui s'ouvre, fournissez les données suivantes.

![Create Azure Storage Blob Connector][3]

- **Emplacement** : choisissez l'emplacement géographique dans lequel vous voulez déployer le connecteur.
- **Abonnement** : choisissez un abonnement dans lequel vous voulez que ce connecteur soit créé.
- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources dans lequel doit résider le connecteur.
- **Plan d'hébergement web** : sélectionnez ou créez un plan d'hébergement web.
- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
- **Nom** : donnez un nom à votre connecteur FTP.
- **Paramètres du package** 
	- **URI du conteneur/SAP** : spécifiez l'URI du conteneur d'objets blob. L'URI peut également inclure le SAS demander un jeton. Exemple http://storageaccountname.blob.core.windows.net/containername ou http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah
	- **Clé d'accès** : spécifiez une clé d'accès de compte de stockage principal/secondaire valide. Laissez ce champ vide si vous utilisez un jeton SAP pour l'authentification.
- Cliquez sur Créer. Un nouveau connecteur d'objet blob de stockage Azure est créé.

## Utiliser le connecteur d'objet blob de stockage Azure dans une application logique
Une fois le connecteur d'objet blob de stockage Azure créé, il peut être consommé à partir du flux.

Créez un flux via +Nouveau -> Web+Mobile -> LogicApp. Fournissez les métadonnées pour le flux, y compris le groupe de ressources.

![Create Logic App][4]

Cliquez sur  *triggers and actions*. Le concepteur de flux s'ouvre.

![Logic App empty flow designer][5]

Le connecteur d'objet blob de stockage Azure peut être utilisé en tant que déclencheur et action. 

### Déclencheur
Dans le concepteur de flux vide, cliquez sur le connecteur d'objet blob de stockage Azure dans le panneau droit de la galerie.

![Choose Blob Available Trigger][6]

Le connecteur d'objet blob de stockage Azure a un seul déclencheur : _BlobAvailable_. Ce déclencheur interroge les objets blob présents dans un conteneur donné. L'interrogation et le filtrage des objets blob au niveau du répertoire sont également pris en charge. Les objets blob sont supprimés du conteneur une fois sélectionnés.

Cliquez sur le déclencheur _BlobAvailable_.

![Basic inputs Blob Available Trigger][7]

Les entrées vous aident à configurer un chemin de dossier spécifique à interroger selon une fréquence planifiée. Les entrées de base sont :
- Fréquence : spécifie la fréquence de l'interrogation FTP.
- Intervalle : spécifie l'intervalle de la fréquence planifiée.
- Chemin d'accès du dossier : spécifie un chemin d'accès de dossier virtuel à interroger. Utilisez '.' pour le dossier de conteneur racine.
- Type de fichier : spécifie un masque de fichier à faire correspondre aux noms de fichiers blob.  Seuls les objets blob portant des noms de fichiers correspondant à ce masque sont inclus dans l'interrogation. Par défaut, tous les objets blob sont inclus.

Cliquez sur ... pour voir les entrées avancées. 

![Advanced inputs Blob Available Trigger][8]

Les entrées avancées sont :

- Masque de fichier : spécifie un masque de fichier à faire correspondre aux noms de fichiers blob.  Seuls les objets blob portant des noms de fichiers correspondant à ce masque sont inclus dans l'interrogation. Par défaut, tous les objets blob sont inclus.
- Masque de fichier à exclure : spécifie un masque de fichier à faire correspondre aux noms de fichiers blob.  Les objets blob correspondant à ce masque de fichier sont exclus. Par défaut, aucun objet blob n'est exclu.

Indiquez les entrées et cliquez sur la coche pour terminer leur configuration.

![Configured Blob Available Trigger][9]

Notez que le déclencheur _Blob Available_ présente à la fois des paramètres d'entrée configurés, ainsi que des paramètres de sortie. 

Une fois l'application logique créée, le déclencheur _Blob Available_ : 


- recherche de nouveaux fichiers dans le chemin du dossier ;
- instancie le flux logique à chaque nouveau fichier ;
- supprime le fichier du chemin du dossier une fois le flux logique instancié.

#### Utilisation de la sortie du déclencheur _Blob Available_ dans les actions consécutives
La sortie du déclencheur _Blob Available_ peut servir d'entrée à certaines autres actions dans le flux. 

Vous pouvez cliquer sur + dans la boîte de dialogue d'entrée d'une action et sélectionner directement la sortie FTP dans la zone de liste déroulante.

Vous pouvez également écrire une expression directement dans la zone d'entrée de l'action. L'expression du flux à référencer dans la sortie du déclencheur est donnée ci-dessous.

	@triggers().outputs.body.Content

### Actions
Cliquez sur le connecteur d'objet blob de stockage Azure dans le panneau droit. Le connecteur répertorie les actions prises en charge.

![List of Azure Storage Blob Actions][10]

Le connecteur d'objet blob de stockage Azure prend en charge quatre actions. Les voici :

- **Obtenir un objet blob** : obtenir un objet blob particulier dans le conteneur.
- **Télécharger un objet blob** : télécharger un nouvel objet blob ou mettre à jour un existant.
- **Supprimer un objet blob** : supprimer un objet blob spécifique d'un conteneur.
- **Répertorier des objets blob** : répertorie tous les objets blob dans un répertoire.
- **Créer un instantané d'un objet blob** : créer un instantané en lecture seule d'un objet blob spécifique.
- **Copier un objet blob** : créer un objet blob en copiant un autre objet blob.  L'objet blob source peut être dans le même compte ou dans un autre compte.

Prenons un exemple : Télécharger un objet blob. Cliquez sur Télécharger un objet blob.

![Inputs of Upload Blob action][11]


- **Chemin de l'objet blob** : spécifie le chemin de l'objet blob à télécharger.  Le chemin est interprété par rapport au chemin du conteneur configuré.
- **Contenu d'écriture de l'objet blob** : spécifie le contenu et les propriétés de l'objet blob à télécharger.
- **Encodage de transfert de contenu** : spécifie none ou Base64.
- **Remplacer** : si la valeur est true, l'objet blob existant est remplacé. Sinon, une erreur est retournée si un objet blob existe déjà dans le même chemin.

Indiquez les entrées et cliquez sur la coche pour terminer leur configuration.


Notez que l'action de téléchargement de l'objet blob de stockage Azure configurée montre à la fois les paramètres d'entrée et les paramètres de sortie.

#### Utilisation des sorties des actions précédentes en tant qu'entrée de l'action FTP
Notez que dans la capture d'écran configurée, la valeur du contenu est définie par une expression.

	@triggers().outputs.body.Content


Vous pouvez définir n'importe quelle valeur souhaitée. Il s'agit simplement d'un exemple. L'expression prend la sortie du déclencheur de l'application logique et l'utilise comme contenu du fichier à télécharger. Supposons que vous vouliez utiliser la sortie d'une action précédente, par exemple l'action transform. Dans ce cas, l'expression serait :

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-azurestorageblob/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-azurestorageblob/SelectAzureStorageBlobConnector.PNG
[3]: ./media/app-service-logic-connector-azurestorageblob/CreateAzureStorageBlobConnector.PNG
[4]: ./media/app-service-logic-connector-azurestorageblob/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-azurestorageblob/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-azurestorageblob/ChooseBlobAvailableTrigger.PNG
[7]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsBlobAvailableTrigger.PNG
[8]: ./media/app-service-logic-connector-azurestorageblob/AdvancedInputsBlobAvailableTrigger.PNG
[9]: ./media/app-service-logic-connector-azurestorageblob/ConfiguredBlobAvailableTrigger.PNG
[10]: ./media/app-service-logic-connector-azurestorageblob/ListOfAzureStorageBlobActions.PNG
[11]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsUploadBlob.PNG

<!--HONumber=49-->