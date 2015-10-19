<properties 
	pageTitle="Utilisation de l’émulateur de stockage Azure pour le développement et le test | Microsoft Azure" 
	description="L’émulateur de stockage Azure fournit un environnement de développement local gratuit pour développer et tester sur Azure Storage. Découvrez plus en détail l’émulateur de stockage, notamment comment les demandes sont authentifiées, comment se connecter à l’émulateur à partir de votre application et comment utiliser l’outil en ligne de commande." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>
<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="tamram"/>

# Utilisation de l'émulateur de stockage Azure pour le développement et le test

## Vue d'ensemble

L'émulateur de stockage Microsoft Azure fournit un environnement local qui émule les services BLOB, de File d'attente et de Table Azure à des fins de développement. L’émulateur de stockage vous permet de tester localement votre application sur les services de stockage, sans souscrire d’abonnement Azure et sans frais. Lorsque vous êtes satisfait du fonctionnement de votre application dans l’émulateur, vous pouvez commencer à utiliser un compte de stockage Azure dans le cloud.

> [AZURE.NOTE]L’émulateur de stockage fait partie du [Kit de développement logiciel (SDK) Microsoft Azure](https://azure.microsoft.com/downloads/). Vous pouvez également installer l'émulateur de stockage en tant que package autonome. Pour configurer l'émulateur de stockage, vous devez disposer de privilèges d'administrateur sur l'ordinateur.
>  
> Notez qu'il n'est pas garanti que vous puissiez accéder aux données créées dans une version de l'émulateur de stockage à partir d'une autre version. Si vous devez rendre vos données persistantes à long terme, nous vous recommandons de stocker ces données dans un compte de stockage Azure plutôt que dans l'émulateur de stockage.

## Fonctionnement de l’émulateur de stockage
 
L’émulateur de stockage utilise une instance locale de Microsoft SQL Server et le système de fichiers local pour émuler les services de stockage Azure. Par défaut, l’émulateur de stockage s’appuie sur une base de données dans Microsoft SQL Server 2012 Express LocalDB. Vous pouvez configurer l’émulateur de stockage de sorte qu’il accède à une instance locale de SQL Server plutôt qu’à l’instance LocalDB. Pour plus d’informations, consultez la section [Démarrer et initialiser l’émulateur de stockage](#start-and-initialize-the-storage-emulator) ci-dessous.

Vous pouvez installer SQL Server Management Studio Express pour gérer votre installation de LocalDB. L'émulateur de stockage se connecte à SQL Server ou LocalDB par l'intermédiaire de l'authentification Windows.

Il existe quelques différences de fonctionnalités entre l’émulateur de stockage et les services de stockage Azure. Pour plus d’informations sur ces différences, consultez la section [Différences entre l’émulateur de stockage et Azure Storage](#differences-between-the-storage-emulator-and-azure-storage).

## Authentification des demandes auprès de l’émulateur de stockage

Tout comme avec Azure Storage dans le cloud, chaque demande que vous effectuez auprès de l’émulateur de stockage doit être authentifiée, sauf s’il s’agit d’une demande anonyme. Vous pouvez authentifier les demandes auprès de l’émulateur de stockage à l’aide de l’authentification par clé partagée ou d’une signature d’accès partagé (SAP).

### Authentification à l’aide d’informations d’identification de clé partagée

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Pour plus d’informations sur les chaînes de connexion, consultez la rubrique [Configuration d’une chaîne de connexion dans Azure Storage](storage-configure-connection-string.md).

### Authentification à l’aide d’une signature d’accès partagé 

Certaines bibliothèques clientes de stockage Azure, telles que la bibliothèque Xamarin, prennent uniquement en charge l’authentification par jeton de signature d’accès partagé (SAP). Vous devez créer ce jeton SAP à l’aide d’un outil ou d’une application qui prend en charge l’authentification par clé partagée. Azure PowerShell permet de facilement générer le jeton SAP :

1. Installez Azure PowerShell si ce n’est pas déjà fait. Il est recommandé d’utiliser la dernière version des applets de commande Azure PowerShell. Pour connaître la procédure d’installation, consultez l’article [Installation et configuration d’Azure PowerShell](../articles/powershell-install-configure.md#Install).

2. Ouvrez Azure PowerShell et exécutez les commandes suivantes. N’oubliez pas de remplacer *ACCOUNT\_NAME* et *ACCOUNT\_KEY==* par vos propres informations d’identification. Remplacez *￼CONTAINER\_NAME￼* par le nom de votre choix.

		$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
		
		New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
		
		$now = Get-Date 
		
		New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

L’URI de la signature d’accès partagé obtenue pour le nouveau conteneur doit être semblable à ce qui suit :

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

La signature d’accès partagé créée avec cet exemple est valide une journée. La signature accorde un accès complet (lecture, écriture, suppression et liste) aux objets blob du conteneur.

Pour plus d’informations sur les signatures d’accès partagé, consultez la page [Signatures d’accès partagé : présentation du modèle SAP](storage-dotnet-shared-access-signature-part-1.md)


## Démarrer et initialiser l’émulateur de stockage

Pour démarrer l’émulateur de stockage Azure, sélectionnez le bouton Démarrer ou appuyez sur la touche Windows. Commencez à taper **Émulateur de stockage Azure**, puis sélectionnez l’émulateur dans la liste des applications.

Lorsque l’émulateur est en cours d’exécution, une icône est affichée dans la zone de notification de la barre des tâches Windows.

Au démarrage de l’émulateur de stockage, une fenêtre de ligne de commande apparaît. Vous pouvez utiliser cette fenêtre de ligne de commande pour démarrer et arrêter l’émulateur de stockage, ainsi que pour effacer des données, obtenir l’état actuel et initialiser l’émulateur. Pour plus d’informations, consultez la section [Référence de l’outil en ligne de commande de l’émulateur de stockage](#storage-emulator-command-line-tool-reference).

Lorsque la fenêtre de ligne de commande est fermée, l'émulateur de stockage continue de s'exécuter. Pour faire réapparaître la ligne de commande, suivez les étapes ci-dessus comme si vous démarriez l'émulateur de stockage.

La première fois que vous exécutez l'émulateur de stockage, l'environnement de stockage local est initialisé pour vous. Le processus d'initialisation crée une base de données dans LocalDB et réserve des ports HTTP pour chaque service de stockage local.

L’émulateur de stockage est installé par défaut dans le répertoire C:\\Program Files(x86)\\Microsoft SDKs\\Windows Azure\\Storage Emulator\\.

### Initialiser l’émulateur de stockage de manière à utiliser une autre base de données SQL

Vous pouvez utiliser l’outil en ligne de commande de l’émulateur de stockage pour initialiser l’émulateur de stockage afin qu’il pointe vers une instance de base de données SQL autre que l’instance LocalDB par défaut. Notez que vous devez exécuter l’outil de ligne de commande avec des privilèges d’administration afin d’initialiser la base de données principale pour l’émulateur de stockage :

1. Cliquez sur le bouton **Démarrer** ou appuyez sur la touche **Windows**. Commencez à taper `Azure Storage Emulator` et sélectionnez le résultat correspondant lorsqu’il apparaît pour afficher l’outil en ligne de commande de l’émulateur de stockage.
2. Dans la fenêtre d’invites de commandes, tapez la commande suivante, où `<SQLServerInstance>` est le nom de l’instance SQL Server. Pour utiliser LocalDb, spécifiez `(localdb)\v11.0` comme instance SQL Server.

		AzureStorageEmulator init /server <SQLServerInstance> 
    
	Vous pouvez également exécuter la commande suivante, qui indique à l'émulateur d'utiliser l'instance SQL Server par défaut :

    	AzureStorageEmulator init /server .\\ 

	En guise d’alternative, vous pouvez exécuter la commande suivante, qui réinitialise la base de données en rétablissant l’instance LocalDB par défaut :

    	AzureStorageEmulator init /forceCreate 

Pour plus d’informations sur ces commandes, consultez la section [Référence de l’outil en ligne de commande de l’émulateur de stockage](#storage-emulator-command-line-tool-reference).

## Adressage des ressources dans l’émulateur de stockage

Les points de terminaison de service de l’émulateur de stockage sont différents de ceux d’un compte de stockage Azure. Cette différence est due au fait que l’ordinateur local n’effectue pas de résolution de noms de domaine ; les points de terminaison de l’émulateur de stockage requièrent donc une adresse locale plutôt qu’un nom de domaine.

Pour adresser une ressource dans un compte de stockage Azure, vous devez utiliser le modèle suivant, où le nom de compte fait partie du nom d’hôte de l’URI et la ressource adressée fait partie du chemin d’accès de l’URI :

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

Par exemple, l’URI suivante est une adresse valide pour un objet blob dans un compte de stockage Azure :

	https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

Dans l’émulateur de stockage, étant donné que l’ordinateur local n’effectue pas la résolution des noms de domaine, le nom de compte fait partie du chemin d’accès de l’URI au lieu du nom d’hôte. Vous devez utiliser le modèle suivant pour une ressource exécutée dans l’émulateur de stockage :

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

Par exemple, l'adresse suivante peut être utilisée pour accéder à un objet blob dans l'émulateur de stockage :

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

Les points de terminaison de service de l’émulateur de stockage sont :

	Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
	Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
	Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

### Adressage du compte secondaire avec RA-GRS

À partir de la version 3.1, le compte d'émulateur de stockage prend en charge la réplication géo-redondante avec accès en lecture. Pour les ressources de stockage dans le cloud et dans l'émulateur local, vous pouvez accéder à l'emplacement secondaire en ajoutant -secondary au nom du compte. Par exemple, vous pouvez utiliser l'adresse suivante pour accéder à un objet blob en utilisant l'emplacement secondaire en lecture seule dans l'émulateur de stockage :

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE]Pour un accès par programmation au stockage secondaire avec l'émulateur de stockage, utilisez la bibliothèque cliente de stockage pour .NET version 3.2 ou ultérieure. Pour plus d'informations, consultez la page [Référence de la bibliothèque cliente Storage](https://msdn.microsoft.com/library/azure/dn261237.aspx).

## Référence de l’outil en ligne de commande de l’émulateur de stockage

À compter de la version 3.0, lorsque vous lancez l’émulateur de stockage, une fenêtre de ligne de commande s’affiche. Utilisez-la pour démarrer ou arrêter l’émulateur, ainsi que pour interroger l’état et effectuer d’autres opérations.

> [AZURE.NOTE]Si l’émulateur de calcul Microsoft Azure est installé, une icône apparaît dans la zone de notification lorsque vous lancez l’émulateur de stockage. Cliquez avec le bouton droit sur l'icône pour faire apparaître un menu, qui fournit un moyen graphique pour démarrer et arrêter l'émulateur de stockage.

### Syntaxe de la ligne de commande

	AzureStorageEmulator [start] [stop] [status] [clear] [init] [help]

### Options

Pour afficher la liste des options, tapez `/help` dans l’invite de commandes.

| Option | Description | Commande | Arguments |
|--------|----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **Start** | Permet de démarrer l’émulateur de stockage. | `AzureStorageEmulator start [-inprocess]` | *-inprocess* : démarrez l’émulateur dans le processus actuel au lieu de créer un nouveau processus. |
| **Stop** | Permet d’arrêter l’émulateur de stockage. | `AzureStorageEmulator stop` | |
| **Status** | Permet d’imprimer l’état de l’émulateur de stockage. | `AzureStorageEmulator status` | |
| **Clear** | Permet d’effacer les données de tous les services spécifiés sur la ligne de commande. | `AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    `| *blob* : efface les données d’objet blob. <br/>*queue* : efface les données de file d’attente. <br/>*table* : efface les données de table. <br/>*all* : efface toutes les données de tous les services. |
| **Init** | Permet d’effectuer une initialisation ponctuelle pour configurer l’émulateur. | `AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate] [-inprocess]` | *-server serverName\\instanceName* : spécifie le serveur hébergeant l’instance SQL. <br/>*-sqlinstance instanceName* : spécifie le nom de l’instance SQL à utiliser dans l’instance de serveur par défaut. <br/>*-forcecreate* : force la création de la base de données SQL, même si celle-ci existe déjà. <br/>*-inprocess* : effectue l’initialisation dans le processus actuel au lieu de générer un nouveau processus. Vous devez lancer le processus en cours avec des autorisations élevées pour exécuter l’initialisation. |
                                                                                                                  
## Différences entre l’émulateur de stockage et Azure Storage

L’émulateur de stockage étant un environnement émulé exécuté dans une instance SQL locale, il existe des différences de fonctionnalités entre l’émulateur et un compte de stockage Azure dans le cloud :

- L'émulateur de stockage prend en charge uniquement un compte fixe et une clé d'authentification connue.

- L’émulateur de stockage n’est pas un service de stockage évolutif et ne prend pas en charge un grand nombre de clients simultanés.

- Comme décrit dans [Adressage des ressources dans l’émulateur de stockage](#addressing-resources-in-the-storage-emulator), les ressources ne sont pas adressées de la même manière dans l’émulateur de stockage et dans un compte de stockage Azure. Cette différence est due au fait que la résolution de noms de domaine est disponible dans le cloud, mais pas sur l’ordinateur local.

- À partir de la version 3.1, le compte d'émulateur de stockage prend en charge la réplication géo-redondante avec accès en lecture. Dans l’émulateur, RA-GRS est activé pour tous les comptes et il n’y a jamais de latence entre le réplica principal et le réplica secondaire. Les opérations Get Blob Service Stats, Get Queue Service Stats et Get Table Service Stats sont prises en charge sur le compte secondaire et retournent toujours la valeur de l’élément de réponse `LastSyncTime` comme heure actuelle en fonction de la base de données SQL sous-jacente.

	Pour un accès par programmation au stockage secondaire avec l'émulateur de stockage, utilisez la bibliothèque cliente de stockage pour .NET version 3.2 ou ultérieure. Pour plus d'informations, consultez la page [Référence de la bibliothèque cliente Storage](https://msdn.microsoft.com/library/azure/dn261237.aspx).

- Les points de terminaison du service de fichiers et de protocole SMB ne sont pas pris en charge dans l’émulateur de stockage pour le moment.

- L’émulateur de stockage renvoie une erreur VersionNotSupportedByEmulator (code d’état HTTP 400 – demande incorrecte) si votre version des services de stockage n’est pas encore prise en charge par la version de l’émulateur que vous utilisez.

### Différences pour le stockage d’objets blob 

Les différences suivantes s’appliquent au stockage d’objets blob dans l’émulateur :

- L’émulateur de stockage prend uniquement en charge les objets blob d’une taille inférieure ou égale à 2 Go.

- Une opération Put Blob peut réussir sur un objet blob qui existe dans l’émulateur de stockage et présente un bail actif, même si l’ID du bail n’a pas été spécifié dans le cadre de la demande.

- L’émulateur ne prend pas en charge les opérations des objets blob d’ajout. Toute tentative d’exécution d’une opération sur un objet blob d’ajout renvoie une erreur FeatureNotSupportedByEmulator (code d’état HTTP 400 – demande incorrecte).

### Différences pour le stockage de tables 

Les différences suivantes s’appliquent au stockage de tables dans l’émulateur :

- Les propriétés de date du service de Table dans l’émulateur de stockage ne prennent en charge que la plage autorisée par SQL Server 2005 (*c’est-à-dire* les dates postérieures au 1er janvier 1753). Toutes les dates antérieures au 1er janvier 1753 sont remplacées par cette valeur. La précision des dates est limitée à la précision de SQL Server 2005, ce qui signifie que les dates sont précises au 1/300e de seconde.

- L’émulateur de stockage prend en charge des valeurs de propriétés de clé de partition et de clé de ligne de moins de 512 octets chacune. De plus, la taille totale du nom du compte, du nom de table et de l’ensemble des noms de propriétés de clé ne peut pas dépasser 900 octets.

- La taille totale d’une ligne de table dans l’émulateur de stockage est limitée à moins de 1 Mo.

- Dans l’émulateur de stockage, les propriétés du type de données `Edm.Guid` ou `Edm.Binary` ne prennent en charge que les opérateurs de comparaison `Equal (eq)` et `NotEqual (ne)` dans les chaînes de filtre de requête.

### Différences pour le stockage de files d’attente

Le stockage de files d’attente dans l’émulateur ne présente aucune différence spécifique.

## Notes de publication de l’émulateur de stockage

### Version 4.2

- L’émulateur de stockage prend maintenant en charge la version 2015-04-05 des services de stockage sur les points de terminaison des services BLOB, de File d’attente et de Table.

### Version 4.1

- L’émulateur de stockage prend maintenant en charge la version 2015-02-21 des services de stockage sur les points de terminaison des services de Blob, de File d’attente et de Table, à l’exception des nouvelles fonctionnalités des objets blob d’ajout. 

- L’émulateur de stockage renvoie désormais un message d’erreur explicite si vous utilisez une version des services de stockage qui n’est pas encore prise en charge par cette version de l’émulateur. Nous vous recommandons d’utiliser la dernière version de l’émulateur. Si vous rencontrez une erreur VersionNotSupportedByEmulator (code d’état HTTP 400 – demande incorrecte), téléchargez la dernière version de l’émulateur de stockage.

- Correction d’un bogue dans lequel une condition de course a généré des données d’entité de table incorrectes lors d’opérations de fusion simultanées.

### Version 4.0

- L’exécutable de l’émulateur de stockage est renommé en *AzureStorageEmulator.exe*.

### Version 3.2
- L’émulateur de stockage prend maintenant en charge la version 2014-02-14 des services de stockage sur les points de terminaison des services BLOB, de File d’attente et de Table. Notez que les points de terminaison du service de fichiers ne sont pas pris en charge dans l’émulateur de stockage pour le moment. Pour plus d’informations sur la version 2014-02-14, consultez la page [Contrôle de version pour les services Azure Storage](https://msdn.microsoft.com/library/azure/dd894041.aspx).

### Version 3.1
- Le stockage géo-redondant avec accès en lecture (RA-GRS) est maintenant pris en charge dans l’émulateur de stockage. Les API Get Blob Service Stats, Get Queue Service Stats et Get Table Service Stats sont prises en charge pour le compte secondaire et renvoient la valeur de l’élément de réponse LastSyncTime comme heure actuelle en fonction de la base de données SQL sous-jacente. Pour un accès par programmation au stockage secondaire avec l'émulateur de stockage, utilisez la bibliothèque cliente de stockage pour .NET version 3.2 ou ultérieure. Pour plus d'informations, consultez la Référence de la bibliothèque cliente de stockage.

### Version 3.0
- L’émulateur de stockage Azure n’est plus inclus dans le même package que l’émulateur de calcul.

- L’interface graphique de l’émulateur de stockage est remplacée par une interface de ligne de commande scriptable. Pour plus d’informations sur l’interface de ligne de commande, consultez la section Référence de l’outil en ligne de commande de l’émulateur de stockage. L’interface graphique est toujours présente dans la version 3.0, mais elle est uniquement accessible lorsque l’émulateur de calcul est installé en cliquant avec le bouton droit sur l’icône de la zone de notification, puis en sélectionnant Afficher l’IU de l’émulateur de stockage.

- La version 2013-08-15 des services de stockage Azure est maintenant entièrement prise en charge. (Auparavant, cette version était uniquement prise en charge par la version préliminaire de l’émulateur de stockage version 2.2.1.)

<!---HONumber=Oct15_HO2-->