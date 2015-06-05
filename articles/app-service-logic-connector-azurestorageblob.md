<properties 
   pageTitle="Connecteur d'objet blob de stockage Azure" 
   description="Prise en main du connecteur d'objet blob de stockage Azure" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
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
   
#Connecteur d'objet blob de stockage Azure

##Vue d'ensemble
Le connecteur d'objet blob de stockage Azure vous permet de télécharger et supprimer des objets blob dans un conteneur d'objets blob.

##Créer un connecteur d'objet blob de stockage Azure
Pour créer un nouveau connecteur de stockage Azure, procédez comme indiqué ci-dessous.
<ul>
<li>Lancez le portail Azure <li>Ouvrez Azure Marketplace à l’aide de +Nouveau (au bas de la page) -> Web+Mobile --> Azure Marketplace.
</ul>

![Lancez Azure Marketplace][1]<br>
<ul>
<li>Cliquez sur Applications API
<li>Recherchez <i>Blob</i>, puis sélectionnez le connecteur d’objet blob de stockage Azure
</ul>

![Sélectionner le connecteur d’objet blob de stockage Azure][2]
<br>
<ul>
<li>Cliquez sur Créer
<li>Dans le panneau du connecteur d’objet blob de stockage Azure qui s’ouvre, fournissez les données suivantes.
</ul>

![Créer le connecteur d’objet blob de stockage Azure][3]

- **Emplacement** : choisissez l’emplacement géographique dans lequel vous voulez déployer le connecteur.
- **Abonnement** : choisissez un abonnement dans lequel vous souhaitez créer ce connecteur.
- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où doit résider le connecteur.
- **Plan d’hébergement web** : sélectionnez ou créez un plan d’hébergement web.
- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
- **Nom** : indiquez le nom de votre connecteur d’objet blob de stockage.
- **Paramètres du package** 
	- **URI du conteneur/SAP** : spécifiez l’URI du conteneur d’objets blob. L’URI peut également inclure le jeton SAP. Par exemple http://storageaccountname.blob.core.windows.net/containername ou http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah.
	- **Clé d’accès** : spécifiez une clé d’accès de compte de stockage principal/secondaire valide. Laissez ce champ vide si vous utilisez un jeton SAP pour l'authentification.
- Cliquez sur Créer. Un nouveau connecteur d'objet blob de stockage Azure est créé.

##Utiliser le connecteur d'objet blob de stockage Azure dans une application logique
Une fois le connecteur d'objet blob de stockage Azure créé, il peut être consommé à partir du flux.

Créez un flux via +Nouveau -> Web+Mobile -> LogicApp. Fournissez les métadonnées pour le flux, y compris le groupe de ressources.

![Créer une application logique][4]

Cliquez sur *Déclencheurs et actions*. Le concepteur de flux s'ouvre.

![Concepteur de flux d’application logique vide][5]

Le connecteur d’objet blob de stockage Azure peut être utilisé en tant qu’action.

###Actions
Cliquez sur le connecteur d'objet blob de stockage Azure dans le panneau droit. Le connecteur répertorie les actions prises en charge.

![Liste des actions de l’objet blob de stockage Azure][10]

Le connecteur d'objet blob de stockage Azure prend en charge quatre actions. Les voici :

- **Obtenir un objet blob** : obtenir un objet blob particulier dans le conteneur.
- **Télécharger un objet blob** : télécharger un nouvel objet blob ou mettre à jour un existant.
- **Supprimer un objet blob** : supprimer un objet blob spécifique d’un conteneur.
- **Répertorier des objets blob** : répertorie tous les objets blob dans un répertoire.
- **Créer un instantané d’un objet blob** : créer un instantané en lecture seule d’un objet blob spécifique.
- **Copier un objet blob** : créer un objet blob en copiant un autre objet blob. L'objet blob source peut être dans le même compte ou dans un autre compte.

Prenons un exemple : Télécharger un objet blob. Cliquez sur Télécharger un objet blob.

![Entrées de l’action de téléchargement de l’objet blob][11]


- **Chemin d’accès d’objet blob** : spécifie le chemin de l’objet blob à télécharger. Le chemin est interprété par rapport au chemin du conteneur configuré.
- **Contenu d’écriture de l’objet blob** : spécifie le contenu et les propriétés de l’objet blob à télécharger.
- **Encodage de transfert de contenu** : spécifiez none ou Base64.
- **Remplacer** : si la valeur est true, l’objet blob existant est remplacé. Sinon, une erreur est retournée si un objet blob existe déjà dans le même chemin.

Indiquez les entrées et cliquez sur la coche pour terminer leur configuration.


Notez que l'action de téléchargement de l'objet blob de stockage Azure configurée montre à la fois les paramètres d'entrée et les paramètres de sortie.

####Utilisation des sorties des actions précédentes en tant qu’entrée des actions de l’objet blob de stockage Azure
Notez que dans la capture d'écran configurée, la valeur du contenu est définie par une expression.

	@triggers().outputs.body.Content


Vous pouvez définir n'importe quelle valeur souhaitée. Il s'agit simplement d'un exemple. L'expression prend la sortie du déclencheur de l'application logique et l'utilise comme contenu du fichier à télécharger. Supposons que vous vouliez utiliser la sortie d'une action précédente, par exemple l'action transform. Dans ce cas, l'expression serait :

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

<!---HONumber=54-->