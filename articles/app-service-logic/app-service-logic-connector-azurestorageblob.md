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
	ms.date="08/19/2015"
	ms.author="rajram"/>
   
# Connecteur d'objet blob de stockage Azure
Connectez-vous à votre objet blob de stockage Azure pour télécharger et supprimer des objets blob dans le conteneur d’objets blob. Les connecteurs peuvent être utilisés dans les applications logiques dans le cadre d’un « flux de travail ».

## Déclencheurs et actions
Les *déclencheurs* sont des événements qui se produisent. Par exemple, lorsqu'une commande est mise à jour ou lorsqu'un nouveau client est ajouté. Une *action* est le résultat du déclencheur. Par exemple, lorsqu'une commande est mise à jour, envoyer une alerte au vendeur. Ou bien, lorsqu'un nouveau client est ajouté, lui envoyer un message de bienvenue.

Le connecteur d’objet blob de stockage peut être utilisé comme une action dans une application logique et prend en charge les données aux formats JSON et XML. Actuellement, il n’y a aucun déclencheur pour le connecteur d’objet blob de stockage.

Le connecteur d’objet blob de stockage propose les déclencheurs et les actions suivants :

Déclencheurs | Actions
--- | ---
Aucun | <ul><li>Get Blob : Obtenir un objet blob spécifique du conteneur</li><li>Upload Blob : télécharger un nouvel objet blob ou mettre à jour un objet blob existant</li><li>Delete Blob : supprimer un objet blob spécifique à partir d’un conteneur</li><li>List Blobs : répertorier tous les objets blob dans un répertoire</li><li>Snapshot Blob : créer un instantané en lecture seule d’un objet blob spécifique</li><li>Copy Blob : créer un nouvel objet blob en le copiant à partir d’un autre objet blob. L’objet blob source peut être dans le même compte ou dans un autre compte.</li></ul>


## Créer le connecteur d’objet blob de stockage Azure

Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Recherchez « Blob » : ![Sélectionner le connecteur d’objet blob de stockage Azure][2]

3. Sélectionnez-le, puis sélectionnez **Créer**.
4. Entrez le nom, le plan App Service et d'autres propriétés.
5. Entrez les paramètres de package suivants :

	Nom | Requis | Description
--- | --- | ---
Conteneur/URI SAS | Oui | Entrez l’URI du conteneur d’objets blob. L’URI peut également inclure le jeton SAP. Par exemple, indiquez http://*storageaccountname*.blob.core.windows.net/containername ou http://*storageaccountname*.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah
Clé d’accès | Non | Entrez une clé d’accès de compte de stockage primaire ou secondaire valide. Si vous utilisez un jeton SAP pour l’authentification, laissez ce champ vide.

	![Créer le connecteur d’objet blob de stockage Azure][3]

6. Cliquez sur **Create**.

## Utiliser le connecteur d'objet blob de stockage Azure dans une application logique
Une fois le connecteur d’objet blob de stockage Azure créé, il peut être ajouté à votre flux de travail.

1. Créez une application logique : Nouveau -> Web+Mobile -> LogicApp. Indiquez les propriétés de votre application logique : ![Créer une application logique][4]

2. Cliquez sur **Déclencheurs et actions**. Le concepteur de flux de travail s'ouvre : ![Concepteur de flux d’application logique vide][5]

3. Sélectionnez votre connecteur d’objet blob de stockage Azure dans le volet droit. Le connecteur répertorie les actions disponibles : ![Liste des actions de l’objet blob de stockage Azure][10]

4. Dans ce scénario, nous allons utiliser l'action **Télécharger un objet Blob** : ![Entrées de l’action de téléchargement de l’objet blob][11]

5. Indiquez les valeurs d’entrée et sélectionnez la case à cocher pour terminer la configuration :

	Entrée | Description
--- | ---
Chemin d'accès d'objet blob | Détermine le chemin d’accès de l’objet blob à télécharger. Le chemin est interprété par rapport au chemin du conteneur configuré.
Contenu en écriture de l’objet blob | Entrez le contenu et les propriétés de l’objet blob à télécharger.
Encodage de transfert de contenu | Tapez aucun ou Base64.
Remplacer | Lorsque la valeur est true, l’objet blob existant est remplacé. Lorsque la valeur est false, elle retourne une erreur si un objet blob existe déjà sur le même chemin d’accès.

Notez que l'action de téléchargement de l'objet blob de stockage Azure configurée montre à la fois les paramètres d'entrée et les paramètres de sortie.

#### Utilisation des sorties des actions précédentes en tant qu’entrée des actions de l’objet blob de stockage Azure
Dans la capture d'écran précédente, la valeur **Content** peut être une expression :

	@triggers().outputs.body.Content

Vous pouvez définir n'importe quelle valeur souhaitée. L'expression prend la sortie du déclencheur de l'application logique et l'utilise comme contenu du fichier à télécharger. Par exemple, vous souhaitez utiliser la sortie d’une transformation. Dans ce scénario, l’expression serait :

	@actions('transformservice').outputs.body.OutputXML

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Pour plus d'informations, consultez [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
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
 

<!---HONumber=August15_HO8-->