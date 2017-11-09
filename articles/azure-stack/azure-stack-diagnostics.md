---
title: Diagnostics dans Azure Stack
description: Comment collecter des fichiers journaux de diagnostics dans Azure Stack
services: azure-stack
author: adshar
manager: byronr
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 10/2/2017
ms.author: adshar
ms.openlocfilehash: 9b1fbbf63ddd8bac2c1a76bbcd5daca69e2513f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-diagnostics-tools"></a>Outils de diagnostics Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*
 
Azure Stack est une grande collection de composants qui fonctionnent ensemble et interagissent. Tous ces composants génèrent leurs propres journaux. Cela peut compliquer le diagnostic des problèmes, notamment lorsque les erreurs proviennent de plusieurs composants Azure Stack en interaction. 

Nos outils de diagnostic aident à garantir la simplicité d’utilisation et l’efficacité du mécanisme de collection de journaux. Le diagramme suivant illustre le fonctionnement des outils de collecte de journaux Azure Stack :

![Outils de collecte de journaux](media/azure-stack-diagnostics/image01.png)
 
 
## <a name="trace-collector"></a>Collecteur de traces
 
Le collecteur de traces est activé par défaut. Il s’exécute de manière continue en arrière-plan, collecte tous les journaux de suivi d’événements pour Windows (ETW) à partir des services de composants sur Azure Stack, et les stocke sur un partage local commun. 

Voici quelques choses importantes à savoir concernant le collecteur de traces :
 
* Le collecteur de traces s’exécute en permanence avec les limites de taille par défaut. La taille maximale par défaut autorisée pour chaque fichier (200 Mo) n’est **pas** une taille limite. Une vérification de la taille se produit régulièrement (actuellement, toutes les 2 minutes) et, si la taille du fichier actuel est supérieure ou égale à 200 Mo, il est enregistré et un fichier est généré. Il existe également une limite de 8 Go (configurable) concernant la taille totale des fichiers générés par session d’événements. Une fois cette limite atteinte, les fichiers les plus anciens sont supprimés à mesure que de nouveaux sont créés.
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
 
Voici quelques exemples de types de journaux collectés :
*   **Journaux de déploiement Azure Stack**
*   **Journaux des événements Windows**
*   **Journaux Panther**

   Pour résoudre les problèmes de création de machine virtuelle, procédez comme suit :
*   **Journaux de cluster**
*   **Journaux de diagnostics de stockage**
*   **Journaux ETW**

Ces fichiers sont recueillis par le collecteur de traces et stockés dans un partage à partir duquel `Get-AzureStackLog` les récupère.
 
**Pour exécuter Get-AzureStackLog sur un Kit de développement Azure Stack (ASDK)**
1.  Connectez-vous en tant que AzureStack\AzureStackAdmin sur l’hôte.
2.  Ouvrez une fenêtre PowerShell en tant qu’administrateur.
3.  Exécutez `Get-AzureStackLog`.  

    **Exemples**

    - Collecter tous les journaux pour tous les rôles :

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs`

    - Collecter les journaux à partir des rôles VirtualMachines et BareMetal :

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal`

    - Collecter les journaux à partir des rôles VirtualMachines et BareMetal, avec un filtrage de date pour les fichiers journaux pour les huit dernières heures :

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)`

    - Collecter les journaux à partir des rôles VirtualMachines et BareMetal, avec un filtrage de date pour les fichiers journaux des 2 à 8 dernières heures :

      `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)`

**Pour exécuter Get-AzureStackLog sur un système intégré Azure Stack :**

Pour exécuter l’outil de collection de journaux sur un système intégré, vous devez avoir accès au point de terminaison privilégié (PEP). Voici un exemple de script que vous pouvez exécuter à l’aide du point de terminaison privilégié pour collecter des journaux sur un système intégré :

```
$ip = "<IP OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputPath "\\<HLH MACHINE ADDREESS>\c$\logs" -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- Lorsque vous collectez les journaux à partir du point de terminaison privilégié, spécifiez le paramètre `OutputPath` afin qu’il corresponde à un emplacement sur la machine HLH. Vérifiez également que l’emplacement est chiffré.
- Les paramètres `OutputSharePath` et `OutputShareCredential` sont facultatifs et sont utilisés lorsque vous chargez des journaux vers un dossier partagé externe. Utilisez ces paramètres *en plus* de `OutputPath`. Si le paramètre `OutputPath` n’est pas spécifié, l’outil de collection de journal utilise le lecteur système de la machine virtuelle PEP pour le stockage. Cela peut entraîner l’échec du script, car l’espace disque est limité.
- Comme indiqué dans l’exemple précédent, les paramètres `FromDate` et `ToDate` peuvent être utilisés pour collecter des journaux pour une période donnée. Cela peut être pratique pour les scénarios, tels que la collection de journaux après l’application d’un package de mise à jour sur un système intégré.

**Considérations relatives aux paramètres du kit ASDK et des systèmes intégrés :**

- Si vous ne spécifiez pas les paramètres `FromDate` et `ToDate`, par défaut les journaux sont collectés pour les quatre dernières heures.
- Vous pouvez utiliser le paramètre `TimeOutInMinutes` pour définir le délai d’expiration pour la collection de journaux. Il est défini sur 150 (2,5 heures) par défaut.

- Actuellement, vous pouvez utiliser le paramètre `FilterByRole` pour filtrer la collecte de journaux en fonction des rôles suivants :

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


Autres informations à connaître :

* L’exécution de cette commande peut prendre un certain temps, en fonction des données du ou des rôles collectées par les journaux. Les facteurs qui entrent en compte sont la durée spécifiée pour la collecte de journaux et le nombre de nœuds de l’environnement Azure Stack.
* Une fois la collecte de journaux terminée, vérifiez le dossier créé dans le paramètre `-OutputPath` spécifié dans la commande.
* Les journaux de chaque rôle se trouvent à l’intérieur de fichiers zip individuels. Selon leur taille, les journaux d’un rôle collectés peuvent être séparés en plusieurs fichiers zip. Pour ce type de rôle, si vous souhaitez disposer de tous les fichiers journaux décompressés dans un dossier unique, utilisez un outil qui peut effectuer cette opération en blocs (7zip, par exemple). Sélectionnez tous les fichiers compressés du rôle, puis sélectionnez **Extract Here**. Cette opération permet de décompresser tous les fichiers journaux de ce rôle, au sein d’un dossier fusionné unique.
* Un fichier appelé `Get-AzureStackLog_Output.log` est également créé dans le dossier qui contient les fichiers journaux compressés. Ce fichier est un journal de la sortie de la commande, qui peut être utilisé pour résoudre des problèmes lors de la collection de journaux.
* Pour examiner un échec spécifique, vous aurez peut-être besoin des journaux de plusieurs composants.
    -   Les journaux système et des événements pour toutes les machines virtuelles de l’infrastructure sont collectés dans le rôle *VirtualMachines*.
    -   Les journaux système et des événements pour tous les hôtes sont collectés dans le rôle *BareMetal*.
    -   Les journaux des événements de cluster de basculement et Hyper-V sont collectés dans le rôle *Storage*.
    -   Les journaux ACS sont collectés dans les rôles *Storage* et *ACS*.

> [!NOTE]
> Nous appliquons des limites de taille et d’âge aux journaux collectés, car il est essentiel de garantir une utilisation efficace de votre espace de stockage afin de s’assurer que vous ne vous retrouverez pas submergé de journaux. Toutefois, lorsque vous diagnostiquez un problème, vous avez parfois besoin de journaux qui n’existent plus, à cause de ces limites. Par conséquent, nous vous **recommandons vivement** de décharger vos journaux vers un espace de stockage externe (un compte de stockage dans Azure, un dispositif de stockage local supplémentaire, etc.) toutes les 8 à 12 heures, et de les conserver pendant 1 à 3 mois, en fonction de vos besoins. Vérifiez également que l’emplacement de stockage est chiffré.

## <a name="next-steps"></a>Étapes suivantes
[Dépannage de Microsoft Azure Stack](azure-stack-troubleshooting.md)
