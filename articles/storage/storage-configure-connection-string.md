<properties 
    pageTitle="Configuration d’une chaîne de connexion dans Azure Storage | Microsoft Azure"
    description="Configurez une chaîne de connexion à un compte de stockage Azure. Une chaîne de connexion inclut les informations nécessaires pour authentifier l’accès à un compte de stockage à partir de votre application, pendant l’exécution."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="configure-azure-storage-connection-strings"></a>Configuration des chaînes de connexion Azure Storage

## <a name="overview"></a>Vue d'ensemble

Une chaîne de connexion inclut les informations d’authentification nécessaires pour accéder aux données dans un compte de stockage Azure à partir de votre application, pendant l’exécution. Vous pouvez configurer une chaîne de connexion des manières suivantes :

- Connexion à l’émulateur de stockage Azure
- Accès à un compte de stockage dans Azure
- Accès aux ressources spécifiées dans Azure via une signature d’accès partagé (SAS).

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Récupération de votre chaîne de connexion

Votre application devra accéder à la chaîne de connexion pendant l’exécution pour authentifier les requêtes transmises à Azure Storage. Plusieurs options vous permettant de stocker votre chaîne de connexion s’offrent à vous :

- Pour une application s’exécutant sur le bureau ou sur un appareil, vous pouvez stocker la chaîne de connexion dans un fichier `app.config ` ou `web.config`. Ajoutez la chaîne de connexion dans la section **AppSettings** .
- Pour une application s’exécutant dans un service cloud Azure, vous pouvez stocker votre chaîne de connexion dans le [schéma de configuration du service Azure (fichier .cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Ajoutez la chaîne de connexion à la section **ConfigurationSettings** du fichier de configuration du service.
- Vous pouvez également utiliser votre chaîne de connexion directement dans votre code. Cependant, le plus souvent, nous vous recommandons de stocker votre chaîne de configuration dans un fichier de configuration.

Le stockage de votre chaîne de connexion dans un fichier de configuration facilite la mise à jour de la chaîne de connexion qui vous permet de basculer entre l'émulateur de stockage et un compte de stockage Azure dans le cloud. Il vous suffit de modifier la chaîne de connexion pour la faire pointer vers votre environnement cible.

Vous pouvez utiliser la classe [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) pour accéder à votre chaîne de connexion lors de l’exécution, quel que soit l’environnement d’exécution de votre application.

## <a name="create-a-connection-string-to-the-storage-emulator"></a>Création d’une chaîne de connexion à l’émulateur de stockage

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Pour plus d’informations sur l’émulateur de stockage, consultez la rubrique [Utilisation de l'émulateur de stockage Azure pour le développement et le test](storage-use-emulator.md) .

## <a name="create-a-connection-string-to-an-azure-storage-account"></a>Création d’une chaîne de connexion à un compte de stockage Azure

Pour créer une chaîne de connexion à votre compte de stockage Azure, utilisez le format de chaîne de connexion suivant. Indiquez si vous souhaitez vous connecter au compte de stockage via HTTPS (recommandé) ou HTTP, remplacez `myAccountName` par le nom de votre compte de stockage et remplacez `myAccountKey` par la touche d’accès rapide à votre compte :

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Par exemple, votre chaîne de connexion ressemble à l’exemple de chaîne de connexion suivant :

    DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>

> [AZURE.NOTE] Azure Storage prend en charge HTTP et HTTPS au sein d’une chaîne de connexion, mais nous vous conseillons vivement d’utiliser HTTPS.

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Création d’une chaîne de connexion à l’aide d’une signature d’accès partagé

[AZURE.INCLUDE [storage-use-sas-in-connection-string-include](../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="creating-a-connection-string-to-an-explicit-storage-endpoint"></a>Création d’une chaîne de connexion vers un point de terminaison de stockage explicite

Vous pouvez spécifier explicitement les points de terminaison de service dans votre chaîne de connexion au lieu d’utiliser les points de terminaison par défaut. Pour créer une chaîne de connexion spécifiant un point de terminaison explicite, indiquez le point de terminaison complet de chaque service, ainsi que le protocole (HTTPS (recommandé) ou HTTP) au format suivant :

    DefaultEndpointsProtocol=[http|https];
    BlobEndpoint=myBlobEndpoint;
    QueueEndpoint=myQueueEndpoint;
    TableEndpoint=myTableEndpoint;
    FileEndpoint=myFileEndpoint;
    AccountName=myAccountName;
    AccountKey=myAccountKey

Si vous avez mappé votre point de terminaison Blob Storage à un domaine personnalisé, il vaut mieux ne spécifier aucun point de terminaison explicite. Dans ce cas, vous pouvez spécifier votre point de terminaison personnalisé pour Blob Storage dans votre chaîne de connexion, et éventuellement les points de terminaison par défaut de l’autre service si votre application les utilise.

Voici des exemples de chaînes de connexion valides qui spécifient un point de terminaison explicite pour le service Blob :

    # Blob endpoint only
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    AccountName=storagesample;
    AccountKey=account-key

    # All service endpoints
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    FileEndpoint=myaccount.file.core.windows.net;
    QueueEndpoint=myaccount.queue.core.windows.net;
    TableEndpoint=myaccount;
    AccountName=storagesample;
    AccountKey=account-key

La valeur du point de terminaison indiquée dans la chaîne de connexion est utilisée pour construire les URI de demande au service BLOB. Elle indique la forme de tous les URI renvoyés à votre code.

Notez que si vous choisissez d’omettre un point de terminaison de service dans la chaîne de connexion, vous ne pourrez pas accéder aux données de ce service avec votre code à l’aide de cette chaîne de connexion.

### <a name="creating-a-connection-string-with-an-endpoint-suffix"></a>Création d’une chaîne de connexion avec un suffixe de point de terminaison

Pour créer une chaîne de connexion pour le service de stockage dans les régions ou les instances avec des suffixes de point de terminaison différents, comme pour Azure China ou Azure Governance, utilisez le format de chaîne de connexion suivant. Indiquez si vous souhaitez vous connecter au compte de stockage via HTTP ou HTTPS, remplacez `myAccountName` par le nom de votre compte de stockage, remplacez `myAccountKey` par votre clé d’accès au compte et remplacez `mySuffix` par le suffixe d’URI :


    DefaultEndpointsProtocol=[http|https];
    AccountName=myAccountName;
    AccountKey=myAccountKey;
    EndpointSuffix=mySuffix;


Par exemple, votre chaîne de connexion doit être similaire à celle-ci :

    DefaultEndpointsProtocol=https;
    AccountName=storagesample;
    AccountKey=<account-key>;
    EndpointSuffix=core.chinacloudapi.cn;

## <a name="parsing-a-connection-string"></a>Analyse d’une chaîne de connexion

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]


## <a name="next-steps"></a>Étapes suivantes

- [Utilisation de l'émulateur de stockage Azure pour le développement et le test](storage-use-emulator.md)
- [Explorateurs du stockage Azure](storage-explorers.md)
- [Utilisation des signatures d’accès partagé (SAP)](storage-dotnet-shared-access-signature-part-1.md)



<!--HONumber=Oct16_HO2-->


