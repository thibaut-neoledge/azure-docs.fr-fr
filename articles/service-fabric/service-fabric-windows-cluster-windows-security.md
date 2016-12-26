---
title: "Sécurisation d’un cluster exécuté sur Windows à l’aide de la sécurité Windows | Microsoft Docs"
description: "Apprenez à configurer la sécurité nœud à nœud et client à nœud sur un cluster autonome exécuté sur Windows à l’aide de la sécurité Windows."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/09/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 08024aedb0889c91909f12aa1e38c91b11d3090f
ms.openlocfilehash: 142b76f053adc273e2e071f169f8f647fbd1c241


---
# <a name="secure-a-standalone-cluster-on-windows-using-windows-security"></a>Sécuriser un cluster autonome sur Windows à l’aide de la sécurité Windows
Pour éviter tout accès non autorisé à un cluster Service Fabric, vous devez le sécuriser, notamment lorsque des charges de travail de production sont exécutées dessus. Cet article explique comment configurer la sécurité nœud à nœud et client à nœud à l’aide de la sécurité Windows dans le fichier *ClusterConfig.JSON*, ce qui correspond à l’étape de configuration de la sécurité de [Créer un cluster autonome exécuté sur Windows](service-fabric-cluster-creation-for-windows-server.md). Pour plus d’informations sur la manière dont Service Fabric utilise la sécurité Windows, référez-vous à [Scénarios de sécurité du cluster](service-fabric-cluster-security.md).

> [!NOTE]
> Vous devez bien réfléchir à votre choix de sécurité pour la sécurité nœud à nœud, car aucune mise à niveau de cluster n’est possible d’un choix de sécurité vers un autre. La modification du choix de sécurité nécessite une régénération complète du cluster.
> 
> 

## <a name="configure-windows-security"></a>Configurer la sécurité Windows
L’exemple de fichier de configuration *ClusterConfig.Windows.JSON* téléchargé avec le package de cluster autonome [Microsoft.Azure.ServiceFabric.WindowsServer.<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) comporte un modèle de configuration de la sécurité Windows.  La sécurité Windows est configurée dans la section **Propriétés** :

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
                "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

| **Paramètres de configuration** | **Description** |
| --- | --- |
| ClusterCredentialType |Pour activer la sécurité Windows, définissez le paramètre **ClusterCredentialType** sur *Windows*. |
| ServerCredentialType |Pour activer la sécurité Windows pour les clients, définissez le paramètre **ServerCredentialType** sur *Windows*. Cela indique que les clients du cluster, et le cluster lui-même, sont en cours d’exécution dans un domaine Active Directory. |
| WindowsIdentities |Contient les identités client et cluster. |
| ClusterIdentity |Configure la sécurité nœud à nœud. Un nom de groupe de machines. |
| ClientIdentities |Configure la sécurité client à nœud. Tableau des comptes d’utilisateur du client. |
| Identité |L’identité du client, un utilisateur de domaine. |
| IsAdmin |La valeur true indique que l’utilisateur de domaine dispose d’un accès administrateur au client, tandis que la valeur false correspond à un accès utilisateur. |

[sécurité nœud à nœud](service-fabric-cluster-security.md#node-to-node-security) est configurée à l’aide de **ClusterIdentity**. Pour créer des relations d’approbation entre les nœuds, ceux-ci doivent se connaître mutuellement. Pour cela, vous devez créer un groupe de domaine incluant tous les nœuds du cluster. Ce nom de groupe doit être spécifié dans **ClusterIdentity**. Pour en savoir plus, consultez la rubrique traitant de la [création d’un groupe dans Active Directory](https://msdn.microsoft.com/en-us/library/aa545347(v=cs.70).aspx).

[sécurité client à nœud](service-fabric-cluster-security.md#client-to-node-security) est configurée à l’aide de **ClientIdentities**. Pour établir une approbation entre un client et le cluster, vous devez configurer le cluster de façon à ce qu’il sache quelles identités client sont fiables. Pour ce faire, deux méthodes s’offrent à vous : indiquez les utilisateurs de groupe de domaine autorisés à se connecter ou indiquez les utilisateurs de nœud de domaine autorisés à se connecter. Service Fabric prend en charge deux types de contrôle d'accès différents pour les clients qui sont connectés à un cluster Service Fabric : administrateur et utilisateur. Le contrôle d'accès permet à l'administrateur du cluster de limiter l'accès à certains types d’opérations de cluster pour différents groupes d'utilisateurs, renforçant ainsi la sécurité du cluster.  Les administrateurs ont un accès complet aux fonctions de gestion (y compris les fonctionnalités de lecture/écriture). Les utilisateurs, par défaut, ont uniquement un accès en lecture aux fonctionnalités de gestion (par exemple, aux fonctionnalités de requête) et la capacité à résoudre les applications et les services.

L’exemple de **sécurité** suivant illustre la configuration de la sécurité Windows et indique que les machines du groupe de machines *ServiceFabric\\ClusterNodes* font partie du cluster et que *CONTOSO\usera* dispose d’un accès administrateur au client :

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric\\ClusterNodes",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
        "IsAdmin": true
        }]
    }
},
```

## <a name="next-steps"></a>Étapes suivantes
Après avoir configuré la sécurité Windows dans le fichier *ClusterConfig.JSON* , reprenez le processus de création de cluster décrit dans [Créer un cluster autonome sous Windows](service-fabric-cluster-creation-for-windows-server.md).

Pour plus d’informations sur la sécurité nœud à nœud, la sécurité client à nœud et le contrôle d’accès en fonction du rôle, consultez [Scénarios relatifs à la sécurité des clusters](service-fabric-cluster-security.md).

Pour obtenir des exemples de connexion à l’aide de PowerShell ou FabricClient, consultez [Se connecter à un cluster sécurisé](service-fabric-connect-to-secure-cluster.md) .




<!--HONumber=Nov16_HO4-->


