---
title: "Journalisation d’Azure Key Vault | Microsoft Docs"
description: "Utilisez ce didacticiel pour vous aider à vous familiariser avec la journalisation du coffre de clés."
services: key-vault
documentationcenter: 
author: cabailey
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/31/2016
ms.author: cabailey
translationtype: Human Translation
ms.sourcegitcommit: c40545833da86426d3e71955b8eb8627db3c1e4b
ms.openlocfilehash: 50a85747a3414e180bcd9129899fef7ffdaebc8d


---
# <a name="azure-key-vault-logging"></a> Journalisation d’Azure Key Vault
Azure Key Vault est disponible dans la plupart des régions. Pour plus d’informations, consultez la [page de tarification de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduction
Une fois que vous avez créé un ou plusieurs coffres de clés, vous voulez sans doute contrôler qui accède à ces derniers, par quel moyen et quand. Pour ce faire, vous pouvez activer la journalisation du coffre de clés, ce qui permet d’enregistrer les informations dans un compte de stockage Azure que vous fournissez. Un nouveau conteneur nommé **insights-logs-auditevent** est automatiquement créé pour le compte de stockage spécifié, et vous pouvez utiliser ce même compte pour recueillir les journaux de plusieurs coffres de clés.

Vous pouvez accéder aux informations de journalisation au plus 10 minutes après l’opération sur le coffre de clés. Dans la plupart des cas, ce sera plus rapide.  C’est à vous de gérer vos journaux dans votre compte de stockage :

* Utilisez les méthodes de contrôle d’accès Azure standard pour assurer la sécurité de vos journaux en limitant l’accès à ces derniers.
* Supprimez les journaux que vous ne souhaitez plus conserver dans votre compte de stockage.

Utilisez ce didacticiel pour vous familiariser avec Azure Key Vault pour créer votre compte de stockage, activer la journalisation et interpréter les informations de journalisation collectées.  

> [!NOTE]
> Ce didacticiel n’inclut pas d’instructions pour créer des coffres de clés, des clés ou des clés secrètes. Pour plus d’informations, consultez [Prise en main d’Azure Key Vault](key-vault-get-started.md). Ou, pour obtenir des instructions de l'interface de ligne de commande interplateforme, consultez [ce didacticiel équivalent](key-vault-manage-with-cli.md).
> 
> Actuellement, vous ne pouvez pas configurer Azure Key Vault dans le portail Azure. Au lieu de cela, vous devez suivre ces instructions Azure PowerShell.
> 
> 

Vous pouvez visualiser les journaux que vous collectez à l’aide de Log Analytics dans Operations Management Suite. Pour plus d’informations, consultez [Azure Key Vault (Preview) solution in Log Analytics](../log-analytics/log-analytics-azure-key-vault.md)(Solution Azure Key Vault (version préliminaire) dans Log Analytics).

Pour plus d’informations générales sur Azure Key Vault, consultez la page [Présentation d’Azure Key Vault](key-vault-whatis.md)

## <a name="prerequisites"></a>Composants requis
Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un coffre de clés existant que vous utilisez déjà.  
* Azure PowerShell, **version 1.0.1 minimum**. Pour installer Azure PowerShell et l’associer à votre abonnement Azure, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs). Si vous avez déjà installé Azure PowerShell et que vous ne connaissez pas la version que vous utilisez, à partir de la console Azure PowerShell, entrez `(Get-Module azure -ListAvailable).Version`.  
* Espace de stockage suffisant sur Azure pour vos journaux de coffre de clés.

## <a name="a-idconnectaconnect-to-your-subscriptions"></a><a id="connect"></a>Se connecter à vos abonnements
Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure avec la commande suivante :  

    Login-AzureRmAccount

Dans la fenêtre contextuelle de votre navigateur, entrez votre nom d’utilisateur et votre mot de passe Azure. Azure PowerShell obtient alors tous les abonnements associés à ce compte et utilise par défaut le premier.

Si vous disposez de plusieurs abonnements, vous devrez peut-être en spécifier un en particulier, celui qui a été utilisé pour créer votre Azure Key Vault. Tapez la commande suivante pour afficher les abonnements de votre compte :

    Get-AzureRmSubscription

Ensuite, pour spécifier l’abonnement associé au coffre de clés que vous allez consigner, tapez :

    Set-AzureRmContext -SubscriptionId <subscription ID>

Pour plus d’informations sur la configuration d’Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="a-idstorageacreate-a-new-storage-account-for-your-logs"></a><a id="storage"></a>Création d’un nouveau compte de stockage pour vos journaux
Bien que vous puissiez utiliser un compte de stockage existant pour vos journaux, nous allons en créer un nouveau qui sera dédié à vos journaux de coffre de clés. Pour plus de commodité, en prévision du moment où vous devrez le spécifier par la suite, nous allons enregistrer les détails dans une variable nommée **sa**.

Pour faciliter encore la gestion, nous allons utiliser le groupe de ressources qui contient votre coffre de clés. Dans le [didacticiel de mise en route](key-vault-get-started.md), ce groupe de ressources se nomme **ContosoResourceGroup** et nous allons continuer d’utiliser l’emplacement Asie orientale. Remplacez ces valeurs par les vôtres, selon le cas :

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


> [!NOTE]
> Si vous décidez d’utiliser un compte de stockage existant, vous devez utiliser le même abonnement que pour votre coffre de clés, ainsi que le modèle de déploiement Resource Manager plutôt que le modèle de déploiement Classic.
> 
> 

## <a name="a-ididentifyaidentify-the-key-vault-for-your-logs"></a><a id="identify"></a>Identification du coffre de clés pour vos journaux
Dans notre didacticiel de prise en main, le nom de notre coffre de clés était **ContosoKeyVault**, donc nous allons continuer à utiliser ce nom et stocker les détails dans une variable nommée **kv** :

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a name="a-idenableaenable-logging"></a><a id="enable"></a>Activation de la journalisation
Pour activer la journalisation du coffre de clés, nous allons utiliser l’applet de commande Set-AzureRmDiagnosticSetting, ainsi que les variables que nous avons créées pour notre compte de stockage et notre coffre de clés. Nous allons également définir l’indicateur **-Enabled** sur **$true** et la catégorie sur AuditEvent (la seule catégorie pour la journalisation de Key Vault) :

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

Le résultat de l’opération inclut :

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


L’activation de la journalisation de votre coffre de clés est à présent activée et les informations sont enregistrées dans votre compte de stockage.

Si vous le souhaitez, vous pouvez également définir une stratégie de rétention pour vos journaux, par exemple la suppression automatique des anciens journaux. Par exemple, définissez une stratégie de rétention en attribuant à l’indicateur **-RetentionEnabled** la valeur **$true** et en définissant le paramètre **-RetentionInDays** sur **90** afin que les journaux antérieurs à 90 jours soient automatiquement supprimés.

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

Éléments consignés :

* toutes les demandes API REST authentifiées sont enregistrées, ce qui inclut des requêtes ayant échoué suite à des demandes, des erreurs système ou des autorisations d’accès incorrectes ;
* les opérations du coffre de clés lui-même, notamment la création, la suppression la définition des stratégies d’accès aux coffres de clés, et la mise à jour des attributs de coffre de clés (par exemple, les balises) ;
* les opérations sur les clés et les clés secrètes dans le coffre de clés, notamment la création, la modification ou la suppression de ces clés ou ces clés secrètes ; les opérations telles que la signature, la vérification, le chiffrement, le déchiffrement, l’encapsulage et le désencapsulage des clés, l’obtention des clés secrètes, la liste des clés et des clés secrètes et leurs versions.
* les requêtes non authentifiées qui génèrent une réponse 401. Par exemple, les requêtes qui ne possèdent pas de jeton de porteur, qui sont incorrectes, qui ont expiré ou qui comportent un jeton non valide.  

## <a name="a-idaccessaaccess-your-logs"></a><a id="access"></a>Accéder à vos journaux
Les journaux de coffre de clés sont stockés dans le conteneur **insights-logs-auditevent** du compte de stockage que vous avez fourni. Pour répertorier tous les objets blob présents dans ce conteneur, saisissez :

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

Le résultat ressemble à ce qui suit :

**URI du conteneur : https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**

**Nom**

- - -
**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json****

Comme vous pouvez le voir dans cette sortie, les objets blob suivent une convention d’affectation de noms : **resourceId =<ARM resource ID>/y =<year>/m =<month>/d =<day of month>/h =<hour>/m =<minute>/filename.json**

Les valeurs de date et d’heure utilisent UTC.

Le même compte de stockage pouvant être utilisé pour collecter les journaux de plusieurs ressources, l’ID complet de ressource dans le nom de l’objet blob est très utile si vous voulez accéder seulement aux objets blob dont vous avez besoin et les télécharger. Mais avant cela, nous aborderons le téléchargement de tous les objets blob.

Tout d’abord, créez un dossier pour télécharger les objets blob. Par exemple :

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

Procurez-vous la liste de tous les objets blob :  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Adressez cette liste via « Get-AzureStorageBlobContent » pour télécharger les objets blob dans notre dossier de destination :

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

Lorsque vous exécutez cette seconde commande, le délimiteur **/** présent dans les noms d’objet blob crée une structure de dossiers complète sous le dossier de destination, structure qui servira à télécharger et à stocker les objets blob en tant que fichiers.

Pour télécharger les objets blob de façon sélective, utilisez des caractères génériques. Par exemple :

* Si vous disposez de plusieurs coffres de clés et souhaitez télécharger les journaux d’un seul d’entre eux nommé CONTOSOKEYVAULT3 :
  
        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
* Si vous disposez de plusieurs groupes de ressources et souhaitez télécharger les journaux d’un seul d’entre eux, utilisez `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:
  
        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
* Si vous souhaitez télécharger tous les journaux du mois de janvier 2016, utilisez `-Blob '*/year=2016/m=01/*'`:
  
        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Vous êtes maintenant prêt à commencer les recherches dans le contenu des journaux. Mais avant de passer à cette étape, il peut s’avérer utile de connaître deux autres paramètres de Get-AzureRmDiagnosticSetting :

* Pour interroger l’état des paramètres de diagnostic de votre ressource de coffre de clés : `Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`
* Pour désactiver la journalisation de votre ressource de coffre de clés : `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`

## <a name="a-idinterpretainterpret-your-key-vault-logs"></a><a id="interpret"></a>Interpréter vos journaux Key Vault
Les objets blob individuels sont stockés sous forme de texte en tant qu’objet blob JSON. Voici un exemple d’entrée de journal après l’exécution de `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }


Le tableau suivant répertorie les noms de champ et les descriptions.

| Nom du champ | Description |
| --- | --- |
| time |Date et heure (UTC) |
| resourceId |ID de ressource Azure Resource Manager Pour les journaux de coffre de clés, il s’agit toujours de l’ID de ressource du coffre de clés. |
| operationName |Nom de l’opération, comme indiqué dans le tableau suivant. |
| operationVersion |Il s’agit de la version de l’API REST demandée par le client. |
| category |Pour les journaux de coffre de clés, AuditEvent est la seule valeur disponible. |
| resultType |Résultat de la demande d’API REST. |
| resultSignature |État HTTP |
| resultDescription |Description supplémentaire du résultat le cas échéant. |
| durationMS |Délai nécessaire pour répondre à la demande API REST, en millisecondes. La latence du réseau n’est pas incluse dans ce chiffre et donc, le temps mesuré côté client peut ne pas correspondre à cette durée. |
| callerIpAddress |Adresse IP du client qui a effectué la demande. |
| correlationId |GUID facultatif que le client peut transférer pour mettre en corrélation les journaux côté client avec les journaux côté service (Key Vault). |
| identité |Identité issue du jeton qui a été présenté lors de la création de la demande de l’API REST. Il s’agit généralement d’un « utilisateur », d’« un principal de service » ou d’une combinaison « utilisateur + appId », comme dans le cas d’une demande résultant d’une applet de commande PowerShell Azure. |
| properties |Ce champ contient des informations différentes en fonction de l’opération (operationName). Dans la plupart des cas, il contient des informations client (chaîne useragent transmise par le client), l’URI de requête API REST exacte et le code d’état HTTP. En outre, lorsqu’un objet est retourné suite à une demande (par exemple, KeyCreate ou VaultGet), il contient également l’URI de clé (sous la forme « id »), l’URI d’archivage ou l’URI de clé secrète. |

Les valeurs du champ **operationName** sont au format ObjectVerb. Par exemple :

* Toutes les opérations de coffre de clés sont au format « Vault`<action>` », comme `VaultGet` et `VaultCreate`.
* Toutes les opérations de clé sont au format « Key`<action>` », comme `KeySign` et `KeyList`.
* Toutes les opérations de secret sont au format « Secret`<action>` », comme `SecretGet` et `SecretListVersions`.

Le tableau suivant répertorie les éléments operationName et la commande API REST correspondante.

| operationName | Commande API REST |
| --- | --- |
| Authentification |Via le point de terminaison Azure Active Directory (Azure AD) |
| VaultGet |[Obtention des informations sur un coffre de clés](https://msdn.microsoft.com/en-us/library/azure/mt620026.aspx) |
| VaultPut |[Création ou mise à jour d’un coffre de clés](https://msdn.microsoft.com/en-us/library/azure/mt620025.aspx) |
| VaultDelete |[Suppression d’un coffre de clés](https://msdn.microsoft.com/en-us/library/azure/mt620022.aspx) |
| VaultPatch |[Mise à jour d’un coffre de clés](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultList |[Liste de l’ensemble des coffres de clés dans un groupe de ressources](https://msdn.microsoft.com/en-us/library/azure/mt620027.aspx) |
| KeyCreate |[Création d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn903634.aspx) |
| KeyGet |[Obtention des informations sur une clé](https://msdn.microsoft.com/en-us/library/azure/dn878080.aspx) |
| KeyImport |[Importation d’une clé dans un coffre](https://msdn.microsoft.com/en-us/library/azure/dn903626.aspx) |
| KeyBackup |[Sauvegarde d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn878058.aspx). |
| KeyDelete |[Suppression d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn903611.aspx) |
| KeyRestore |[Restauration d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn878106.aspx) |
| KeySign |[Signature avec une clé](https://msdn.microsoft.com/en-us/library/azure/dn878096.aspx) |
| KeyVerify |[Vérification à l’aide d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn878082.aspx) |
| KeyWrap |[Encapsulage d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx) |
| KeyUnwrap |[Désencapsulage d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn878079.aspx) |
| KeyEncrypt |[Chiffrement avec une clé](https://msdn.microsoft.com/en-us/library/azure/dn878060.aspx) |
| KeyDecrypt |[Déchiffrement avec une clé](https://msdn.microsoft.com/en-us/library/azure/dn878097.aspx) |
| KeyUpdate |[Mise à jour d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn903616.aspx) |
| KeyList |[Liste des clés dans un coffre](https://msdn.microsoft.com/en-us/library/azure/dn903629.aspx) |
| KeyListVersions |[Liste des versions d’une clé](https://msdn.microsoft.com/en-us/library/azure/dn986822.aspx) |
| SecretSet |[Création d’une clé secrète](https://msdn.microsoft.com/en-us/library/azure/dn903618.aspx) |
| SecretGet |[Obtention d’une clé secrète](https://msdn.microsoft.com/en-us/library/azure/dn903633.aspx) |
| SecretUpdate |[Mise à jour d’une clé secrète](https://msdn.microsoft.com/en-us/library/azure/dn986818.aspx) |
| SecretDelete |[Suppression d’une clé secrète](https://msdn.microsoft.com/en-us/library/azure/dn903613.aspx) |
| SecretList |[Liste des clés secrètes d’un coffre](https://msdn.microsoft.com/en-us/library/azure/dn903614.aspx) |
| SecretListVersions |[Liste des versions d’une clé secrète](https://msdn.microsoft.com/en-us/library/azure/dn986824.aspx) |

## <a name="a-idnextanext-steps"></a><a id="next"></a>Étapes suivantes
Pour accéder à un didacticiel utilisant Azure Key Vault dans une application web, consultez l’article [Utilisation d’Azure Key Vault à partir d’une application web](key-vault-use-from-web-application.md).

Pour les références de programmation, consultez le [guide du développeur de coffre de clés Azure](key-vault-developers-guide.md).

Pour obtenir la liste des applets de commande Azure PowerShell 1.0 pour Azure Key Vault, consultez l’article [Azure Key Vault Cmdlets (Applets de commande Azure Key Vault)](https://msdn.microsoft.com/library/azure/dn868052.aspx).

Pour accéder à un didacticiel sur la rotation des clés et les journaux d’audit avec Azure Key Vault, consultez l’article [Configuration du coffre de clés avec une rotation des clés et un audit de bout en bout](key-vault-key-rotation-log-monitoring.md).




<!--HONumber=Dec16_HO1-->


