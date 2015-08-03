<properties
   pageTitle="Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure"
   description="Décrit les problèmes courants du déploiement de ressources dans Azure et montre comment utiliser le portail Azure, l'interface de ligne de commande Azure pour Mac, Linux, Windows (interface de ligne de commande Azure) et PowerShell pour examiner les déploiements et de détecter les problèmes."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure"
   ms.date="04/25/2015"
   ms.author="rasquill"/>

# Résolution des problèmes liés aux déploiements de groupes de ressources dans Azure

Il est beaucoup plus facile d'éviter des erreurs de déploiement en vérifiant à l'avance certains points, mais les déploiements échouent parfois pour plusieurs raisons. Ce document décrit les outils et opérations permettant d'éviter des erreurs simples, de télécharger des fichiers de modèle et d'examiner des journaux de déploiement. Il aborde également les principaux domaines à prendre en compte lors de la recherche de défaillances dans des journaux de déploiement.

## Outils utiles pour interagir avec Azure
Le module AzureResourceManager inclut des applets de commande qui, lorsque vous travaillez avec vos ressources Azure à partir de la ligne de commande, collectent des outils qui vous permettent d'effectuer vos tâches. Les modèles de groupe de ressources Azure sont des documents JSON, et l'API de gestion des ressources Azure accepte et retourne des données en JSON. Par conséquent, les outils d'analyse JSON sont l'un des premiers éléments que vous utiliserez pour parcourir des informations relatives à vos ressources, ainsi que pour concevoir des modèles et fichiers de paramètres de modèle ou interagir avec ces modèles et fichiers.

### Outils Mac, Linux et Windows
Si vous utilisez l'interface de ligne de commande Azure pour Mac, Linux et Windows, vous êtes probablement familiarisé avec les outils de téléchargement standard tels que **[curl](http://curl.haxx.se/)** et **[wget](https://www.gnu.org/software/wget/)**, ou **[Resty](https://github.com/beders/Resty)**, et les utilitaires JSON tels que **[jq](http://stedolan.github.io/jq/download/)**, **[jsawk](https://github.com/micha/jsawk)**, et les bibliothèques de langue qui gèrent bien JSON. (Beaucoup de ces outils disposent également de ports pour Windows, tels que [wget](http://gnuwin32.sourceforge.net/packages/wget.htm) ; en fait, il existe plusieurs manières d'obtenir des outils Linux et d'autres outils logiciels open source fonctionnant également sur Windows.)

Cette rubrique inclut certaines commandes d'interface de ligne de commande Azure que vous pouvez utiliser avec **jq** pour obtenir précisément les informations souhaitées avec plus d'efficacité. Vous devez choisir l'ensemble d'outils que vous maîtrisez pour vous comprendre l'utilisation des ressources Azure.

### Windows PowerShell

Windows PowerShell possède plusieurs commandes de base permettant d'effectuer les mêmes procédures.

- Utilisez l'applet de commande **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** pour télécharger des fichiers tels que des modèles de groupe de ressources ou des fichiers JSON de paramètres.
- Utilisez l'applet de commande **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** pour convertir une chaîne JSON en un objet personnalisé ([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx)) qui possède une propriété pour chaque champ de la chaîne JSON.

## Prévention des erreurs dans l'interface de ligne de commande Azure pour Mac, Linux et Windows

L'interface de ligne de commande Azure comprend plusieurs commandes permettant d'éviter des erreurs et de détecter la cause du problème le cas échéant.

- **azure location list**. Cette commande permet d'obtenir les emplacements qui prennent en charge chaque type de ressource, tels que le fournisseur pour les machines virtuelles. Avant d'entrer un emplacement pour une ressource, utilisez cette commande pour vérifier que l'emplacement est compatible avec le type de ressource concerné.

    Étant donné que la liste des emplacements peut être longue, et qu'il existe de nombreux fournisseurs, vous pouvez utiliser des outils pour examiner les fournisseurs et les emplacements avant d'utiliser un emplacement qui n'est pas encore disponible. Le script suivant utilise **jq** pour détecter les emplacements où le fournisseur de ressources pour Azure Virtual Machines est disponible. ()

        azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
        {
          "name": "Microsoft.Compute/virtualMachines",
          "location": "East US,West US,West Europe,East Asia,Southeast Asia,North Europe"
        }

- **azure group template validate <resource group>**. Cette commande permet de valider vos modèles ainsi que les paramètres de modèles avant de les utiliser. Entrez un modèle personnalisé ou de la galerie et les valeurs de paramètres du modèle que vous prévoyez d'utiliser.

    L'exemple suivant montre comment valider un modèle et tous les paramètres requis. L'interface de ligne de commande Azure vous invite à entrer les valeurs de paramètre qui sont requises.

        azure group template validate \
        > --template-uri "https://contoso.com/templates/azuredeploy.json" \
        > resource-group
        info:    Executing command group template validate
        info:    Supply values for the following parameters
        adminUserName: UserName
        adminPassword: PassWord
        + Initializing template configurations and parameters
        + Validating the template
        info:    group template validate command OK

## Obtention d'informations permettant de résoudre des problèmes de déploiement avec l'interface de ligne de commande Azure

- **azure group log show <resource group>** : cette commande récupère les entrées du journal pour chaque déploiement du groupe de ressources. Si une erreur survient, commencez par examiner les journaux des déploiements.

        info:    Executing command group log show
        info:    Getting group logs
        data:    ----------
        data:    EventId:              <guid>
        data:    Authorization:
        data:                          action: Microsoft.Network/networkInterfaces/write
        data:                          role:   Subscription Admin
        data:                          scope:  /subscriptions/xxxxxxxxxxx/resourcegroups/templates/
                                               providers/Microsoft.Network/
                                               networkInterfaces/myNic
        data:    ResourceUri:          /subscriptions/xxxxxxxxxxxx/resourcegroups/templates/providers/
                                       Microsoft.Network/networkInterfaces/myNic
        data:    SubscriptionId:       <guid>
        data:    EventTimestamp (UTC): Wed Apr 22 2015 05:53:31 GMT+0000 (UTC)
        data:    OperationName:        Microsoft.Network/networkInterfaces/write
        data:    OperationId:          <guid>
        data:    Status:               Started
        data:    SubStatus:
        data:    Caller:
        data:    CorrelationId:        <guid>
        data:    Description:
        data:    HttpRequest:          clientRequestId: <guid>
                                       clientIpAddress: 000.000.00.000
                                       method:          PUT

        data:    Level:                Informational
        data:    ResourceGroup:        templates
        data:    ResourceProvider:     Microsoft.Network
        data:    EventSource:          Microsoft Resources
        data:    Properties:           requestbody: {"location":"West US","properties
                                       ":{"ipConfigurations":[{"
                                       name":"ipconfig1","properties":{"
                                       privateIPAllocationMethod

                                       ":"Dynamic","publicIPAddress":{"id":"/
                                       subscriptions/
                                       <guid>/
                                       resourceGroups/
                                       templates/providers/Microsoft.Network/
                                       publicIPAddresses/
                                       myPublicIP"},"subnet":{"idThe AzureResourceManager module includes cmdlets that ":"/subscriptions/
                                       <guid>/resourceGroups/templates/
                                       providers/
                                       Microsoft.Network/virtualNetworks/myVNET/subnets/
                                       Subnet-1
                                       "}}}]}}

Utilisez l’option **--last-deployment** pour récupérer uniquement le journal du dernier déploiement. Le script suivant utilise l’option **--json** et **jq** pour rechercher les échecs de déploiement dans le journal.

        azure group log show templates --json | jq '.[] | select(.status.value == "Failed")'

        {
          "claims": {
            "aud": "https://management.core.windows.net/",
            "iss": "https://sts.windows.net/<guid>/",
            "iat": "1429678549",
            "nbf": "1429678549",
            "exp": "1429682449",
            "ver": "1.0",
            "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
            "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "ahmet@contoso.onmicrosoft.com",
            "puid": "XXXXXXXXXXXXXX",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "ahmet",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "",
            "name": "Friendly Name",
            "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
            "groups": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "ahmet@contoso.onmicrosoft.com",
            "appid": "<guid>",
            "appidacr": "0",
            "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
            "http://schemas.microsoft.com/claims/authnclassreference": "1"
          },
          "properties": {},
          "authorization": {
            "action": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "role": "Subscription Admin",
            "scope": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1"
          },
          "eventChannels": "Operation",
          "eventDataId": "<guid>",
          "correlationId": "<guid>",
          "eventName": {
            "value": "EndRequest",
            "localizedValue": "End request"
          },
          "eventSource": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "level": "Error",
          "resourceGroupName": "templates",
          "resourceProviderName": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "resourceUri": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1",
          "operationId": "<guid>",
          "operationName": {
            "value": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "localizedValue": "Update deployment"
          },
          "status": {
            "value": "Failed",
            "localizedValue": "Failed"
          },
          "subStatus": {},
          "eventTimestamp": "2015-04-22T05:53:40.8150293Z",
          "submissionTimestamp": "2015-04-22T05:54:00.6728843Z",10037FFE8E80BB65
          "subscriptionId": "<guid>"
        }


- **Options --verbose et -vv** : utilisez l'option **--verbose** pour définir le mode détaillé, afin d'afficher les étapes par lesquelles passent les opérations sur `stdout`. Pour obtenir l'historique complet des demandes incluant les étapes que **-verbose** active, utilisez l'option **-vv**. Souvent, ces messages fournissent des indices essentiels sur les causes d'erreurs.

- **Vos informations d'identification Azure n'ont pas été configurées ou ont expiré** : pour actualiser les informations d'identification dans votre session d'interface de ligne de commande Azure, tapez `azure login`. Pour obtenir de l'aide sur les erreurs d'authentification, assurez-vous que vous avez [correctement configuré l'interface de ligne de commande Azure](../xplat-cli-connect.md).

## Prévention des erreurs dans Windows PowerShell

Le module AzureResourceManager inclut des applets de commande vous permettant d'éviter les erreurs.


- **Get-AzureLocation** : cette applet de commande obtient les emplacements qui prennent en charge chaque type de ressource. Avant d'entrer un emplacement pour une ressource, utilisez cette applet de commande pour vérifier que l'emplacement est compatible avec le type de ressource concerné.


- **Test-AzureResourceGroupTemplate** : testez votre modèle et paramètre de modèle avant de les utiliser. Entrez un modèle personnalisé ou de la galerie et les valeurs de paramètres du modèle que vous prévoyez d'utiliser. Cette applet de commande teste si le modèle est intrinsèquement cohérent et si la valeur que vous avez définie correspond au modèle.

## Obtention d'informations permettant de résoudre des problèmes de déploiement dans Windows PowerShell

- **Get-AzureResourceGroupLog** : cette applet de commande récupère les entrées du journal pour chaque déploiement du groupe de ressources. Si une erreur survient, commencez par examiner les journaux des déploiements.

- **Verbose et Debug** : les applets de commande du module AzureResourceManager appellent les API REST qui effectuent la tâche effective. Pour voir les messages renvoyés par les API, définissez la variable $DebugPreference sur Continue et ajoutez le paramètre courant Verbose dans vos commandes. Souvent, ces messages fournissent des indices essentiels sur les causes d'erreurs.

- **Vos informations d'identification Azure n'ont pas été configurées ou ont expiré** : pour actualiser les informations d'identification dans votre session Windows PowerShell, utilisez l'applet de commande Add-AzureAccount. Les informations d'identification que contient un fichier de paramètres de publication ne sont pas suffisantes pour les applets de commande du module AzureResourceManager.

## Problèmes liés à l'authentification, aux abonnements, aux rôles et aux quotas

Il est possible qu'un ou plusieurs problèmes empêchent un déploiement impliquant l'authentification, l'autorisation et Azure Active Directory. Quelle que soit la façon dont vous gérez vos groupes de ressources Azure, l'identité que vous utilisez pour vous connecter à votre compte doit être soit des objets soit des principaux de service Azure Active Directory, également appelés comptes professionnels ou scolaires, ou ID professionnels.

Mais avec Azure Active Directory, vous ou votre administrateur pouvez contrôler très précisément quelles identités peuvent accéder à quelles ressources. Si vos déploiements échouent, examinez les demandes elles-mêmes pour rechercher des signes d'authentification ou des problèmes d'autorisation, et consultez également les journaux de déploiement de votre groupe de ressources. Il est possible que bien que vous possédiez des autorisations pour certaines ressources, vous n'en ayez pas pour d'autres ressources. À l'aide de l'interface de ligne de commande Azure, vous pouvez examiner les locataires et les utilisateurs Azure Active Directory à l'aide des commandes `azure ad`. (Pour obtenir la liste complète des commandes de l'interface de ligne de commande Azure, consultez [Utilisation de l'interface de ligne de commande Azure pour Mac, Linux et Windows avec le gestionnaire des ressources Azure](azure-cli-arm-commands.md).)

Il est possible que vous rencontriez également des problèmes lorsqu'un déploiement atteint un quota par défaut, ce qui peut être par groupe de ressources, par abonnement, par compte, et pour d'autres étendues également. Vérifiez, à votre convenance, que vous disposez des ressources disponibles pour effectuer correctement le déploiement. Pour obtenir des informations complètes sur les quotas, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).

Pour examiner les quotas de cœurs de votre abonnement, vous devez utiliser la commande `azure vm list-usage` dans l’interface de ligne de commande Azure et l’applet de commande `Get-AzureVMUsage` dans PowerShell. Ce qui suit présente la commande dans l’interface de ligne de commande Azure et indique que le quota de cœurs d’un compte d’évaluation gratuit est de 4 :

    azure vm list-usage
    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4    
    info:    vm list-usage command OK

Si vous essayez de déployer un modèle créant plus de 4 cœurs dans l’Ouest des États-Unis dans l’abonnement ci-dessus, vous obtenez une erreur de déploiement qui peut ressembler à ceci (dans le portail ou en examinant les journaux de déploiement) :

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

Dans ce cas, vous devez accéder au portail et signaler un problème de support afin d’augmenter votre quota pour la région vers laquelle vous souhaitez procéder au déploiement.

> [AZURE.NOTE]N’oubliez pas que pour les groupes de ressources, le quota est défini pour chaque région, pas pour tout l’abonnement. Si vous devez déployer 30 cœurs dans l’Ouest des États-Unis, vous devez demander 30 cœurs de gestion de ressources dans l’Ouest des États-Unis. Si vous devez déployer 30 cœurs dans l’une des régions auxquelles vous avez accès, vous devez demander 30 cœurs de gestion de ressources dans toutes les régions. <!-- --> Concrètement, vous pouvez par exemple vérifier les régions pour lesquelles vous devez demander le quota approprié à l’aide de la commande suivante, qui est dirigée vers **jq** pour l’analyse json. <!-- --> azure provider show Microsoft.Compute --json | jq '.resourceTypes | select(.name == "virtualMachines") | { name,apiVersions, locations}' { "name": "virtualMachines", "apiVersions": [ "2015-05-01-preview", "2014-12-01-preview" ], "locations": [ "East US", "West US", "West Europe", "East Asia", "Southeast Asia" ] }
     

## Interface de ligne de commande Azure et problèmes liés au mode PowerShell

Il peut arriver que des ressources Azure déployées avec l'API de gestion des services ou le portail classique ne soient pas visibles à l'aide de l'API de gestion des ressources ou du portail Azure. Il est important de gérer les ressources en utilisant la même API de gestion ou le même portail que ceux utilisés pour les créer. Si une ressource a disparu, vérifiez si elle est disponible en utilisant l'autre API de gestion ou l'autre portail.

## Problèmes d'inscription du fournisseur de ressources Azure

Les ressources sont gérées par les fournisseurs de ressources, et un compte ou un abonnement peut être activé pour utiliser un fournisseur particulier. Si vous êtes configuré pour utiliser un fournisseur, il doit également être enregistré pour être utilisé. La plupart des fournisseurs sont enregistrés automatiquement par le portail Azure ou l'interface de ligne de commande que vous utilisez, mais pas tous.

Pour voir si le fournisseur est enregistré pour être utilisé à l'aide de l'interface de ligne de commande Azure, utilisez la commande `azure provider list` (l'exemple de sortie ci-dessous est tronqué).

        azure provider list
        info:    Executing command provider list
        + Getting ARM registered providers
        data:    Namespace                        Registered
        data:    -------------------------------  -------------
        data:    Microsoft.Compute                Registered
        data:    Microsoft.Network                Registered  
        data:    Microsoft.Storage                Registered
        data:    microsoft.visualstudio           Registered
        data:    Microsoft.Authorization          Registered
        data:    Microsoft.Automation             NotRegistered
        data:    Microsoft.Backup                 NotRegistered
        data:    Microsoft.BizTalkServices        NotRegistered
        data:    Microsoft.Features               Registered
        data:    Microsoft.Search                 NotRegistered
        data:    Microsoft.ServiceBus             NotRegistered
        data:    Microsoft.Sql                    Registered
        info:    provider list command OK

Là encore, si vous souhaitez plus d’informations sur les fournisseurs, y compris sur leur disponibilité régionale, tapez `azure provider list --json`. Les éléments suivants sélectionnent uniquement le premier élément de la liste à afficher :

        azure provider list --json | jq '.[0]'
        {
          "resourceTypes": [
            {
              "apiVersions": [
                "2014-02-14"
              ],
              "locations": [
                "North Central US",
                "East US",
                "West US",
                "North Europe",
                "West Europe",
                "East Asia"
              ],
              "properties": {},
              "name": "service"
            }
          ],
          "id": "/subscriptions/<guid>/providers/Microsoft.ApiManagement",
          "namespace": "Microsoft.ApiManagement",
          "registrationState": "Registered"
        }


Si un fournisseur doit être enregistré, utilisez la commande `azure provider register <namespace>`, où la valeur *namespace* provient de la liste précédente.

## Présentation des raisons d'un déploiement réussi pour des modèles personnalisés

Si vous utilisez des modèles que vous avez créés, il est important de comprendre que le système de gestion des ressources Azure signale la réussite d'un déploiement en cas de retour réussi de tous les fournisseurs par le déploiement. Cela signifie que tous vos éléments de modèle ont été déployés pour votre utilisation.

Toutefois, cela ne signifie pas nécessairement que votre groupe de ressources est **actif et prêt pour vos utilisateurs**. Par exemple, la plupart des déploiements demandent le téléchargement de certaines mises à niveau, l'attente pour d'autres, des ressources sans modèle, ou encore l'installation de scripts complexes ou d'une autre activité exécutable qu'Azure ne connaît pas, car il ne s'agit pas d'une activité suivie par un fournisseur. Dans ce cas, cela peut prendre un moment avant que vos ressources soient prêtes pour une utilisation concrète. Par conséquent, il est normal que l'état du déploiement indique la réussite un certain temps avant que votre déploiement puisse être utilisé.

Vous pouvez toutefois empêcher Azure de signaler la réussite d'un déploiement en créant un script personnalisé pour votre modèle personnalisé (à l'aide de l'extension [CustomScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/), par exemple) qui sait comment détecter la disponibilité pour tout le système par une surveillance de l'ensemble du déploiement et qui se termine correctement uniquement lorsque les utilisateurs peuvent interagir avec la totalité du déploiement. Si vous voulez vous assurer que votre extension est la dernière à s'exécuter, utilisez la propriété **dependsOn** dans votre modèle. Un exemple est illustré [ici](https://msdn.microsoft.com/library/azure/dn790564.aspx).

## Fusion de modèles

Vous devrez peut-être parfois fusionner deux modèles, ou lancer un modèle enfant à partir d'un parent. Pour ce faire, vous pouvez utiliser une ressource de déploiement dans le modèle principal pour déployer un modèle enfant.


    {
            "name": "instance01",
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2015-01-01",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "https://mystore.blob.windows.net/azurermtemplates/my-child-template.json",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "storageAccountName": { "value": "[variables('stgAcctName1')]" },
                    "adminUsername": { "value": "[parameters('adminUsername')]" },
                    "adminPassword": { "value": "[parameters('adminPassword')]" }
                }
            }
    }


## Croisement de groupes de ressources

Souvent, vous voudrez utiliser une ressource se trouvant hors du groupe de ressources actuel dans lequel un modèle est déployé. Le scénario le plus courant pour ce comportement consiste à utiliser un compte de stockage ou un réseau virtuel se trouvant dans un autre groupe de ressources. Cela est souvent nécessaire pour que la suppression du groupe de ressources contenant les machines virtuelles n'entraîne pas la suppression des objets blob vhd ou d'un réseau virtuel utilisé par plusieurs groupes de ressources. L'exemple suivant montre comment une ressource d'un groupe de ressources externe peut être facilement utilisée :


    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]

    }

## Étapes suivantes

Pour maîtriser la création de modèles, lisez le document [Création de modèles de gestionnaire des ressources Azure](../resource-group-authoring-templates.md), et parcourez le [référentiel AzureRMTemplates](https://github.com/azurermtemplates/azurermtemplates) pour obtenir des exemples pouvant pas être déployés. Un exemple de la propriété **dependsOn** est l'[équilibrage de charge avec le modèle de règle NAT de trafic entrant](https://github.com/azurermtemplates/azurermtemplates/blob/master/101-create-internal-loadbalancer/azuredeploy.json).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/
 

<!---HONumber=July15_HO4-->