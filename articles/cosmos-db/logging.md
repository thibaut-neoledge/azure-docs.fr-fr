---
title: "Journalisation des diagnostics Azure Cosmos DB | Microsoft Docs"
description: "Utilisez ce didacticiel pour vous familiariser avec la journalisation d’Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mimig
ms.openlocfilehash: 34c46fb282ad154225f5ee8ef544bc8da1c50016
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Journalisation des diagnostics Azure Cosmos DB

Lorsque vous avez commencé à utiliser une ou plusieurs bases de données Azure Cosmos DB, vous pouvez surveiller comment et quand vos bases de données font l’objet d’un accès. La journalisation des diagnostics dans Azure Cosmos DB vous permet d’effectuer cette analyse. En activant la journalisation des diagnostics, vous pouvez envoyer des journaux au [Stockage Azure](https://azure.microsoft.com/services/storage/), les transmettre à [Azure Event Hubs](https://azure.microsoft.com/en-us/services/event-hubs/) et/ou les exporter dans un espace de travail [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) via [Log Analytics](https://azure.microsoft.com/services/log-analytics/).

![Journalisation des diagnostics dans le Stockage, Event Hubs ou Operations Management Suite via Log Analytics](./media/logging/azure-cosmos-db-logging-overview.png)

Utilisez ce didacticiel pour commencer à utiliser la journalisation Azure Cosmos DB via le portail Azure, l’interface CLI ou PowerShell.

## <a name="what-is-logged"></a>Quels sont les éléments journalisés ?

* Toutes les demandes API DocumentDB (SQL) REST authentifiées sont journalisées, ce qui inclut les demandes ayant échoué suite à des autorisations d’accès incorrectes, des erreurs système ou des demandes erronées. Actuellement, la prise en charge des API MongoDB, Graphe et Table n’est pas disponible.
* Les opérations sur la base de données même, notamment les opérations CRUD sur l’ensemble des documents, des conteneurs et des bases de données.
* Les opérations sur les clés de compte, notamment la création, la modification ou la suppression de ces clés.
* les requêtes non authentifiées qui génèrent une réponse 401. Par exemple, les requêtes qui ne possèdent pas de jeton de porteur, qui sont incorrectes, qui ont expiré ou qui comportent un jeton non valide.

## <a name="prerequisites"></a>Composants requis
Pour suivre ce didacticiel, vous avez besoin des ressources suivantes :

* Un compte, une base de données et un conteneur Azure Cosmos DB existants. Pour obtenir des instructions sur la création de ces ressources, consultez [Créer un compte de base de données à l’aide du portail Azure](create-documentdb-dotnet.md#create-a-database-account), [Exemples d’interface CLI](cli-samples.md) ou [Exemples PowerShell](powershell-samples.md).


## <a name="turn-on-logging-in-the-azure-portal"></a>Activer la journalisation dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com), dans votre compte Azure Cosmos DB, cliquez sur **Journaux de diagnostic** dans le volet de navigation de gauche, puis sur **Activer les diagnostics**.

    ![Activer la journalisation des diagnostics pour Azure Cosmos DB dans le portail Azure](./media/logging/turn-on-portal-logging.png)

2. Dans la page **Paramètres de diagnostic**, procédez comme suit : 

    * **Nom**. Entrez un nom pour les journaux à créer.

    * **Archive vers un compte de stockage**. Pour utiliser cette option, vous avez besoin d’un compte de stockage existant auquel vous connecter. Pour créer un compte de stockage dans le portail, consultez [Créer un compte de stockage](../storage/common/storage-create-storage-account.md) et suivez les instructions pour créer un compte Resource Manager à usage général. Puis revenez à cette page dans le portail pour sélectionner votre compte de stockage. L’affichage des comptes de stockage nouvellement créés dans le menu déroulant peut prendre quelques minutes.
    * **Transmettre à un Event Hub**. Pour utiliser cette option, vous avez besoin d’un espace de noms Event Hub existant et d’un Event Hub auquel vous connecter. Pour créer un espace de noms Event Hubs, consultez [Créer un espace de noms Event Hubs et un Event Hub à l’aide du portail Azure](../event-hubs/event-hubs-create.md). Puis revenez à cette page dans le portail pour sélectionner l’espace de noms Event Hub et le nom de la stratégie.
    * **Envoyer à Log Analytics**. Pour utiliser cette option, utilisez un espace de travail existant ou créez un espace de travail Operations Management Suite en suivant les invites dans le portail.
    * **Journaliser DataPlaneRequests**. Si vous archivez vers un compte de stockage, vous pouvez sélectionner la période de rétention pour les journaux de diagnostic en sélectionnant **DataPlaneRequests** et en choisissant le nombre de jours pendant lesquels conserver les journaux. Les journaux sont supprimés automatiquement après l’expiration de la période de rétention. 

3. Cliquez sur **Enregistrer**.

    Vous pouvez revenir à cette page à tout moment pour modifier les paramètres de journalisation des diagnostics pour votre compte.

## <a name="turn-on-logging-using-cli"></a>Activation de la journalisation à l’aide de l’interface CLI

Pour activer la journalisation des métriques et diagnostics à l’aide d’Azure CLI, utilisez les commandes suivantes :

- Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   `resourceId` est le nom du compte Azure Cosmos DB. `storageId` est le nom du compte de stockage auquel vous souhaitez envoyer les journaux.

- Pour activer la diffusion en continu des journaux de diagnostic vers un Event Hub, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   `resourceId` est le nom du compte Azure Cosmos DB. `serviceBusRuleId` est une chaîne au format suivant :

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Pour activer l’envoi des journaux de diagnostic à un espace de travail Log Analytics, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

## <a name="turn-on-logging-using-powershell"></a>Activation de la journalisation à l’aide de PowerShell

Pour activer la journalisation à l’aide de PowerShell, vous avez besoin d’Azure Powershell, avec la version minimale 1.0.1.

Pour installer Azure PowerShell et l’associer à votre abonnement Azure, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

Si vous avez déjà installé Azure PowerShell et que vous ne connaissez pas la version que vous utilisez, à partir de la console PowerShell, entrez `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Se connecter à vos abonnements
Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure avec la commande suivante :  

```powershell
Login-AzureRmAccount
```

Dans la fenêtre contextuelle de votre navigateur, entrez votre nom d’utilisateur et votre mot de passe Azure. Azure PowerShell obtient alors tous les abonnements associés à ce compte et utilise par défaut le premier.

Si vous disposez de plusieurs abonnements, vous devrez peut-être en spécifier un en particulier, celui qui a été utilisé pour créer votre Azure Key Vault. Tapez la commande suivante pour afficher les abonnements de votre compte :

```powershell
Get-AzureRmSubscription
```

Ensuite, pour spécifier l’abonnement associé au compte Azure Cosmos DB que vous allez journaliser, tapez :

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Si plusieurs abonnements sont associés à votre compte, il est important de spécifier l’abonnement.
>
>

Pour plus d’informations sur la configuration d’Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Création d’un nouveau compte de stockage pour vos journaux
Bien que vous puissiez utiliser un compte de stockage existant pour vos journaux, dans ce didacticiel nous en créons un nouveau qui sera dédié à vos journaux Azure Cosmos DB. Pour plus de commodité, nous stockons les détails du compte de stockage dans une variable nommée **sa**.

Pour faciliter encore la gestion, dans ce didacticiel nous utilisons le groupe de ressources qui contient notre base de données Azure Cosmos DB. Remplacez les valeurs de ContosoResourceGroup, contosocosmosdblogs et « Nord du centre des États-Unis » pour vos propres valeurs, comme il convient :

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Si vous décidez d’utiliser un compte de stockage existant, vous devez utiliser le même abonnement que pour votre abonnement Azure Cosmos DB, ainsi que le modèle de déploiement Resource Manager plutôt que le modèle de déploiement Classic.
>
>

### <a id="identify"></a>Identifier le compte Azure Cosmos DB pour vos journaux
Définissez le nom du compte Azure Cosmos DB sur une variable nommée **account**, où ResourceName est le nom du compte Azure Cosmos DB.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Activation de la journalisation
Pour activer la journalisation pour Azure Cosmos DB, utilisez la cmdlet Set-AzureRmDiagnosticSetting, ainsi que les variables pour le nouveau compte de stockage, le compte Azure Cosmos DB et la catégorie pour laquelle vous souhaitez activer les journaux. Exécutez la commande suivante, en définissant l’indicateur **-Enabled** sur **$true** :

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

La sortie de la commande doit ressembler à ce qui suit :

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

L’activation de la journalisation de votre base de données est à présent activée et les informations sont enregistrées dans votre compte de stockage.

Si vous le souhaitez, vous pouvez également définir une stratégie de rétention pour vos journaux, par exemple la suppression automatique des anciens journaux. Par exemple, définissez une stratégie de rétention en attribuant à l’indicateur **-RetentionEnabled** la valeur **$true** et en définissant le paramètre **-RetentionInDays** sur **90** afin que les journaux antérieurs à 90 jours soient automatiquement supprimés.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Accéder à vos journaux
Les journaux Azure Cosmos DB pour la catégorie **DataPlaneRequests** sont stockés dans le conteneur **insights-logs-data-plane-requests** dans le compte de stockage spécifié. 

Commencez par créer une variable pour le nom du conteneur. Ce nom sera utilisé dans le reste de cette procédure pas à pas.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Pour répertorier tous les objets blob présents dans ce conteneur, saisissez :

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Le résultat ressemble à ce qui suit :

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Comme vous pouvez le voir dans cette sortie, les objets blob suivent une convention d’affectation de noms : `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Les valeurs de date et d’heure utilisent UTC.

Le même compte de stockage pouvant être utilisé pour collecter les journaux de plusieurs ressources, l’ID complet de ressource dans le nom de l’objet blob est très utile si vous voulez accéder seulement aux objets blob dont vous avez besoin et les télécharger. Mais avant cela, nous aborderons le téléchargement de tous les objets blob.

Tout d’abord, créez un dossier pour télécharger les objets blob. Par exemple :

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Procurez-vous la liste de tous les objets blob :  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Adressez cette liste via « Get-AzureStorageBlobContent » pour télécharger les objets blob dans notre dossier de destination :

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Lorsque vous exécutez cette seconde commande, le délimiteur **/** présent dans les noms d’objet blob crée une structure de dossiers complète sous le dossier de destination. Cette structure de dossiers est utilisée pour télécharger et stocker les objets blob en tant que fichiers.

Pour télécharger les objets blob de façon sélective, utilisez des caractères génériques. Par exemple :

* Si vous disposez de plusieurs bases de données et souhaitez télécharger les journaux d’une seule d’entre elles nommée CONTOSOCOSMOSDB3 :

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Si vous disposez de plusieurs groupes de ressources et souhaitez télécharger les journaux d’un seul d’entre eux, utilisez `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Si vous souhaitez télécharger tous les journaux du mois de juillet 2017, utilisez `-Blob '*/year=2017/m=07/*'` :

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Vous êtes maintenant prêt à commencer les recherches dans le contenu des journaux. Mais avant de passer à cette étape, il peut s’avérer utile de connaître deux autres paramètres de `Get-AzureRmDiagnosticSetting` :

* Pour interroger l’état des paramètres de diagnostic de votre ressource de base de données : `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
* Pour désactiver la journalisation de la catégorie **DataPlaneRequests** pour votre ressource de compte de base de données : `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`

## <a id="interpret"></a>Interpréter vos journaux Azure Cosmos DB
Les objets blob individuels sont stockés sous forme de texte en tant qu’objet blob JSON. Ce JSON est un exemple d’entrée de journal :

    {
        "records":
        [
            {
               "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
               "resourceId": "contosocosmosdb",
               "category": "DataPlaneRequests",
               "operationName": "Query",
               "resourceType": "Database",
               "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
               "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
               "resourceType": "Database","statusCode": "200","documentResourceId": "",`
               "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
               "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
            }
        ]
    }


Le tableau suivant répertorie les noms de champ et les descriptions.

| Nom du champ | Description |
| --- | --- |
| time |Date et heure (UTC) de l’opération. |
| resourceId |Le compte Azure Cosmos DB pour lequel les journaux sont activés.|
| category |Pour les journaux Azure Cosmos DB, DataPlaneRequests est la seule valeur disponible. |
| operationName |Nom de l’opération. Cette valeur peut être l’une des opérations suivantes : Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed ou Upsert.   |
| properties |Le contenu de ce champ est décrit dans le tableau suivant. |

Le tableau suivant répertorie les champs journalisés dans le champ des propriétés.

| Nom de champ de propriété | Description |
| --- | --- |
| activityId | GUID unique de l’opération journalisée. |
| userAgent |Chaîne qui spécifie l’agent utilisateur client effectuant la demande. Le format est {nom de l’agent utilisateur}/{version}.|
| resourceType | Type de la ressource faisant l’objet de l’accès. Cette valeur peut être l’un des types de ressources suivants : Database, Collection, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction ou Offer. |
| statusCode |État de réponse de l’opération. |
| requestResourceId | L’ID de ressource se rapportant à la demande, peut pointer vers databaseRid, collectionRid ou documentRid en fonction de l’opération.|
| clientIpAddress |Adresse IP du client. |
| requestCharge | Nombre d’unités de requête utilisées par l’opération. |
| collectionRid | ID unique de la collection.|
| duration | Durée de l’opération, en cycles. |
| requestLength |Longueur de la demande, en octets. |
| responseLength | Longueur de la réponse, en octets.|
| resourceTokenUserRid | Ce champ n’est pas vide lorsque des [jetons de ressource](https://docs.microsoft.com/en-us/azure/cosmos-db/secure-access-to-data#resource-tokens) sont utilisés pour l’authentification et pointe vers l’ID de ressource de l’utilisateur. |

## <a name="managing-your-logs"></a>Gestion de vos journaux

Les journaux sont disponibles dans votre compte dans les deux heures suivant le moment où l’opération Azure Cosmos DB a été effectuée. C’est à vous de gérer vos journaux dans votre compte de stockage :

* Utilisez les méthodes de contrôle d’accès Azure standard pour assurer la sécurité de vos journaux en limitant l’accès à ces derniers.
* Supprimez les journaux que vous ne souhaitez plus conserver dans votre compte de stockage.
* La période de rétention pour les demandes de plan de données archivées dans un compte de stockage est configurée dans le portail lorsque l’option **Journaliser DataPlaneRequests** est sélectionnée. Pour modifier ce paramètre, consultez [Activer la journalisation dans le portail Azure](#turn-on-logging-in-the-azure-portal).

## <a name="next-steps"></a>Étapes suivantes

- Pour comprendre non seulement comment activer la journalisation, mais aussi les métriques et les catégories de journaux prises en charge par les différents services Azure, consultez les articles [Vue d’ensemble des métriques dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) et [Présentation des journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
- Pour en savoir plus sur les concentrateurs d’événements, lisez les articles suivants :
   - [Qu’est-ce qu’Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) ?
   - [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Consultez [Télécharger les journaux de métriques et diagnostics de Stockage Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs).
