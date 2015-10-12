<properties 
	pageTitle="Signatures d’accès partagé : Présentation du modèle SAP | Microsoft Azure" 
	description="Découvrez comment déléguer l’accès aux ressources de stockage Azure, notamment les objets blob, les files d’attente, les tables et les fichiers, à l’aide de signatures d’accès partagé (SAP). Ces signatures vous permettent de protéger la clé de votre compte de stockage tout en octroyant un accès aux ressources de votre compte à d’autres utilisateurs. Vous pouvez contrôler les autorisations accordées et l’intervalle pendant lequel la signature d’accès partagé est valide. Si vous établissez également une stratégie d’accès stockée, vous pouvez révoquer la signature d’accès partagé si vous craignez que la sécurité de votre compte ne soit compromise." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/27/2015" 
	ms.author="tamram"/>



# Signatures d'accès partagé, partie 1 : présentation du modèle SAP

## Vue d'ensemble

Une signature d’accès partagé (SAP) constitue un moyen efficace pour octroyer aux autres clients un accès limité aux objets blob dans votre compte de stockage, sans exposer votre clé de compte. Dans la première partie de ce didacticiel consacré aux signatures d'accès partagé, nous allons vous présenter un aperçu du modèle SAP et examiner les meilleures pratiques concernant les signatures d'accès partagé. La [partie 2](storage-dotnet-shared-access-signature-part-2.md) du didacticiel décrit étape par étape la procédure de création de signatures d'accès partagé avec le service BLOB.

## Présentation de la signature d’accès partagé

Une signature d'accès partagé fournit un accès délégué aux ressources de votre compte de stockage. Cela vous permet d’octroyer à un client des autorisations d’accès limité à des objets de votre compte de stockage pendant une période donnée et avec un ensemble défini d’autorisations, sans partager les clés d’accès de votre compte. La signature d'accès partagé est un URI qui englobe dans ses paramètres de requête toutes les informations nécessaires pour un accès authentifié à une ressource de stockage. Pour accéder aux ressources de stockage avec la signature d'accès partagé, il suffit au client de transmettre cette dernière à la méthode ou au constructeur approprié.

## Quand devez-vous utiliser une signature d’accès partagé ?

Vous pouvez utiliser une signature d'accès partagé lorsque vous voulez fournir un accès aux ressources dans votre compte de stockage à un client qui ne peut pas être approuvé avec la clé du compte. Vos clés de compte de stockage incluent une clé primaire et une clé secondaire, qui octroient toutes deux un accès administratif à votre compte et à toutes ses ressources. En exposant l'une ou l'autre des clés de votre compte, vous courez le risque d'une utilisation malveillante ou négligente de ce dernier. Les signatures d'accès partagé offrent une alternative sûre qui permet aux autres clients de lire, d'écrire et de supprimer des données dans votre compte de stockage en fonction des autorisations que vous avez octroyées, sans avoir besoin de la clé du compte.

Un service où les utilisateurs lisent et écrivent leurs propres données dans votre compte de stockage correspond à un scénario courant dans lequel une signature d'accès partagé peut s'avérer utile. Dans un scénario où un compte de stockage stocke les données utilisateur, il existe deux modèles de conception types :


1\. Les clients chargent et téléchargent les données par le biais d’un service proxy frontal, qui se charge de l’authentification. Ce service présente l'avantage de permettre la validation des règles métier, mais pour de grosses quantités de données ou des transactions à haut volume, la création d'un service qui peut être mis à l'échelle en fonction de la demande peut se révéler coûteuse ou difficile.

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2\.	Un service léger authentifie le client en fonction des besoins, puis génère une signature d’accès partagé. Une fois que le client reçoit la signature, il peut accéder aux ressources du compte de stockage directement avec les autorisations définies par la signature d'accès partagé et pendant l'intervalle autorisé par cette dernière. La signature d'accès partagé atténue la nécessité du routage de toutes les données via le service proxy frontal.

![sas-storage-provider-service][sas-storage-provider-service]

De nombreux services dans le monde réel peuvent utiliser un mélange de ces deux approches, suivant le scénario concerné, certaines données étant traitées et validées par l'intermédiaire du proxy frontal, tandis que les autres sont enregistrées et/ou lues directement avec la signature d'accès partagé.

En outre, vous devez utiliser une SAP pour authentifier l’objet source d’une opération de copie, dans certains cas de figure :

- Lorsque vous copiez un objet blob dans un autre objet blob qui réside dans un autre compte de stockage, vous devez utiliser une SAP pour authentifier l’objet blob source. Avec la version 2015-04-05, vous pouvez éventuellement utiliser une SAP pour authentifier également l’objet blob de destination.
- Lorsque vous copiez un objet blob dans un autre fichier qui réside dans un autre compte de stockage, vous devez utiliser une SAP pour authentifier le fichier source. Avec la version 2015-04-05, vous pouvez éventuellement utiliser une SAP pour authentifier également le fichier de destination.
- Lorsque vous copiez un objet blob dans un fichier ou un fichier dans un objet blob, vous devez utiliser une SAP pour authentifier l’objet source, même si les objets source et de destination résident dans le même compte de stockage.

## Types de signatures d’accès partagé

La version 2015-04-05 d’Azure Storage introduit un nouveau type de signature d’accès partagé : la SAP de compte. Vous pouvez maintenant créer deux types de signatures d’accès partagé différents :

- **SAP de compte.** La SAP de compte délègue l’accès aux ressources d’un ou plusieurs des services de stockage. Toutes les opérations disponibles via une SAP de service sont également disponibles via une SAP de compte. En outre, avec la SAP de compte, vous pouvez déléguer l’accès à des opérations qui s’appliquent à un service donné, telles que **Get/Set Service Properties** et **Get Service Stats**. Vous pouvez également déléguer l’accès aux opérations de lecture, d’écriture et de suppression sur les conteneurs d’objets blob, les tables, les files d’attente et les partages de fichiers qui ne sont pas autorisées avec une SAP de service. Pour obtenir des informations détaillées sur la construction du jeton de SAP de compte, consultez la page [Construction d’une SAP de compte](https://msdn.microsoft.com/library/mt584140.aspx).

- **SAP de service.** Une SAP de service délègue l’accès à une ressource d’un seul des services de stockage : le service BLOB, de File d’attente, de Table ou de fichiers. Pour obtenir des informations détaillées sur la construction du jeton de SAP de compte, consultez les pages [Construction d’une SAP de service](https://msdn.microsoft.com/library/dn140255.aspx) et [Exemples de SAP de service](https://msdn.microsoft.com/library/dn140256.aspx).

## Fonctionnement d’une signature d’accès partagé

Une signature d’accès partagé est un URI qui désigne une ou plusieurs ressources de stockage et inclut un jeton qui contient un ensemble spécial de paramètres de requête. Le jeton indique comment le client peut accéder aux ressources. L’un des paramètres de requête, la signature, est construit à partir des paramètres de signature d’accès partagé et signé avec la clé du compte. Cette signature est utilisée par Azure Storage pour authentifier la signature d'accès partagé.

Les jetons de SAP de compte et de SAP de service incluent des paramètres communs et également quelques paramètres différents.

### Paramètres communs aux jetons de SAP de compte et de SAP de service

- **Version de l’API.** Paramètre facultatif qui spécifie la version du service de stockage à utiliser pour exécuter la demande. 
- **Version du service.** Paramètre obligatoire qui spécifie la version du service de stockage à utiliser pour authentifier la demande.
- **Heure de début.** Il s'agit de l'heure à laquelle la signature d'accès partagé devient valide. L'heure de début pour une signature d'accès partagé est facultative ; si elle est omise, la signature d'accès partagé prend effet immédiatement. 
- **Heure d’expiration.** Il s'agit de l'heure à laquelle la signature d'accès partagé cesse d'être valide. Les meilleures pratiques recommandent soit de spécifier une heure d'expiration pour une signature d'accès partagé, soit de l'associer à une stratégie d'accès stockée (voir plus loin).
- **Autorisations.** Les autorisations spécifiées sur la signature d'accès partagé indiquent quelles opérations le client peut exécuter avec cette dernière sur la ressource de stockage. Les autorisations disponibles ne sont pas les mêmes pour une SAP de compte et une SAP de service.
- **IP.** Paramètre facultatif qui spécifie une adresse IP ou une plage d’adresses IP dont les demandes doivent être acceptées. 
- **Protocole.** Paramètre facultatif qui spécifie le protocole autorisé pour une demande. Les valeurs possibles sont HTTPS et HTTP à la fois (https,http), qui est la valeur par défaut, ou HTTPS uniquement (https). Notez que HTTP uniquement n’est pas une valeur autorisée.
- **Signature.** La signature est construite à partir des autres paramètres spécifiés pour le jeton, puis chiffrée. Elle est utilisée pour authentifier la SAP.

### Paramètres d’un jeton de compte SAP

- **Service ou services.** Une SAP de compte peut déléguer l’accès à un ou plusieurs des services de stockage. Par exemple, vous pouvez créer une SAP de compte qui délègue l’accès au service BLOB et au service de fichiers. Vous pouvez également créer une SAP qui délègue l’accès à l’ensemble des quatre services (BLOB, File d’attente, Table et fichiers).
- **Types de ressources de stockage.** Une SAP de compte s’applique à une ou plusieurs classes de ressources de stockage plutôt qu’à une ressource spécifique. Vous pouvez créer une SAP de compte pour déléguer l’accès à :
	- Des API au niveau de service, qui sont appelées sur la ressource du compte de stockage. Exemples : **Get/Set Service Properties**, **Get Service Stats** et **List Containers/Queues/Tables/Shares**.
	- Des API au niveau du conteneur, qui sont appelés sur les objets de conteneur pour chaque service : conteneurs d’objets blob, files d’attente, tables et partages de fichiers. Exemples : **Create/Delete Container**, **Create/Delete Queue**, **Create/Delete Table**, **Create/Delete Share** et **List Blobs/Files and Directories**.
	- Des API au niveau de l’objet, qui sont appelées sur les objets blob, les messages de file d’attente, les entités de table et les fichiers. Exemples : **Put Blob**, **Query Entity**, **Get Messages** et **Create File**.

### Paramètres d’un jeton de SAP de service

- **Ressource de stockage.** Les ressources de stockage dont vous pouvez déléguer l’accès à l’aide d’une SAP de service incluent :
	- Conteneurs et objets blob
	- Partages de fichiers et fichiers
	- Files d’attente
	- Tables et plages d’entités de table.

## Exemples d’URI de SAP

Voici un exemple d’URI de SAP de service qui fournit des autorisations d’accès en lecture et en écriture sur un objet blob. La table décompose chaque partie de l’URI pour comprendre comment elle contribue à la signature d’accès partagé :

	https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

Nom|Partie de la SAP|Description
---|---|---
URI de l’objet blob|https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt |Adresse de l'objet blob. Notez que l'utilisation de HTTPS est fortement recommandée.
Version des services de stockage|sv=2015-04-05|Pour la version 2012-02-12 des services de stockage et les versions ultérieures, ce paramètre indique la version à utiliser.
Heure de début|st=2015-04-29T22%3A18%3A26Z|Spécifiée au format ISO 8061. Si vous voulez que la signature d'accès partagé soit valide immédiatement, omettez l'heure de début.
Heure d’expiration|se=2015-04-30T02%3A23%3A26Z|Spécifiée au format ISO 8061.
Ressource|sr=b|La ressource est un objet blob.
Autorisations|sp=rw|Les autorisations octroyées par la signature d'accès partagé incluent les opérations de lecture (r) et d'écriture (w).
Plage d’adresses IP|sip=168.1.5.60-168.1.5.70|Plage d’adresses IP dont les demandes seront acceptées.
Protocole|spr=https|Seules les demandes utilisant HTTPS sont autorisées.
Signature|sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D|Utilisée pour authentifier l'accès à l'objet blob. La signature est un HMAC calculé sur une chaîne de signature et une clé à l'aide de l'algorithme SHA256, puis codé en Base64.

Et voici un exemple de SAP de compte qui utilise les mêmes paramètres communs sur le jeton. Dans la mesure où ces paramètres sont décrits ci-dessus, ils ne sont pas décrits ici. Seuls les paramètres propres à la SAP de compte sont décrits dans le tableau ci-dessous.

	https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B

Nom|Partie de la SAP|Description
---|---|---
URI de ressource|https://myaccount.blob.core.windows.net/?restype=service&comp=properties|The Point de terminaison du service BLOB, avec les paramètres d’obtention des propriétés du service (appel avec la méthode GET) ou de définition des propriétés du service (appel avec la méthode SET).
Services|ss=bf|La SAP s’applique au service BLOB et au service de fichiers.
Types de ressources|srt=s|La SAP s’applique aux opérations au niveau du service.
Autorisations|sp=rw|Les autorisations accordent l’accès aux opérations de lecture et d’écriture.  

Étant donné que les autorisations sont limitées au niveau du service, les opérations accessibles avec cette SAP sont **Get Blob Service Properties** (lecture) et **Set Blob Service Properties** (écriture). Cependant, avec un autre URI de ressource, le même jeton de SAP peut également être utilisé pour déléguer l’accès à l’opération **Get Blob Service Stats** (lecture).

## Contrôle d’une SAP avec une stratégie d’accès stockée ##

Une signature d’accès partagé peut prendre deux formes :

- **Signature d'accès partagé ad hoc :** lorsque vous créez une signature d'accès partagé ad hoc, l'heure de début, l'heure d'expiration et les autorisations associées à cette signature sont spécifiées sur l'URI de signature d'accès partagé (ou sont implicites, dans le cas où l'heure de début est omise). Ce type de SAP peut être créé en tant que SAP de compte ou SAP de service. 

- **Signature d’accès partagé avec stratégie d’accès stockée :** une stratégie d’accès stockée est définie sur un conteneur de ressources (conteneur d’objets blob, table, file d’attente ou partage de fichiers) et permet de gérer les contraintes d’une ou de plusieurs signatures d’accès partagé. Lorsque vous associez une signature d'accès partagé à une stratégie d'accès stockée, la signature hérite des contraintes (heure de début, heure d'expiration et autorisations) définies pour la stratégie.

>[AZURE.NOTE]À l’heure actuelle, une SAP de compte doit être une SAP ad hoc. Les stratégies d’accès stockées ne sont pas encore prises en charge pour les SAP de compte.

La différence entre les deux formes est importante pour un scénario clé : la révocation. Une signature d’accès partagé est une URL. Par conséquent, toute personne qui obtient la signature peut s’en servir, quel que soit celui qui l’a demandée initialement. Si une SAP est publiée publiquement, elle peut être utilisée par n’importe qui. Une clé d'accès partagé qui est distribuée est valide jusqu'à ce que l'un des quatre événements suivants ait lieu :

1.	L'heure d'expiration spécifiée sur la signature d'accès partagé est atteinte.
2.	L'heure d'expiration spécifiée sur la stratégie d'accès stockée référencée par la signature d'accès partagé est atteinte (si une stratégie d'accès stockée est référencée et si elle spécifie une heure d'expiration). Cela peut arriver soit parce que l'intervalle s'est écoulé, soit parce que vous avez modifié la stratégie d'accès stockée pour définir une heure d'expiration dans le passé, ce qui est une manière de révoquer la signature d'accès partagé.
3.	La stratégie d'accès stockée référencée par la signature d'accès partagé est supprimée, ce qui est une autre manière de révoquer la signature d'accès partagé. Notez que si vous recréez la stratégie d'accès stockée avec exactement le même nom, tous les jetons de signature d'accès partagé existants seront de nouveau valides en fonction des autorisations associées à cette stratégie d'accès stockée (en partant du principe que l'heure d'expiration sur la signature d'accès partagé n'est pas passée). Si vous avez l'intention de révoquer la signature d'accès partagé, veillez à utiliser un nom différent si vous recréez la stratégie d'accès avec une heure d'expiration située dans le futur.
4.	La clé de compte qui a été utilisée pour créer la signature d'accès partagé est régénérée. Notez que cela provoquera un échec d'authentification pour tous les composants de l'application qui utilisent cette clé de compte jusqu'à ce qu'ils soient mis à jour afin d'utiliser soit l'autre clé de compte valide soit la clé de compte nouvellement régénérée.

>[AZURE.IMPORTANT]L’URI d’une signature d’accès partagé est associé à la clé du compte utilisée pour créer la signature et à la stratégie d’accès stockée correspondante (le cas échéant). Si aucune stratégie d’accès stockée n’est spécifiée, la seule façon de révoquer une signature d’accès partagé consiste à modifier la clé du compte.

## Exemples de signatures d’accès partagé

Vous trouverez ci-dessous des exemples des deux types de signatures d’accès partagé, SAP de compte et SAP de service.

### Exemple de SAP de compte

L’exemple de code suivant crée une SAP de compte valide pour le service BLOB et le service de fichiers, et donne au client des autorisations d’accès en lecture, en écriture et en liste pour accéder aux API au niveau du service. Le SAP de compte limitant le protocole à HTTPS, la demande doit être effectuée avec ce protocole.

    static string GetAccountSASToken()
    {
        // To create the account SAS, you need to use your shared key credentials.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Create a new access policy for the account.
        SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
            {
                Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
                Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
                ResourceTypes = SharedAccessAccountResourceTypes.Service,
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
                Protocols = SharedAccessProtocol.HttpsOnly
            };

        // Return the SAS token.
        return storageAccount.GetSharedAccessSignature(policy);
    }

Afin utiliser la SAP de compte pour accéder aux API au niveau du service pour le service BLOB, construisez un client d’objet blob à l’aide de la SAP et du point de terminaison de stockage d’objets blob de votre compte de stockage.

    static void UseAccountSAS(string sasToken)
    {
        // In this case, we have access to the shared key credentials, so we'll use them
        // to get the Blob service endpoint.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create new storage credentials using the SAS token.
        StorageCredentials accountSAS = new StorageCredentials(sasToken);
        // Use these credentials and the Blob storage endpoint to create a new Blob service client.
        CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, blobClient.StorageUri, null, null, null);
        CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

        // Now set the service properties for the Blob client created with the SAS.
        blobClientWithSAS.SetServiceProperties(new ServiceProperties()
        {
            HourMetrics = new MetricsProperties()
            {
                MetricsLevel = MetricsLevel.ServiceAndApi,
                RetentionDays = 7,
                Version = "1.0"
            },
            MinuteMetrics = new MetricsProperties()
            {
                MetricsLevel = MetricsLevel.ServiceAndApi,
                RetentionDays = 7,
                Version = "1.0"
            },
            Logging = new LoggingProperties()
            {
                LoggingOperations = LoggingOperations.All,
                RetentionDays = 14,
                Version = "1.0"
            }
        });

        // The permissions granted by the account SAS also permit you to retrieve service properties.
        ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
        Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
        Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
        Console.WriteLine(serviceProperties.HourMetrics.Version);
    }

### Exemple de SAP de service

L’exemple de code suivant crée une stratégie d’accès stockée sur un conteneur puis génère une SAP de service pour ce conteneur. Cette SAP peut ensuite être donnée aux clients pour leur accorder des autorisations d’accès en lecture/écriture sur le conteneur :

    // The connection string for the storage account.  Modify for your account.
    string storageConnectionString =
       "DefaultEndpointsProtocol=https;" +
       "AccountName=myaccount;" +
       "AccountKey=<account-key>";
    
    // As an alternative, you can retrieve storage account information from an app.config file. 
    // This is one way to store and retrieve a connection string if you are 
    // writing an application that will run locally, rather than in Microsoft Azure.
    
    // string storageConnectionString = ConfigurationManager.AppSettings["StorageAccountConnectionString"];
    
    // Create the storage account with the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
       
    // Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Get a reference to the container for which shared access signature will be created.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();
    
    // Get the current permissions for the blob container.
    BlobContainerPermissions blobPermissions = container.GetPermissions();
    
    // The new shared access policy provides read/write access to the container for 24 hours.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // To ensure SAS is valid immediately, don’t set the start time.
       // This way, you can avoid failures caused by small clock differences.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
       Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
    });
    
    // The public access setting explicitly specifies that 
    // the container is private, so that it can't be accessed anonymously.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;
    
    // Set the permission policy on the container.
    container.SetPermissions(blobPermissions);
    
    // Get the shared access signature token to share with users.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

Un client en possession de la SAP de service peut l’utiliser à partir de son code pour authentifier une demande de lecture ou d’écriture sur un objet blob dans le conteneur. Par exemple, le code suivant utilise le jeton de SAP pour créer un objet blob dans le conteneur :

    Uri blobUri = new Uri("https://myaccount.blob.core.windows.net/mycontainer/myblob.txt");
    
    // Create credentials with the SAS token. The SAS token was created in previous example.
    StorageCredentials credentials = new StorageCredentials(sasToken);
    
    // Create a new blob.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);
    
    // Upload the blob. 
    // If the blob does not yet exist, it will be created. 
    // If the blob does exist, its existing content will be overwritten.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Test\myblob.txt"))
    {
    	blob.UploadFromStream(fileStream);
    }


## Meilleures pratiques pour l’utilisation des signatures d’accès partagé

Lorsque vous utilisez des signatures d'accès partagé dans vos applications, vous devez être conscient de deux risques potentiels :

- Si une signature d'accès partagé est divulguée, toute personne qui se la procure peut s'en servir et votre compte de stockage court donc le risque d'être compromis.
- Si une signature d'accès partagé fournie à une application cliente expire et que l'application est incapable d'en récupérer une nouvelle à partir de votre service, la fonctionnalité de votre application risque d'être entravée.  

Les recommandations suivantes relatives à l'utilisation des signatures d'accès partagé vous aideront à limiter ces risques :

1. **Utilisez toujours HTTPS** pour créer ou distribuer une signature d'accès partagé. Si une signature d’accès partagé est transmise sur HTTP et interceptée, un pirate qui lance une attaque de type « attaque de l’intercepteur » (man-in-the-middle) peut lire la signature et s’en servir exactement comme l’utilisateur concerné aurait pu le faire, d’où le risque que les données sensibles soient compromises ou que les données soient altérées par l’utilisateur malveillant.
2. **Référencez si possible les stratégies d'accès stockées.** Celles-ci vous donnent la possibilité de révoquer les autorisations sans avoir à régénérer les clés de compte de stockage. Définissez l'expiration pour ces dernières de telle sorte que l'échéance soit très éloignée dans le temps (voire infinie) et veillez à ce qu'elle soit régulièrement mise à jour et repoussée dans le futur.
3. **Utilisez des heures d'expiration avec une échéance à court terme sur une signature d'accès partagé ad hoc.** De cette manière, même si une signature d'accès partagé est compromise à votre insu, elle ne sera viable que pendant une courte durée. Cette pratique est particulièrement importante si vous ne pouvez pas référencer une stratégie d'accès stockée. Elle permet également de limiter la quantité de données pouvant être écrite dans un objet blob en limitant le temps disponible pour le téléchargement vers ce dernier.
4. **Faites en sorte que les clients renouvellent automatiquement la signature d'accès partagé si nécessaire.** Les clients doivent renouveler la signature d'accès partagé bien avant l'heure d'expiration prévue afin de laisser suffisamment de temps pour de nouvelles tentatives, si le service qui fournit la signature est indisponible. Si votre signature d'accès partagé doit être utilisée pour un petit nombre d'opérations immédiates de courte durée, censées être terminées avant l'heure d'expiration indiquée, cela ne sera peut-être pas nécessaire, car il n'est pas prévu que la signature d'accès partagé soit renouvelée. Toutefois, si vous avez un client qui effectue régulièrement des demandes par le biais de signatures d'accès partagé, le risque d'expiration est à prendre en compte. La principale considération consiste à trouver un équilibre entre la nécessité que la signature d'accès partagé ait une durée de vie limitée (comme indiqué plus haut) et la nécessité de veiller à ce que le client demande le renouvellement suffisamment tôt pour éviter une interruption due à une expiration de la signature avant le renouvellement effectif.
5. **Faites attention à la date de début de la signature d'accès partagé.** Si vous définissez la date de début d'une signature d'accès partagé sur **maintenant**, en raison du décalage d'horloge (différences constatées dans l'heure actuelle sur des machines différentes), des défaillances peuvent être observées par intermittence pendant les premières minutes. En règle générale, définissez une heure de début située au moins 15 minutes avant l'heure courante ; ou ne la définissez pas du tout, et elle sera alors valide immédiatement dans tous les cas. Cela vaut également d'une manière générale pour l'heure d'expiration. Souvenez-vous que vous pouvez observer jusqu'à 15 minutes de décalage d'horloge (dans l'une ou l'autre direction) sur une demande. Notez que pour les clients qui utilisent une version de REST antérieure à la version 2012-02-12, la durée maximale pour une signature d'accès partagé qui ne renvoie pas à une stratégie d'accès stockée est d'1 heure et que toutes les stratégies spécifiant une période plus longue échoueront.
6.	**Soyez précis quant à la ressource pour laquelle vous voulez configurer l'accès.** Une meilleure pratique courante en matière de sécurité consiste à fournir à l'utilisateur les privilèges minimaux requis. Si un utilisateur a besoin d'un accès en lecture à une seule entité, accordez-lui un accès en lecture à cette seule entité, plutôt qu'un accès en lecture/écriture/suppression à toutes les entités. Cela permet également d'atténuer la menace d'une signature d'accès partagé compromise, dans la mesure où son pouvoir serait moindre entre les mains d'une personne malveillante.
7.	**Sachez que toute utilisation de votre compte sera facturée, y compris pour les signatures d'accès partagé.** Si vous fournissez un accès en écriture à un objet blob, un utilisateur peut choisir de charger un objet blob de 200 Go. Si vous lui avez également accordé un accès en lecture, il peut choisir de le télécharger 10 fois, et vous devrez alors acquitter des frais de sortie pour l'équivalent de 2 To. Accordez des autorisations limitées pour atténuer les risques liés aux utilisateurs malveillants. Utilisez des signatures d'accès partagé à durée de vie limitée pour atténuer cette menace (mais pensez au décalage d'horloge pour l'heure de fin).
8.	**Validez les données écrites avec une signature d'accès partagé.** Lorsqu'une application cliente écrit des données dans votre compte de stockage, n'oubliez pas que ces données peuvent être une source de problèmes. Si votre application exige que ces données soient validées ou autorisées avant de pouvoir être utilisées, vous devez effectuer cette validation après l'écriture des données et avant qu'elles ne soient utilisées par votre application. Cette pratique assure également une protection contre l'écriture de données endommagées ou malveillantes dans votre compte, soit par un utilisateur qui a acquis correctement la signature d'accès partagé, soit par un utilisateur qui exploite sa divulgation.
9. **N'utilisez pas toujours une signature d'accès partagé.** Parfois, les risques associés à une opération particulière sur votre compte de stockage l'emportent sur les avantages offerts par la signature d'accès partagé. Pour ces opérations, créez un service de niveau intermédiaire qui écrit dans votre compte de stockage après avoir effectué la validation des règles métier, l'authentification et un audit. Parfois aussi, il est plus simple de gérer l'accès par d'autres moyens. Par exemple, si vous voulez que tous les objets blob dans un conteneur soient publiquement lisibles, vous pouvez rendre le conteneur public, au lieu de fournir une signature d'accès partagé à chaque client.
10.	**Utilisez Storage Analytics pour surveiller votre application.** Vous pouvez utiliser la journalisation et les mesures pour observer tout pic dans les échecs d'authentification dus à une interruption du service de votre fournisseur de signatures d'accès partagé ou à la suppression par inadvertance d'une stratégie d'accès stockée. Pour plus d'informations, consultez le [blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx).

## Conclusion ##

Les signatures d'accès partagé sont utiles pour fournir des autorisations d'accès limitées à votre compte de stockage aux clients qui ne doivent pas avoir la clé du compte. À ce titre, elles sont un élément crucial du modèle de sécurité pour toute application utilisant Azure Storage. Si vous suivez les meilleures pratiques énumérées ci-dessus, vous pouvez utiliser une signature d'accès partagé pour offrir une plus grande souplesse d'accès aux ressources de votre compte de stockage, sans compromettre la sécurité de votre application.

## Étapes suivantes ##

- [Signatures d’accès partagé, partie 2 : création et utilisation d’une signature d’accès partagé avec le service BLOB](storage-dotnet-shared-access-signature-part-2.md)
- [Utilisation du stockage de fichiers Azure avec Windows](storage-dotnet-how-to-use-files.md)
- [Gestion de l’accès aux ressources d’Azure Storage](storage-manage-access-to-resources.md)
- [Délégation de l'accès avec une signature d'accès partagé](http://msdn.microsoft.com/library/azure/ee395415.aspx)
- [Présentation des signatures d'accès partagé des tables et des files d'attente](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx) [sas-storage-fe-proxy-service] : ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png [sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png


 

<!---HONumber=Oct15_HO1-->