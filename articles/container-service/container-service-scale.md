---
title: "Mise à l’échelle de votre cluster ACS avec l’interface CLI Azure | Microsoft Docs"
description: "Mise à l’échelle de votre cluster Azure Container Service à l’aide de l’interface de ligne de commande Azure."
services: container-service
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, conteneurs, micro-services, Mesos, Azure
ms.assetid: 4a567474-f9a2-4172-bf86-7522aa4d4d80
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9e8df2e68b1b7018d76da89ba9ab332b6ea216fb


---
# <a name="scale-an-azure-container-service"></a>Mise à l’échelle d’Azure Container Service
Vous pouvez faire évoluer le nombre de nœuds d’Azure Container Service (ACS) à l’aide de l’outil d’interface de ligne de commande Azure. Lorsque vous utilisez l’interface de ligne de commande Azure pour mettre à l’échelle, l’outil renvoie un nouveau fichier de configuration représentant la modification appliquée au conteneur.

## <a name="about-the-command"></a>À propos de la commande
L’interface de ligne de commande Azure doit être en mode Azure Resource Manager pour vous permettre d’interagir avec les conteneurs Azure. Vous pouvez basculer en mode Resource Manager en appelant `azure config mode arm`. La commande `acs` dispose d’une commande enfant nommée `scale` qui effectue toutes les opérations de mise à l’échelle pour un service de conteneur. Vous pouvez obtenir de l’aide sur les différents paramètres utilisés dans la commande de mise à l’échelle en exécutant `azure acs scale --help`, qui génère un résultat semblable à ceci :

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>Utilisation de la commande de mise à l’échelle
Pour faire évoluer un service de conteneur, vous devez tout d’abord connaître le **groupe de ressources** et le **nom Azure Container Service (ACS)**, et également spécifier le nouveau nombre d’agents. En utilisant un montant inférieur ou supérieur, vous pouvez faire descendre ou monter en puissance, respectivement.

Vous voudrez peut-être connaître le nombre actuel d’agents pour un service de conteneur avant de mettre à l’échelle. Utilisez la commande `azure acs show <resource group> <ACS name>` pour renvoyer la configuration d’ACS. Notez le résultat <mark>Nombre</mark>.

#### <a name="see-current-count"></a>Affichage du nombre actuel
```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>Mise à l’échelle vers le nouveau nombre
Cela est sans doute déjà évident, mais vous pouvez faire évoluer le service de conteneur en appelant `azure acs scale` et en fournissant le **groupe de ressources**, le **nom ACS** et le **nombre d’agents**. Lorsque vous mettez à l’échelle un service de conteneur, l’interface de ligne de commande Azure renvoie une chaîne JSON représentant la nouvelle configuration du service de conteneur, avec notamment le nouveau nombre d’agents.

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>Étapes suivantes
* [Déployer un cluster](container-service-deployment.md)




<!--HONumber=Nov16_HO3-->


