---
title: "Sécuriser un cluster exécuté sous Windows avec la sécurité Windows | Microsoft Docs"
description: "Apprenez à configurer la sécurité de nœud à nœud et de client à nœud sur un cluster autonome exécuté sous Windows avec la sécurité Windows."
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
ms.date: 01/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 6294a6fac8748f70c807df76feb425cb627bf4c3
ms.lasthandoff: 03/15/2017


---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Sécuriser un cluster autonome sous Windows avec la sécurité Windows
Pour empêcher tout accès non autorisé à un cluster Service Fabric, vous devez sécuriser le cluster. La sécurité est particulièrement importante lorsque le cluster exécute des charges de travail de production. Cet article explique comment configurer la sécurité de nœud à nœud et de client à nœud avec la sécurité Windows dans le fichier *ClusterConfig.JSON*.  Le processus correspond à l’étape de configuration de la sécurité sur la page [Créer un cluster autonome s’exécutant sous Windows](service-fabric-cluster-creation-for-windows-server.md). Pour plus d’informations sur la manière dont Service Fabric utilise la sécurité Windows, référez-vous à [Scénarios de sécurité du cluster](service-fabric-cluster-security.md).

> [!NOTE]
> Vous devez bien réfléchir à votre choix de sécurité de nœud à nœud, car aucune mise à niveau de cluster n’est possible d’un choix de sécurité vers un autre. Pour modifier la sécurité sélectionnée, il faut recréer la totalité du cluster.
>
>

## <a name="configure-windows-security"></a>Configurer la sécurité Windows  
L’exemple de fichier de configuration *ClusterConfig.Windows.JSON*, téléchargé avec le package de cluster autonome [Microsoft.Azure.ServiceFabric.WindowsServer<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690), comporte un modèle de configuration de la sécurité Windows. La sécurité Windows est configurée dans la section **Propriétés** :

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

| **Paramètre de configuration** | **Description** |
| --- | --- |
| ClusterCredentialType |Définissez sur *Windows* pour activer la sécurité Windows. |
| ServerCredentialType |Définissez sur *Windows* pour activer la sécurité Windows pour les clients.<br /><br />Cela indique que les clients du cluster, et le cluster lui-même, sont en cours d’exécution dans un domaine Active Directory. |  
| WindowsIdentities |Contient les identités client et cluster. |  
| ClusterIdentity |Utilisez un nom de groupe de machines, domain\machinegroup, pour configurer la sécurité de nœud à nœud. |  
| ClientIdentities |Configure la sécurité client à nœud. Tableau des comptes d’utilisateur du client. |  
| Identité |Ajoutez l’utilisateur du domaine, domain\username, pour l’identité du client. |  
| IsAdmin |Définissez sur true pour indiquer que l’utilisateur de domaine dispose d’un accès administrateur au client, ou sur false pour un accès utilisateur. |  

La [sécurité de nœud à nœud](service-fabric-cluster-security.md#node-to-node-security) est configurée avec **ClusterIdentity**. Les nœuds doivent se connaître mutuellement pour entretenir des relations d’approbation. Pour établir cette reconnaissance, créez un groupe de domaine incluant tous les nœuds du cluster. Ce nom de groupe doit être spécifié dans **ClusterIdentity**. Pour en savoir plus, consultez la rubrique traitant de la [création d’un groupe dans Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).  

La [sécurité de client à nœud](service-fabric-cluster-security.md#client-to-node-security) est configurée avec **ClientIdentities**. Pour établir une approbation entre un client et le cluster, vous devez configurer le cluster de façon à ce qu’il connaisse les identités du client auxquelles il peut faire confiance. Vous pouvez établir cette approbation de deux manières différentes :

- Spécifiez les utilisateurs du groupe de domaines qui peuvent se connecter.
- Spécifiez les utilisateurs du nœud de domaine qui peuvent se connecter.

Service Fabric prend en charge deux types de contrôle d'accès différents pour les clients qui sont connectés à un cluster Service Fabric : administrateur et utilisateur. Le contrôle d’accès permet à l’administrateur du cluster de limiter l’accès à certains types d’opérations de cluster pour différents groupes d’utilisateurs, ce qui renforce la sécurité du cluster.  Les administrateurs ont un accès complet aux fonctions de gestion (y compris les fonctionnalités de lecture/écriture). Les utilisateurs, par défaut, ont uniquement un accès en lecture aux fonctionnalités de gestion (par exemple, aux fonctionnalités de requête) et la capacité à résoudre les applications et les services.  

L’exemple de **sécurité** suivant illustre la configuration de la sécurité Windows, indique que les ordinateurs de *ServiceFabric/clusterA.contoso.com* font partie du cluster et spécifie que *CONTOSO\usera* dispose d’un accès administrateur au client :

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> Service Fabric ne doit pas être déployé sur un contrôleur de domaine. Veillez à ce que ClusterConfig.json n’inclue pas l’adresse IP du contrôleur de domaine en cas d’utilisation d’un groupe de machines ou d’un compte gMSA (group Managed Service Account).
>
>

## <a name="next-steps"></a>Étapes suivantes
Après avoir configuré la sécurité Windows dans le fichier *ClusterConfig.JSON* , reprenez le processus de création de cluster décrit dans [Créer un cluster autonome sous Windows](service-fabric-cluster-creation-for-windows-server.md).

Pour plus d’informations sur la sécurité de nœud à nœud, la sécurité de client à nœud et le contrôle d’accès en fonction du rôle, consultez la page [Scénarios relatifs à la sécurité des clusters](service-fabric-cluster-security.md).

Pour voir des exemples de connexion avec PowerShell ou FabricClient, consultez la page [Se connecter à un cluster sécurisé](service-fabric-connect-to-secure-cluster.md).

