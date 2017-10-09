---
title: Diagnostics dans Azure Stack | Microsoft Docs
description: Comment collecter des fichiers journaux de diagnostics dans Azure Stack
services: azure-stack
documentationcenter: 
author: adshar
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: adshar
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: d8f39d921222a3642e3da6e288b4ca11ae1ccaeb
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="azure-stack-diagnostics-tools"></a>Outils de diagnostics Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*
 
Azure Stack est une grande collection de composants qui fonctionnent ensemble et interagissent. Tous ces composants génèrent leurs propres journaux uniques, ce qui signifie que diagnostiquer les problèmes peut rapidement devenir une tâche difficile, en particulier pour les erreurs provenant de plusieurs composants Azure Stack qui interagissent. 

Nos outils de diagnostics aident à garantir la simplicité d’utilisation et l’efficacité du mécanisme de collecte de journaux. Le diagramme suivant illustre le fonctionnement des outils de collecte de journaux Azure Stack :

![Outils de collecte de journaux](media/azure-stack-diagnostics/image01.png)
 
 
## <a name="trace-collector"></a>Collecteur de traces
 
Le collecteur de traces est activé par défaut. Il s’exécute de manière continue en arrière-plan, collecte tous les journaux de suivi d’événements pour Windows (ETW) à partir des services de composants sur Azure Stack, et les stocke sur un partage local commun. 

Voici quelques choses importantes à savoir concernant le collecteur de traces :
 
* Le collecteur de traces s’exécute en permanence avec les limites de taille par défaut. La taille maximale par défaut autorisée pour chaque fichier (200 Mo) n’est **pas** une taille limite. Une vérification de la taille se produit régulièrement (actuellement toutes les 10 minutes), et si la taille du fichier actuel est supérieure ou égale à 200 Mo, il est enregistré et un nouveau fichier est généré. Il existe également une limite de 8 Go (configurable) concernant la taille totale des fichiers générés par session d’événements. Une fois cette limite atteinte, les fichiers les plus anciens sont supprimés à mesure que de nouveaux sont créés.
* Il existe une limite d’âge de journal de cinq jours. Cette limite est également configurable. 
* Chaque composant définit les propriétés de configuration de trace par le biais d’un fichier JSON. Les fichiers JSON sont stockés dans `C:\TraceCollector\Configuration`. Si nécessaire, vous pouvez modifier ces fichiers pour changer les limites d’âge et de taille des journaux collectés. Les changements apportés à ces fichiers nécessitent un redémarrage du service *Collecteur de traces Microsoft Azure Stack* pour que les modifications prennent effet.
* L’exemple suivant est un fichier JSON de configuration de trace pour les opérations de FabricRingServices à partir de la machine virtuelle XRP : 

```
{
    "LogFile": 
    {
        "SessionName": "FabricRingServicesOperationsLogSession",
        "FileName": "\\\\SU1FileServer\\SU1_ManagementLibrary_1\\Diagnostics\\FabricRingServices\\Operations\\AzureStack.Common.Infrastructure.Operations.etl",
        "RollTimeStamp": "00:00:00",
        "MaxDaysOfFiles": "5",
        "MaxSizeInMB": "200",
        "TotalSizeInMB": "5120"
    },
    "EventSources":
    [
        {"Name": "Microsoft-AzureStack-Common-Infrastructure-ResourceManager" },
        {"Name": "Microsoft-OperationManager-EventSource" },
        {"Name": "Microsoft-Operation-EventSource" }
    ]
}
```

* **MaxDaysOfFiles**

    Ce paramètre contrôle la durée de conservation des fichiers. Les fichiers journaux plus anciens sont supprimés.
* **MaxSizeInMB**

    Ce paramètre contrôle le seuil de taille pour un seul fichier. Si la taille est atteinte, un nouveau fichier .etl est créé.
* **TotalSizeInMB**

    Ce paramètre contrôle la taille totale des fichiers .etl générés à partir d’une session d’événements. Si la taille totale des fichiers est supérieure à la valeur de ce paramètre, les fichiers plus anciens sont supprimés.
  
## <a name="log-collection-tool"></a>Outil de collecte de journaux
 
Vous pouvez utiliser la commande PowerShell `Get-AzureStackLog` pour collecter des journaux à partir de tous les composants dans un environnement Azure Stack. Elle les enregistre dans des fichiers zip à un emplacement défini par l’utilisateur. Si notre équipe de support technique a besoin de vos journaux pour aider à résoudre un problème, elle peut vous demander d’exécuter cet outil.

> [!CAUTION]
> Ces fichiers journaux peuvent contenir des informations d’identification personnelle. Pensez-y avant de publier des fichiers journaux publiquement.
 
Nous collectons actuellement les types de journaux suivants :
*   **Journaux de déploiement Azure Stack**
*   **Journaux des événements Windows**
*   **Journaux Panther**

     Pour résoudre les problèmes de création de machine virtuelle.
*   **Journaux de cluster**
*   **Journaux de diagnostics de stockage**
*   **Journaux ETW**

    Ces journaux sont recueillis par le collecteur de traces et stockés dans un partage à partir duquel `Get-AzureStackLog` les récupère.
 
Pour identifier tous les journaux qui sont recueillis à partir de tous les composants, consultez les balises `<Logs>` dans le fichier de configuration client situé dans `C:\EceStore\<Guid>\<GuidWithMaxFileSize>`.
 
**Pour exécuter Get-AzureStackLog**
1.  Connectez-vous en tant que AzureStack\AzureStackAdmin sur l’hôte.
2.  Ouvrez une fenêtre PowerShell en tant qu’administrateur.
3.  Exécutez `Get-AzureStackLog`.  

    **Exemples**

    - Collecter tous les journaux pour tous les rôles :

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs`

    - Collecter les journaux à partir des rôles VirtualMachines et BareMetal :

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal`

    - Collecter les journaux à partir des rôles VirtualMachines et BareMetal, avec un filtrage de date pour les fichiers journaux pour les huit dernières heures :

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date)`

Si vous ne spécifiez pas les paramètres `FromDate` et `ToDate`, par défaut les journaux sont collectés pour les quatre dernières heures.

Actuellement, vous pouvez utiliser le paramètre `FilterByRole` pour filtrer la collecte de journaux en fonction des rôles suivants :

|   |   |   |
| - | - | - |
| `ACSMigrationService`     | `ACSMonitoringService`   | `ACSSettingsService` |
| `ACS`                     | `ACSFabric`              | `ACSFrontEnd`        |
| `ACSTableMaster`          | `ACSTableServer`         | `ACSWac`             |
| `ADFS`                    | `ASAppGateway`           | `BareMetal`          |
| `BRP`                     | `CA`                     | `CPI`                |
| `CRP`                     | `DeploymentMachine`      | `DHCP`               |
|`Domain`                   | `ECE`                    | `ECESeedRing`        |        
| `FabricRing`              | `FabricRingServices`     | `FRP`                |
|` Gateway`                 | `HealthMonitoring`       | `HRP`                |               
| `IBC`                     | `InfraServiceController` | `KeyVaultAdminResourceProvider`|
| `KeyVaultControlPlane`    | `KeyVaultDataPlane`      | `NC`                 |            
| `NonPrivilegedAppGateway` | `NRP`                    | `SeedRing`           |
| `SeedRingServices`        | `SLB`                    | `SQL`                |     
| `SRP`                     | `Storage`                | `StorageController`  |
| `URP`                     | `UsageBridge`            | `VirtualMachines`    |  
| `WAS`                     | `WASPUBLIC`              | `WDS`                |


Points à noter :

* Cette commande de collecte de journaux prend un certain temps, en fonction des journaux de rôles qui sont recueillis. Les facteurs qui entrent en compte sont la durée spécifiée pour la collecte de journaux et le nombre de nœuds dans l’environnement Azure Stack.
* Une fois la collecte de journaux terminée, vérifiez le dossier créé dans le paramètre `-OutputPath` spécifié dans la commande.
* Un fichier nommé `Get-AzureStackLog_Output.log` est créé dans le dossier contenant les fichiers zip. Il inclut la sortie de commande, que vous pouvez utiliser pour résoudre les erreurs de collecte de journaux.
* Les journaux de chaque rôle se trouvent à l’intérieur d’un fichier zip individuel. 
* Pour examiner un échec spécifique, vous aurez peut-être besoin des journaux de plusieurs composants.
    -   Les journaux système et des événements pour toutes les machines virtuelles de l’infrastructure sont collectés dans le rôle *VirtualMachines*.
    -   Les journaux système et des événements pour tous les hôtes sont collectés dans le rôle *BareMetal*.
    -   Les journaux des événements de cluster de basculement et Hyper-V sont collectés dans le rôle *Storage*.
    -   Les journaux ACS sont collectés dans les rôles *Storage* et *ACS*.
* Pour plus d’informations, vous pouvez consulter le fichier de configuration client. Examinez les balises `<Logs>` pour les différents rôles.

> [!NOTE]
> Nous appliquons des limites de taille et d’âge aux journaux recueillis, car il est essentiel de garantir une utilisation efficace de votre espace de stockage afin de s’assurer que vous ne vous retrouverez pas submergé de journaux. Ceci dit, quand vous diagnostiquerez un problème vous pourriez avoir besoin de journaux qui n’existent plus en raison de ces limites. Par conséquent, nous vous **recommandons vivement** de décharger vos journaux vers un espace de stockage externe (un compte de stockage dans Azure, un dispositif de stockage local supplémentaire, etc.) toutes les 8 à 12 heures, et de les conserver pendant 1 à 3 en fonction de vos besoins.

### <a name="multi-node-considerations"></a>Considérations relatives à plusieurs nœuds
Si vous souhaitez collecter les journaux dans un environnement à plusieurs nœuds, notez les différences suivantes :
* La fonction `get-date` n’est pas autorisée dans des environnements à plusieurs nœuds. Par conséquent, vous devez spécifier explicitement une date. Par exemple :

   `-FromDate "Friday, August 18, 2017 6:34:48 AM" -ToDate "Friday, August 18, 2017 7:35:25 AM"`
* Spécifiez un chemin d’accès UNC pour la sortie vers un dossier partagé sur l’hôte de cycle de vie du matériel, ou tout autre dossier partagé auquel vous avez accès. Par exemple :

   `Get-AzureStackLog -OutputSharePath \\10.193.128.250\logs -OutputShareCredential $sharecred`

   Le paramètre `-OutputShareCredential` vous invite à entrer les informations d’identification pour accéder au dossier partagé.

## <a name="next-steps"></a>Étapes suivantes
[Dépannage de Microsoft Azure Stack](azure-stack-troubleshooting.md)

