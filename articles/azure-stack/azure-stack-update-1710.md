---
title: "Mise à jour 1710 d’Azure Stack (Build 20171020.1) | Microsoft Docs"
description: "Apprenez-en davantage sur le contenu de la mise à jour 1710 pour les systèmes intégrés, les problèmes connus, et l’emplacement à partir duquel la télécharger."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: twooley
ms.openlocfilehash: 6b54bb616accb926af9364865bf4108fe0aa3bc8
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="azure-stack-1710-update-build-201710201"></a>Mise à jour 1710 d’Azure Stack (Build 20171020.1)

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit les améliorations et correctifs contenus dans cette mise à jour, les problèmes connus dans cette publication, et l’emplacement de téléchargement de la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour, et problèmes propres à la build (après l’installation).

> [!IMPORTANT]
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement d’Azure Stack.

## <a name="improvements-and-fixes"></a>Améliorations et correctifs

Cette mise à jour inclut les améliorations de la qualité et les correctifs suivants pour Azure Stack.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Améliorations et correctifs de Windows Server 2016

- Mises à jour pour Windows Server 2016 : 10 octobre 2017, KB4041691 (version du système d’exploitation 14393.1770). Pour plus d’informations, voir [https://support.microsoft.com/help/4041691](https://support.microsoft.com/help/4041691).

### <a name="additional-quality-improvements-and-fixes"></a>Correctifs et améliorations de la qualité supplémentaires

- Ajout d’applets de commande PowerShell de point de terminaison privilégié pour aider à dépanner et à mettre à jour le serveur NTP (Network Time Protocol).
- Ajout de la prise en charge des modules de points de terminaison d’administration suffisante de point de terminaison privilégié et de la liste verte d’applets de commande. 
- Correction des erreurs de langue locale dans le point de terminaison privilégié.
- Ajout de la possibilité d’opérer une rotation des informations d’identification de la passerelle .
- Suppression du compte d’administrateur local CBLocalAdmin. 
- Correction du contenu du modèle d’alerte d’interrogation pour s’assurer que les alertes d’interrogation fonctionnent correctement après une mise à jour.
- Correction du fournisseur de ressources de structure pour gérer les délais d’attente durant des opérations FRU (Field Replaceable Unit). 
- Ajout de la possibilité pour les développeurs cloud d’utiliser des profils d’API Azure Resource Manager sur Azure Stack.
- Désactivation du service Windows Update sur la machine virtuelle de déploiement. 
- Suppression des actions d’activation/désactivation de nœud à partir de l’interface utilisateur.
- Corrections de divers autres problèmes de performances et de stabilité. 
 
## <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour

Cette section énumère des problèmes connus que vous pouvez rencontrer lors de l’installation de la mise à jour 1710.

| Symptôme  | Cause :  | Résolution : |
|---------|---------|---------|
|Lorsque vous effectuez une mise à jour, une erreur similaire à la suivante peut se produire lors de l’étape « Storage Hosts Restart Storage Node » du plan d’action de mise à jour.<br><br>**{"name":"Restart Storage Hosts","description":"Restart Storage Hosts.","errorMessage":"Type ’Restart’ of Role ’BareMetal’ raised an exception:\n\nThe computer HostName-05 is skipped. Fail to retrieve its LastBootUpTime via the WMI service with the following error message: The RPC server is unavailable. (Exception from HRESULT: 0x800706BA).\nat Restart-Host** | Ce problème est dû à la présence d’un pilote potentiellement défectueux dans certaines configurations. | 1. Connectez-vous à l’interface web du contrôleur de gestion de la carte de base (BMC), puis redémarrez l’ordinateur hôte identifié dans le message d’erreur.<br><br>2. Reprenez la mise à jour. |
| Lorsque vous effectuez une mise à jour, le processus de mise à jour semble stagner et cesser de progresser après l’étape « Step: Running step 2.4 - Install update » du plan d’action de mise à jour.<br><br>Cette étape est suivie par une série de processus de copie de fichiers .nupkg vers des partages de fichiers de l’infrastructure interne. Par exemple :<br><br>**Copying 1 files from content\PerfCollector\VirtualMachines to \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\PerfCounterConfiguration**  | Ce problème est dû au fait que des fichiers journaux saturent les disques d’une machine virtuelle d’infrastructure, et à un dysfonctionnement du serveur de fichiers avec montée en puissance parallèle (SOFS) de Windows Server qui sera corrigé dans une prochaine mise à jour. | Pour obtenir de l’aide, contactez le Support technique et Service clientèle (CSS) Microsoft. | 
| Lorsque vous effectuez une mise à jour, une erreur similaire à la suivante peut se produire lors de l’étape « Step: Running step 2.13.2 - Update *VM_Name* » du plan d’action de mise à jour (le nom de la machine virtuelle peut varier).<br><br>**ActionPlanInstanceWarning ece/MachineName: WarningMessage:Task: Invocation of interface ’LiveUpdate’ of role ’Cloud\Fabric\WAS’ failed:<br>Type ’LiveUpdate’ of Role ’WAS’ raised an exception:<br>ERROR during storage initialization: An error occurred while trying to make an API call to Microsoft Storage service: {"Message":"A timeout occurred while communicating with Service Fabric. Exception Type: TimeoutException. Exception message: Operation timed out."}**  | Ce problème est dû à un délai d’attente d’E/S dans Windows Server, qui sera résolu dans une mise à jour ultérieure. | Pour obtenir une assistance, contactez le Support technique et Service clientèle Microsoft.
| Lorsque vous effectuez une mise à jour, une erreur similaire à la suivante peut se produire lors de l’étape « Step 21 Restart SQL Server VMs. »<br><br>**Type 'LiveUpdateRestart' of Role 'VirtualMachines' raised an exception:<br>VerboseMessage:[VirtualMachines:LiveUpdateRestart] Querying for VM MachineName-Sql01. - 10/13/2017 5:11:50 PM VerboseMessage:[VirtualMachines:LiveUpdateRestart] VM is marked as HighlyAvailable. - 10/13/2017 5:11:50 PM VerboseMessage:[VirtualMachines:LiveUpdateRestart] at MS.Internal.ServerClusters.ExceptionHelp.Build at MS.Internal.ServerClusters.ClusterResource.BeginTakeOffline(Boolean force) at Microsoft.FailoverClusters.PowerShell.StopClusterResourceCommand.BeginTimedOperation() at Microsoft.FailoverClusters.PowerShell.TimedCmdlet.WrappedProcessRecord() at Microsoft.FailoverClusters.PowerShell.FCCmdlet.ProcessRecord() - 10/13/2017 5:11:50 PM WarningMessage:Task: Invocation of interface 'LiveUpdateRestart' of role 'Cloud\Fabric\VirtualMachines' failed:** | Ce problème peut se produire si la machine virtuelle n’a pas pu redémarrer. | Pour obtenir une assistance, contactez le Support technique et Service clientèle Microsoft.
| Lorsque vous effectuez une mise à jour, une erreur similaire à la suivante peut se produire :<br><br>**2017-10-22T01:37:37.5369944Z Type 'Shutdown' of Role 'SQL' raised an exception:An error occurred pausing node 's45r1004-Sql01'.at Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\Applications\EnterpriseCloudEngineApplicationType_App1\EnterpriseCloudEngineServicePkg.Code.1.0.597.18\CloudDeployment\Roles\SQL\SQL.psm1: line 542at Shutdown, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\Applications\EnterpriseCloudEngineApplicationType_App1\EnterpriseCloudEngineServicePkg.Code.1.0.597.18\CloudDeployment\Classes\SQL\SQL.psm1: line 50at <ScriptBlock>, <No file>: line 18at <ScriptBlock>, <No file>: line 16** | Ce problème peut se produire si la machine virtuelle ne peut pas être mise en état suspendu afin de drainer les rôles. | Pour obtenir une assistance, contactez le Support technique et Service clientèle Microsoft.
| Lorsque vous effectuez une mise à jour, l’une des erreurs suivantes peut se produire :<br><br>**Type 'Validate' of Role 'ADFS' raised an exception: Validation for ADFS/Graph role failed with error: Error checking ADFS probe endpoint *endpoint_URI*: Exception calling "GetResponse" with "0" argument(s): "The remote server returned an error: (503) Server Unavailable." at Invoke-ADFSGraphValidation**<br><br>**Type 'Validate' of Role 'ADFS' raised an exception: Validation for ADFS/Graph role failed with error: Error fetching ADFS properties: Could not connect to net.tcp://localhost:1500/policy. The connection attempt lasted for a time span of 00:00:02.0498923. TCP error code 10061: No connection could be made because the target machine actively refused it 127.0.0.1:1500. at Invoke-ADFSGraphValidation** | Le plan d’action de mise à jour ne peut pas valider l’intégrité des services de fédération Active Directory (AD FS). | Pour obtenir de l’assistance, contactez le Support technique et Service clientèle Microsoft.

## <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

Cette section énumère des problèmes connus après l’installation de la build 20171020.1.

### <a name="portal"></a>Portail

- Il se peut qu’il ne soit pas possible d’afficher les ressources de calcul ou de stockage sur le portail d’administration. Cela indique qu’une erreur s’est produite pendant l’installation de la mise à jour, et que celle-ci n’a pas été correctement signalée comme réussie. Si ce problème se produit, contactez le Support technique et Service clientèle Microsoft pour obtenir une assistance.
- Il se peut qu’un tableau de bord vide s’affiche sur le portail. Pour récupérer le tableau de bord, sélectionnez l’icône d’engrenage dans l’angle supérieur droit du portail, puis choisissez **Restaurer les paramètres par défaut**.
- Les utilisateurs ont la possibilité de parcourir entièrement la Place de marché, et peuvent voir des éléments administratifs, tels que des plans et des offres, qui ne sont pas fonctionnels pour eux.
- Le bouton **Déplacer** est désactivé lorsque vous affichez les propriétés d’un groupe de ressources. Il s’agit du comportement attendu. Le déplacement de groupes de ressources entre des abonnements n’est pas pris en charge actuellement.
- Vous n’avez pas la possibilité d’afficher les autorisations de votre abonnement sur les portails Azure Stack. Pour contourner ce problème, vous pouvez vérifier les autorisations à l’aide de PowerShell.
-  Pour tout workflow dans lequel vous sélectionnez un abonnement, un groupe de ressources ou un emplacement dans une liste déroulante, vous pouvez rencontrer un ou plusieurs des problèmes suivants :

   - Vous pouvez voir une ligne vide en haut de la liste. Vous devriez toujours être en mesure de sélectionner un élément comme prévu.
   - Si la liste déroulante des éléments est courte, il se peut que vous ne puissiez afficher aucun nom d’élément.
   - Si vous avez plusieurs abonnements utilisateur, la liste déroulante des groupes de ressources peut être vide. 

   Pour contourner les deux derniers problèmes, vous pouvez taper le nom de l’abonnement ou du groupe de ressources (si vous les connaissez), ou utiliser PowerShell à la place.
  
### <a name="backup"></a>Sauvegarde

- N’activez pas la **sauvegarde de l’infrastructure** sur le panneau dédié à celle-ci.

### <a name="health-and-monitoring"></a>Intégrité et surveillance

- Si vous redémarrez une instance de rôle d’infrastructure, il se peut que vous receviez un message indiquant que le redémarrage a échoué. Pour tant, en réalité, le redémarrage a réussi.

### <a name="services"></a>Services

**Place de marché**
- Lorsque vous tentez d’ajouter des éléments à la Place de marché d’Azure Stack à l’aide de l’option **Ajouter à partir d’Azure**, certains éléments disponibles en téléchargement peuvent ne pas être visibles.
- Il n’existe aucune expérience de Place de marché pour créer des groupes de machines virtuelles identiques. Vous pouvez créer un groupe identique à l’aide d’un modèle.
- Un client doit inscrire le fournisseur de ressources de stockage avant de créer sa première fonction Azure dans l’abonnement.
- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur. 

**SQL/MySQL**
- Il faut parfois attendre une heure pour qu’ils puissent créer des bases de données avec une nouvelle référence SQL ou MySQL. 
- La création d’éléments directement sur des serveurs d’hébergement SQL et MySQL qui n’est pas effectuée par le fournisseur de ressources n’est pas prise en charge, et peut aboutir à un état non compatible.

**Calcul**
- Les utilisateurs ont la possibilité de créer une machine virtuelle avec stockage géoredondant. Cette configuration fait échouer la création.
- Vous pouvez configurer un groupe à haute disponibilité de machines virtuelles uniquement avec un domaine d’erreur et un domaine de mise à jour, tous deux de valeur égale à un.
 
**Réseau**
- Vous ne pouvez pas créer un équilibreur de charge avec une adresse IP publique à l’aide du portail. Pour résoudre ce problème, vous pouvez utiliser PowerShell pour créer l’équilibreur de charge.
- Lorsque vous créez un équilibreur de charge réseau, vous devez créer une règle de traduction d’adresses réseau (NAT). À défaut, une erreur s’affiche lorsque vous tentez d’ajouter une règle NAT après avoir créé l’équilibreur de charge.
 
### <a name="field-replaceable-unit-fru-procedures"></a>Procédures de FRU (Field Replaceable Unit)

- Lors de l’exécution de la mise à jour, les images hors connexion ne sont pas corrigées. Si vous avez besoin de remplacer un nœud d’unité d’échelle, vérifiez avec votre fournisseur de matériel OEM que le nœud remplacé dispose du dernier niveau de correctif.

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1710 [ici](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la gestion des mises à jour dans Azure Stack, voir [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).
- Pour plus d’informations sur la façon d’appliquer les mises à jour, voir [Effectuer des mises à jour dans Azure Stack](azure-stack-apply-updates.md).