---
title: Utilisation de REST pour sauvegarder et restaurer des applications App Service
description: "Découvrez comment utiliser les appels d’API RESTful pour sauvegarder et restaurer une application dans Azure App Service"
services: app-service
documentationcenter: 
author: NKing92
manager: erikre
editor: 
ms.assetid: f94d0aea-edc1-43ab-9b51-ea7375859276
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2016
ms.author: nicking
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: c1b8fc3be3af46279bf35bddbc82acf1827b9eb9
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---
# <a name="use-rest-to-back-up-and-restore-app-service-apps"></a>Utilisation de REST pour sauvegarder et restaurer des applications App Service
> [!div class="op_single_selector"]
> * [PowerShell](../app-service/app-service-powershell-backup.md)
> * [API REST](websites-csm-backup.md)
> 
> 

[applications App Service](https://azure.microsoft.com/services/app-service/web/) peuvent être sauvegardées en tant qu’objets blob dans Azure Storage. La sauvegarde peut également contenir des bases de données de l’application. En cas de suppression accidentelle de l’application, ou si l’application doit être rétablie à une version antérieure, il est possible de la restaurer à partir d’une sauvegarde précédente. Les sauvegardes peuvent être effectuées à tout moment à la demande, ou être planifiées à des intervalles appropriés.

Cet article explique comment sauvegarder et restaurer une application à l’aide de requêtes d’API RESTful. Si vous souhaitez créer et gérer des sauvegardes d’application sous la forme de graphiques dans le portail Azure, consultez [Sauvegarder une application web dans Azure App Service](web-sites-backup.md)

<a name="gettingstarted"></a>

## <a name="getting-started"></a>Mise en route
Pour envoyer des requêtes REST, vous devez connaître le **nom**, le **groupe de ressources** et l’**ID d’abonnement** de votre application. Vous trouverez ces informations en cliquant sur votre application dans le panneau **App Service** du [portail Azure](https://portal.azure.com). Pour les exemples de cet article, nous configurons le site web **backuprestoreapiexamples.azurewebsites.net**. Celui-ci est stocké dans le groupe de ressources Default-Web-WestUS et s’exécute sur un abonnement associé à l’ID 00001111-2222-3333-4444-555566667777.

![Informations de l’exemple de site web][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>

## <a name="backup-and-restore-rest-api"></a>API REST de sauvegarde et de restauration
Nous allons maintenant présenter plusieurs exemples illustrant la manière dont il est possible d’utiliser l’API REST pour sauvegarder et restaurer une application. Chaque exemple inclut une URL et un corps de requête HTTP. L’exemple d’URL contient des espaces réservés entre accolades, du type {subscription-id}. Remplacez les espaces réservés par les informations correspondantes de votre application. À titre de référence, voici une explication de chaque espace réservé qui apparaît dans les exemples d’URL.

* subscription-id : ID de l’abonnement Azure contenant l’application
* resource-group-name : nom du groupe de ressources contenant l’application
* name : nom de l’application
* backup-id : ID de la sauvegarde de l’application

Pour accéder à la documentation complète de l’API, y compris plusieurs paramètres facultatifs pouvant être inclus dans la requête HTTP, consultez l’ [Explorateur de ressources Azure](https://resources.azure.com/).

<a name="backup-on-demand"></a>

## <a name="backup-an-app-on-demand"></a>Sauvegarde d’une application à la demande
Pour sauvegarder une application immédiatement, envoyez une demande **POST** à **https://management.azure.com/subscriptions/{id-abonnement}/resourceGroups/{nom-groupe-ressources}/providers/Microsoft.Web/sites/{nom}/backup/**.

Voici à quoi ressemble l’URL dans notre exemple de site web. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backup/**

Spécifiez un objet JSON dans le corps de votre requête afin de spécifier le compte de stockage à utiliser pour stocker la sauvegarde. L’objet JSON doit avoir une propriété nommée **storageAccountUrl**, qui contient une [URL SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) qui octroie un accès en écriture au conteneur Azure Storage dans lequel l’objet blob de sauvegarde sera stocké. Si vous souhaitez sauvegarder vos bases de données, vous devez également fournir une liste contenant les noms, les types et les chaînes de connexion des bases de données à sauvegarder.

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

Une sauvegarde de l’application débute immédiatement après la réception de la requête. Le processus de sauvegarde peut prendre un certain temps. La réponse HTTP contient un ID que vous pouvez utiliser dans une autre requête pour connaître l’état de la sauvegarde. Voici un exemple du corps de la réponse HTTP à notre requête de sauvegarde.

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

> [!NOTE]
> Vous pouvez rencontrer des messages d’erreur dans la propriété du journal de la réponse HTTP.
> 
> 

<a name="schedule-automatic-backups"></a>

## <a name="schedule-automatic-backups"></a>Planification de sauvegardes automatiques
Si vous pouvez sauvegarder une application à la demande, vous avez également la possibilité de planifier l’exécution automatique d’une sauvegarde.

### <a name="set-up-a-new-automatic-backup-schedule"></a>Configurer une nouvelle planification de sauvegarde automatique
Pour configurer une planification de sauvegarde, envoyez une demande **PUT** à **https://management.azure.com/subscriptions/{id-abonnement}/resourceGroups/{nom-groupe-ressources}/providers/Microsoft.Web/sites/{nom}/config/backup/**.

Voici à quoi ressemble l’URL dans notre exemple de site web. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup**

Le corps de requête doit comporter un objet JSON qui spécifie la configuration de la sauvegarde. Cet exemple reprend tous les paramètres nécessaires.

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

Cet exemple configure l’application de manière à ce qu’elle soit automatiquement sauvegardée tous les sept jours. Les paramètres **frequencyInterval** et **frequencyUnit** déterminent la fréquence à laquelle les sauvegardes sont exécutées. Les valeurs valides de **frequencyUnit** sont **hour** et **day**. Par exemple, pour sauvegarder une application toutes les 12 heures, définissez le paramètre frequencyInterval sur 12 et attribuez la valeur « hour » au paramètre frequencyUnit.

Les anciennes sauvegardes sont automatiquement supprimées du compte de stockage. Vous pouvez contrôler l’ancienneté des sauvegardes en définissant le paramètre **retentionPeriodInDays** . Si vous souhaitez qu’au moins une sauvegarde soit toujours enregistrée, quelle que soit son ancienneté, définissez le paramètre **keepAtLeastOneBackup** sur true.

### <a name="get-the-automatic-backup-schedule"></a>Obtenir la planification de sauvegarde automatique
Pour obtenir la configuration de sauvegarde d’une application, envoyez une demande **POST** à l’URL **https://management.azure.com/subscriptions/{id-abonnement}/resourceGroups/{nom-groupe-ressources}/providers/Microsoft.Web/sites/{nom}/config/backup/list**.

L’URL de notre exemple de site est **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list**.

<a name="get-backup-status"></a>

## <a name="get-the-status-of-a-backup"></a>Informations sur l’état d’une sauvegarde
Selon la taille de l’application, une sauvegarde peut prendre un certain temps. Les sauvegardes peuvent également échouer, dépasser le délai d’attente ou n’aboutir que partiellement. Pour consulter l’état de toutes les sauvegardes d’une application, envoyez une demande **GET** à l’URL **https://management.azure.com/subscriptions/{id-abonnement}/resourceGroups/{nom-groupe-ressources}/providers/Microsoft.Web/sites/{nom}/backups**.

Pour consulter l’état d’une sauvegarde spécifique, envoyez une demande GET à l’URL **https://management.azure.com/subscriptions/{id-abonnement}/resourceGroups/{nom-groupe-ressources}/providers/Microsoft.Web/sites/{nom}/backups/{id-sauvegarde}**.

Voici à quoi ressemble l’URL dans notre exemple de site web. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

Le corps de la réponse contient un objet JSON similaire à cet exemple.

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

L’état d’une sauvegarde est un type énuméré. Voici la liste de tous les états possibles.

* 0 – InProgress : la sauvegarde a démarré mais n’est pas encore terminée.
* 1 – Failed : la sauvegarde a échoué.
* 2 – Succeeded : la sauvegarde s’est terminée correctement.
* 3 – TimedOut : la sauvegarde ne s’est pas terminée dans les temps et a été annulée.
* 4 – Created : la demande de sauvegarde est en attente mais n’a pas été démarrée.
* 5 – Skipped : la sauvegarde ne s’est pas poursuivie en raison d’un trop grand nombre de sauvegardes planifiées.
* 6 – PartiallySucceeded : la sauvegarde a réussi, mais certains fichiers n’ont pas été sauvegardés en raison de problèmes de lecture. Cela se produit généralement lorsqu’un verrou exclusif est placé sur les fichiers.
* 7 – DeleteInProgress : la sauvegarde a fait l’objet d’une demande de suppression, mais n’a pas encore été supprimée.
* 8 – DeleteFailed : la sauvegarde n’a pas pu être supprimée. Cela peut se produire si l’URL SAS utilisée pour créer la sauvegarde a expiré.
* 9 – Deleted : la sauvegarde a été supprimée avec succès.

<a name="restore-app"></a>

## <a name="restore-an-app-from-a-backup"></a>Restauration d’une application à partir d’une sauvegarde
Si votre application a été supprimée, ou si vous voulez rétablir votre application à une version antérieure, vous pouvez restaurer l’application à partir d’une sauvegarde. Pour invoquer une restauration, envoyez une demande **POST** à l’URL **https://management.azure.com/subscriptions/{id-abonnement}/resourceGroups/{nom-groupe-ressources}/providers/Microsoft.Web/sites/{nom}/backups/{id-sauvegarde}/restore**.

Voici à quoi ressemble l’URL dans notre exemple de site web. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/restore**

Dans le corps de la requête, envoyez un objet JSON qui contient les propriétés de l’opération de restauration. Voici un exemple contenant toutes les propriétés requises :

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### <a name="restore-to-a-new-app"></a>Restauration vers une nouvelle application
Lors de la restauration d’une sauvegarde, vous avez parfois intérêt à créer une application au lieu de remplacer une application existante. Pour ce faire, modifiez l’URL de requête pour pointer vers la nouvelle application que vous souhaitez créer et définissez la propriété **overwrite** dans le fichier JSON sur **false**.

<a name="delete-app-backup"></a>

## <a name="delete-an-app-backup"></a>Suppression d’une sauvegarde d’application
Pour supprimer une sauvegarde, envoyez une demande **DELETE** à l’URL **https://management.azure.com/subscriptions/{id-abonnement}/resourceGroups/{nom-groupe-ressources}/providers/Microsoft.Web/sites/{nom}/backups/{id-sauvegarde}**.

Voici à quoi ressemble l’URL dans notre exemple de site web. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

<a name="manage-sas-url"></a>

## <a name="manage-a-backups-sas-url"></a>Gestion de l’URL SAS d’une sauvegarde
Les applications Azure App Service tentent de supprimer votre sauvegarde d’Azure Storage en utilisant l’URL SAS fournie au moment de la création de la sauvegarde. Si cette URL SAS n’est plus valide, la sauvegarde ne peut pas être supprimée via l’API REST. Toutefois, vous pouvez mettre à jour l’URL SAS associée à une sauvegarde en envoyant une demande **POST** à l’URL **https://management.azure.com/subscriptions/{id-abonnement}/resourceGroups/{nom-groupe-ressources}/providers/Microsoft.Web/sites/{nom}/backups/{id-sauvegarde}/list**.

Voici à quoi ressemble l’URL dans notre exemple de site web. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/list**

Dans le corps de la requête, envoyez un objet JSON contenant la nouvelle URL SAS. Voici un exemple.

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

> [!NOTE]
> Pour des raisons de sécurité, l’URL SAS associée à une sauvegarde n’est pas renvoyée lors de l’envoi d’une requête GET pour une sauvegarde spécifique. Si vous souhaitez afficher l’URL SAS associée à une sauvegarde, envoyez une requête POST à l’URL indiquée ci-dessus. Incluez un objet JSON vide dans le corps de la requête. La réponse du serveur contient toutes les informations relatives à cette sauvegarde, notamment son URL SAS.
> 
> 

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png

