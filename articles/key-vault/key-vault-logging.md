<properties
	pageTitle="Journalisation d’Azure Key Vault | Microsoft Azure"
	description="Utilisez ce didacticiel pour vous aider à vous familiariser avec la journalisation du coffre de clés."
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/08/2016"
	ms.author="cabailey"/>

# Journalisation d’Azure Key Vault #
Azure Key Vault est disponible dans la plupart des régions. Pour plus d’informations, consultez la [page de tarification de Key Vault](../../../../pricing/details/key-vault/).

## Introduction  
Une fois que vous avez créé un ou plusieurs coffres de clés, vous voulez sans doute contrôler qui accède à ces derniers, par quel moyen et quand. Pour ce faire, vous pouvez activer la journalisation du coffre de clés, ce qui permet d’enregistrer les informations dans un compte de stockage Azure que vous fournissez. Un nouveau conteneur nommé **insights-logs-auditevent** est automatiquement créé pour le compte de stockage spécifié, et vous pouvez utiliser ce même compte pour recueillir les journaux de plusieurs coffres de clés.

Vous pouvez accéder aux informations de journalisation au plus 10 minutes après l’opération sur le coffre de clés. Dans la plupart des cas, ce sera plus rapide. C’est à vous de gérer vos journaux dans votre compte de stockage :

- Utilisez les méthodes de contrôle d’accès Azure standard pour assurer la sécurité de vos journaux en limitant l’accès à ces derniers.
- Supprimez les journaux que vous ne souhaitez plus conserver dans votre compte de stockage. 

Utilisez ce didacticiel pour vous familiariser avec Azure Key Vault pour créer votre compte de stockage, activer la journalisation et interpréter les informations de journalisation collectées.


>[AZURE.NOTE]Ce didacticiel n’inclut pas d’instructions pour créer des coffres de clés, des clés ou des clés secrètes. Pour plus d’informations, consultez [Prise en main d’Azure Key Vault](key-vault-get-started.md). Ou, pour obtenir des instructions de l'interface de ligne de commande interplateforme, consultez [ce didacticiel équivalent](key-vault-manage-with-cli.md).
>
>Actuellement, vous ne pouvez pas configurer Azure Key Vault dans le portail Azure. Au lieu de cela, vous devez suivre ces instructions Azure PowerShell.

Pour plus d’informations générales sur Azure Key Vault, consultez la page [Présentation d’Azure Key Vault](key-vault-whatis.md)

## Configuration requise

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- Un coffre de clés existant que vous utilisez déjà.  
- Azure PowerShell, **version 1.0.1 minimum**. Pour installer Azure PowerShell et l’associer à votre abonnement Azure, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md). Si vous avez déjà installé Azure PowerShell et que vous ne connaissez pas la version que vous utilisez, à partir de la console Azure PowerShell, entrez `(Get-Module azure -ListAvailable).Version`.  
- Espace de stockage suffisant sur Azure pour vos journaux de coffre de clés.


## <a id="connect"></a>Connexion à vos abonnements ##

Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure avec la commande suivante :

    Login-AzureRmAccount 

Dans la fenêtre contextuelle de votre navigateur, entrez votre nom d’utilisateur et votre mot de passe Azure. Azure PowerShell obtient alors tous les abonnements associés à ce compte et utilise par défaut le premier.

Si vous disposez de plusieurs abonnements, vous devrez peut-être en spécifier un en particulier, celui qui a été utilisé pour créer votre coffre de clés Azure. Saisissez la commande suivante pour afficher les abonnements de votre compte :

    Get-AzureRmSubscription

Ensuite, pour spécifier l’abonnement associé au coffre de clés auquel vous allez vous connecter, saisissez :

    Set-AzureRmContext -SubscriptionId <subscription ID>

Pour plus d’informations sur la configuration d’Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).


## <a id="storage"></a>Création d’un nouveau compte de stockage pour vos journaux ##

Bien que vous puissiez utiliser un compte de stockage existant pour vos journaux, nous allons en créer un nouveau qui sera dédié à vos journaux de coffre de clés. Pour plus de commodité, en prévision du moment où vous devrez le spécifier par la suite, nous allons enregistrer les détails dans une variable nommée **sa**.

Pour faciliter encore la gestion, nous allons utiliser le groupe de ressources qui contient votre coffre de clés. Dans le [didacticiel de mise en route](key-vault-get-started.md), ce groupe de ressources se nomme **ContosoResourceGroup** et nous allons continuer d’utiliser l’emplacement Asie orientale. Remplacez ces valeurs par les vôtres, selon le cas :

	$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


>[AZURE.NOTE]Si vous décidez d’utiliser un compte de stockage existant, vous devez utiliser le même abonnement que pour votre coffre de clés, ainsi que le modèle de déploiement de Resource Manager plutôt que le modèle de déploiement Classic.

## <a id="identify"></a>Identifier le coffre de clés pour vos journaux ##

Dans notre didacticiel de prise en main, le nom de notre coffre de clés était **ContosoKeyVault**, donc nous allons continuer à utiliser ce nom et stocker les détails dans une variable nommée **kv** :

	$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Activation de la journalisation ##

Pour activer la journalisation du coffre de clés, nous allons utiliser l’applet de commande Set-AzureRmDiagnosticSetting, ainsi que les variables que nous avons créées pour notre compte de stockage et notre coffre de clés. Nous allons également définir l’indicateur **-activé** sur **$true** et la valeur de la catégorie AuditEvent (la seule catégorie pour la journalisation de coffre de clés) :

   
	Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent


Le résultat de l’opération inclut :

**Journaux**

**Activé : True**

**Catégorie : AuditEvent**

L’activation de la journalisation de votre coffre de clés est à présent activée et les informations sont enregistrées dans votre compte de stockage.

Éléments consignés :

- toutes les demandes API REST authentifiées sont enregistrées, ce qui inclut des requêtes ayant échoué suite à des demandes, des erreurs système ou des autorisations d’accès incorrectes ;
- les opérations du coffre de clés lui-même, notamment la création, la suppression la définition des stratégies d’accès aux coffres de clés, et la mise à jour des attributs de coffre de clés (par exemple, les balises) ;
- les opérations sur les clés et les clés secrètes dans le coffre de clés, notamment la création, la modification ou la suppression de ces clés ou ces clés secrètes ; les opérations telles que la signature, la vérification, le chiffrement, le déchiffrement, l’encapsulage et le désencapsulage des clés, l’obtention des clés secrètes, la liste des clés et des clés secrètes et leurs versions.

Les requêtes non authentifiées ne sont pas consignées.

## <a id="access"></a>Accès à vos journaux ##

Les journaux de coffre de clés sont stockés dans le conteneur **journaux-insights-auditevent** que vous avez fourni. Pour répertorier tous les objets blob présents dans ce conteneur, saisissez :

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

Le résultat ressemble à ce qui suit :

****Conteneur Uri : https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**


**Name**

**----**

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

Lorsque vous exécutez cette seconde commande, le délimiteur **/** présent dans les noms d’objet blob crée une structure de dossier complète sous le dossier de destination, structure qui servira à télécharger et stocker les objets blob en tant que fichiers.

Pour télécharger les objets blob de façon sélective, utilisez des caractères génériques. Par exemple :

- Si vous disposez de plusieurs coffres de clés et souhaitez télécharger les journaux d’un seul d’entre eux nommé CONTOSOKEYVAULT3 :

		Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3

- Si vous disposez de plusieurs groupes de ressources et souhaitez télécharger les journaux d’un seul d’entre eux, utilisez `-Blob '*/RESOURCEGROUPS/<resource group name>/*'` :

		Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'

- Si vous souhaitez télécharger tous les journaux du mois de janvier 2016, utilisez `-Blob '*/year=2016/m=01/*'` :

		Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Vous êtes maintenant prêt à commencer les recherches dans le contenu des journaux. Mais avant de passer à cette étape, il peut s’avérer utile de connaître deux autres paramètres de Get-AzureRmDiagnosticSetting :

- Pour interroger l’état des paramètres de diagnostic de votre ressource de coffre de clés : `Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`
 
- Pour désactiver l’enregistrement de votre ressource de coffre de clés : `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`


## <a id="interpret"></a>Interprétation de vos journaux de coffre de clés ##

Les objets blob individuels sont stockés sous forme de texte en tant qu’objet blob JSON. Voici un exemple d’entrée de journal après l’exécution de `Get-AzureRmKeyVault -VaultName 'contosokeyvault'` :

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
| ------------- |-------------|
| time | Date et heure (UTC)|
| resourceId | ID de ressource Azure Resource Manager Pour les journaux de coffre de clés, il s’agit toujours de l’ID de ressource du coffre de clés.|
| operationName | Nom de l’opération, comme indiqué dans le tableau suivant.|
| operationVersion | Il s’agit de la version de l’API REST demandée par le client.|
| category | Pour les journaux de coffre de clés, AuditEvent est la seule valeur disponible.|
| resultType | Résultat de la demande d’API REST.|
| resultSignature | État HTTP|
| resultDescription | Description supplémentaire du résultat le cas échéant.|
| durationMS | Délai nécessaire pour répondre à la demande API REST, en millisecondes. La latence du réseau n’est pas incluse dans ce chiffre et donc, le temps mesuré côté client peut ne pas correspondre à cette durée.|
| callerIpAddress | Adresse IP du client qui a effectué la demande.|
| correlationId | GUID facultatif que le client peut transférer pour mettre en corrélation les journaux côté client avec les journaux côté service (Key Vault).|
| identité | Identité issue du jeton qui a été présenté lors de la création de la demande de l’API REST. Il s’agit généralement d’un « utilisateur », d’« un principal de service » ou d’une combinaison « utilisateur + appId », comme dans le cas d’une demande résultant d’une applet de commande PowerShell Azure.|
| properties | Ce champ contient des informations différentes en fonction de l’opération (operationName). Dans la plupart des cas, il contient des informations client (chaîne useragent transmise par le client), l’URI de requête API REST exacte et le code d’état HTTP. En outre, lorsqu’un objet est retourné suite à une demande (par exemple, KeyCreate ou VaultGet), il contient également l’URI de clé (sous la forme « id »), l’URI d’archivage ou l’URI de clé secrète.|

 


Les valeurs de champ **operationName** sont au format ObjectVerb. Par exemple :

- Toutes les opérations de coffre de clés sont au format « Archivage`<action>` », comme `VaultGet` et `VaultCreate`. 

- Toutes les opérations sont au format « Clé`<action>` », comme `KeySign` et `KeyList`.

- Toutes les opérations de secret sont au format « Clé secrète`<action>` », comme `SecretGet` et `SecretListVersions`.

Le tableau suivant répertorie les éléments operationName et la commande API REST correspondante.

| operationName | Commande API REST |
| ------------- |-------------|
| Authentification | Via le point de terminaison Azure Active Directory (Azure AD)|
| VaultGet | [Obtention des informations sur un coffre de clés](https://msdn.microsoft.com/fr-FR/library/azure/mt620026.aspx)|
| VaultPut | [Création ou mise à jour d’un coffre de clés](https://msdn.microsoft.com/fr-FR/library/azure/mt620025.aspx)|
| VaultDelete | [Suppression d’un coffre de clés](https://msdn.microsoft.com/fr-FR/library/azure/mt620022.aspx)|
| VaultPatch | [Mise à jour d’un coffre de clés](https://msdn.microsoft.com/library/azure/mt620025.aspx)|
| VaultList | [Liste de l’ensemble des coffres de clés dans un groupe de ressources](https://msdn.microsoft.com/fr-FR/library/azure/mt620027.aspx)|
| KeyCreate | [Création d’une clé](https://msdn.microsoft.com/fr-FR/library/azure/dn903634.aspx)|
| KeyGet | [Obtention des informations sur une clé](https://msdn.microsoft.com/fr-FR/library/azure/dn878080.aspx)|
| KeyImport | [Importation d’une clé dans un coffre](https://msdn.microsoft.com/fr-FR/library/azure/dn903626.aspx)|
| KeyBackup | [Sauvegarde d’une clé](https://msdn.microsoft.com/fr-FR/library/azure/dn878058.aspx).|
| KeyDelete | [Suppression d’une clé](https://msdn.microsoft.com/fr-FR/library/azure/dn903611.aspx)|
| KeyRestore | [Restauration d’une clé](https://msdn.microsoft.com/fr-FR/library/azure/dn878106.aspx)|
| KeySign | [Signature avec une clé](https://msdn.microsoft.com/fr-FR/library/azure/dn878096.aspx)|
| KeyVerify | [Vérification à l’aide d’une clé](https://msdn.microsoft.com/fr-FR/library/azure/dn878082.aspx)|
| KeyWrap | [Encapsulage d’une clé](https://msdn.microsoft.com/fr-FR/library/azure/dn878066.aspx)|
| KeyUnwrap | [Désencapsulage d’une clé](https://msdn.microsoft.com/fr-FR/library/azure/dn878079.aspx)|
| KeyEncrypt | [Chiffrement avec une clé](https://msdn.microsoft.com/fr-FR/library/azure/dn878060.aspx)|
| KeyDecrypt | [Déchiffrement avec une clé](https://msdn.microsoft.com/fr-FR/library/azure/dn878097.aspx)|
| KeyUpdate | [Mise à jour d’une clé](https://msdn.microsoft.com/fr-FR/library/azure/dn903616.aspx)|
| KeyList | [Liste des clés dans un coffre](https://msdn.microsoft.com/fr-FR/library/azure/dn903629.aspx)|
| KeyListVersions | [Liste des versions d’une clé](https://msdn.microsoft.com/fr-FR/library/azure/dn986822.aspx)|
| SecretSet | [Création d’une clé secrète](https://msdn.microsoft.com/fr-FR/library/azure/dn903618.aspx)|
| SecretGet | [Obtention d’une clé secrète](https://msdn.microsoft.com/fr-FR/library/azure/dn903633.aspx)|
| SecretUpdate | [Mise à jour d’une clé secrète](https://msdn.microsoft.com/fr-FR/library/azure/dn986818.aspx)|
| SecretDelete | [Suppression d’une clé secrète](https://msdn.microsoft.com/fr-FR/library/azure/dn903613.aspx)|
| SecretList | [Liste des clés secrètes d’un coffre](https://msdn.microsoft.com/fr-FR/library/azure/dn903614.aspx)|
| SecretListVersions | [Liste des versions d’une clé secrète](https://msdn.microsoft.com/fr-FR/library/azure/dn986824.aspx)|




## <a id="next"></a>Étapes suivantes ##

Pour en savoir plus sur un didacticiel utilisant Azure Key Vault dans une application web, consultez la page [Utilisation d’Azure Key Vault à partir d’une application web](key-vault-use-from-web-application.md).

Pour les références de programmation, consultez le [guide du développeur de coffre de clés Azure](key-vault-developers-guide.md).

Pour obtenir la liste des applets de commande Azure PowerShell 1.0 pour le coffre de clés Azure, consultez la page [Applets de commande d’Azure Key Vault](https://msdn.microsoft.com/library/azure/dn868052.aspx).
 

<!---HONumber=AcomDC_0114_2016-->