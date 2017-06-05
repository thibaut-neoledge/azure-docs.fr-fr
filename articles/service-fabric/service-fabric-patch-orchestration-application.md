---
title: "Application d’orchestration des correctifs pour Azure Service Fabric | Documents Microsoft"
description: "Application pour automatiser l’application de correctifs système sur un cluster Service Fabric."
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/9/2017
ms.author: nachandr
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: a3554881e7db894c602e73feb385b5b361837409
ms.contentlocale: fr-fr
ms.lasthandoff: 05/18/2017


---

# <a name="application-to-patch-windows-os-in-your-service-fabric-cluster"></a>Application pour appliquer des correctifs au système d’exploitation Windows dans votre cluster Service Fabric

L’application d’orchestration des correctifs est une application Service Fabric qui vous permet d’automatiser les mises à jour correctives du système d’exploitation sur un cluster Service Fabric, dans Azure ou localement, sans temps d'arrêt.

L’application d’orchestration des correctifs inclut les fonctionnalités essentielles suivantes :

1. **Installation automatique des mises à jour du système d’exploitation** : l’application d’orchestration des correctifs veille à ce que les mises à jour soient automatiquement téléchargées et installées, et que le nœud soit redémarré le cas échéant.
    Cette opération est effectuée sur tous les nœuds de cluster sans temps d’arrêt.

1. **Application de mise à jour corrective adaptée aux clusters et intégration de l’état d’intégrité** : lors de l’implémentation des mises à jour, l’application d’orchestration des correctifs surveille l’état d'intégrité du cluster à mesure qu’elle progresse dans la mise à jour nœud après nœud ou domaine de mise à niveau après domaine de mise à niveau. 
    Si l’application vient à détecter que le processus de mise à jour corrective a pour effet de dégrader l’état d'intégrité du cluster, elle interrompt le processus pour éviter d’aggraver le problème.

1. **Prise en charge de tous les clusters Service Fabric** : l’application est suffisamment générique pour fonctionner tant dans des clusters Azure Service Fabric que dans des clusters autonomes.
    > [!NOTE]
    > Les clusters autonomes seront bientôt pris en charge.

## <a name="link-to-download-the-application-package"></a>Lien de téléchargement du package d’application

Téléchargez l’application à partir du [Lien de téléchargement](https://go.microsoft.com/fwlink/P/?linkid=849590)

## <a name="internal-details-of-the-application"></a>Détails internes de l’application

L’application d’orchestration des correctifs comprend les sous-composants suivants :

- **Service Coordinateur** : service avec état dont les responsabilités sont les suivantes :
    - coordination de la tâche Windows Update sur le cluster tout entier ;
    - stockage du résultat des opérations Windows Update accomplies.
- **Service Agent du nœud** : service sans état qui s’exécute sur tous les nœuds du cluster Service Fabric, dont les responsabilités sont les suivantes :
    - amorçage du service NT Agent du nœud ;
    - surveillance du service NT Agent du nœud.
- **Service NT Agent du nœud** : service Windows NT. Le service NT Agent du nœud s’exécute avec des privilèges supérieurs (système). À l’opposé, les services Agent du nœud et Coordinateur s’exécutent avec des privilèges inférieurs (SERVICE réseau). Le service est chargé de l’exécution sur tous les nœuds du cluster des tâches Windows Update suivantes :
    - désactivation des mises à jour automatiques Windows Update sur le nœud ;
    - téléchargement et installation des mises à jour Windows Update conformément à la stratégie définie par l’utilisateur ;
    - redémarrage de l’ordinateur après installation de Windows Update ;
    - chargement du résultat de l’exécution de Windows Update sur le service Coordinateur ;
    - génération de rapport d’intégrité en cas d’échec de l’opération une fois toutes les nouvelles tentatives effectuées.

> [!NOTE]
> L’application d’orchestration des correctifs utilise le service système de **gestion des réparations** de Service Fabric pour activer ou désactiver le nœud, et effectuer des contrôles d’intégrité. La tâche de réparation créée par l’application d’orchestration des correctifs suit la progression de l’exécution de Windows Update pour chaque nœud.

## <a name="prerequisites-for-using-the-application"></a>Conditions préalables à l’utilisation de l’application

### <a name="ensure-service-fabric-version-is-55-or-above"></a>S’assurer que la version de Service Fabric est la version 5.5 ou supérieure

L’application d’orchestration des correctifs peut être exécutée sur des clusters ayant la version du runtime Service Fabric v5.5 ou ultérieure.

### <a name="enable-repair-manager-service-if-not-running-already"></a>Activer le service de gestion des réparations (se n'est pas encore en cours d’exécution)

Pour que l’application d’orchestration des correctifs puisse fonctionner, le service système de gestion des réparations doit être activé sur le cluster.

#### <a name="service-fabric-clusters-on-azure"></a>Clusters Service Fabric sur Azure

Sur les clusters Azure au niveau de durabilité Silver, le service de gestion des réparations devrait être activé par défaut. Sur les clusters Azure au niveau de durabilité Gold, le service de gestion des réparations peut être activé ou non, selon le temps écoulé depuis la création de ces clusters. Et sur les clusters Azure au niveau de durabilité Bronze, par défaut, le service de gestion des réparations n’est pas activé. 

Vous pouvez utiliser le [modèle Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) pour activer le service de gestion des réparations sur les clusters Service Fabric nouveaux ou existants, s’il n’apparaît pas comment étant en cours d’exécution dans la section des services système de Service Fabric Explorer.

Tout d’abord, dotez-vous du modèle approprié pour le cluster que vous souhaitez déployer. Vous pouvez soit utiliser les exemples de modèles, soit créer un modèle Resource Manager personnalisé. Vous pouvez ensuite activer le service de gestion des réparations en procédant comme suit :

1. Tout d’abord, vérifiez que `apiversion` est défini sur `2017-07-01-preview` pour la ressource `Microsoft.ServiceFabric/clusters`, comme indiqué dans l’extrait de code suivant. Si ce n’est pas le cas, vous devez mettre à jour `apiVersion` en définissant la valeur`2017-07-01-preview`

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. À présent, activez le service de gestion des réparations en ajoutant la section `addonFeaturres` suivante après la section `fabricSettings`, comme indiqué ci-dessous

    ```json
    "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "RepairManager"
        ],
    ```

3. Après avoir mis à jour votre modèle de cluster avec ces modifications, appliquez-les et laissez la mise à niveau s’accomplir. Une fois celle-ci terminée, vous pouvez voir, sous la section des services système du Service Fabric Explorer, que le service système de gestion des réparations appelé `fabric:/System/RepairManagerService` s’exécute dans votre cluster. 

#### <a name="standalone-on-premise-clusters"></a>Clusters locaux autonomes

> [!NOTE]
> Les clusters autonomes seront bientôt pris en charge.

### <a name="disable-automatic-windows-update-on-all-nodes"></a>Désactivez les mises à jour automatiques Windows Update sur tous les nœuds.

L’activation des mises à jour automatiques Windows Update sur un cluster Service Fabric peut entraîner une perte de disponibilité, car plusieurs nœuds risquent de redémarrer simultanément pour achever une mise à jour.

Par défaut, l’application d’orchestration des correctifs tente de désactiver les mises à jour automatiques Windows Update sur chaque nœud du cluster.

Toutefois, si les paramètres sont gérés par une stratégie d’administrateur ou de groupe, nous vous recommandons de définir la stratégie Windows Update en optant explicitement pour l’envoi d’une notification avant le téléchargement.


### <a name="optional-enable-windows-azure-diagnostics"></a>Facultatif : activez les Diagnostics Microsoft Azure

Prochainement, les journaux de l’application d’orchestration des correctifs seront collectés en même temps que les journaux de Service Fabric. En attendant, les journaux sont collectés localement sur chaque nœud du cluster. Nous vous recommandons de configurer les Diagnostics Microsoft Azure de façon à ce que les journaux soient chargés à partir de tous les nœuds dans un emplacement central.

Les étapes d’activation des Diagnostics Microsoft Azure sont détaillées [ici](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-how-to-setup-wad).

Les journaux de l’application d’orchestration des correctifs seraient générés sur les ID de fournisseur fixes.

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

À la section « WadCfg » du modèle Azure Resource Manager, ajoutez la section suivante : 

```json
"PatchOrchestrationApplication": \[
  {
    "provider": "e39b723c-590c-4090-abb0-11e3e6616346",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
      "eventDestination": "PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "fc0028ff-bfdc-499f-80dc-ed922c52c5e9",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "24afa313-0d3b-4c7c-b485-1047fd964b60",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "05dc046c-60e9-4ef7-965e-91660adffa68",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
\]
```

> [!NOTE]
> Si votre cluster Service Fabric comprend plusieurs types de nœuds, la section ci-dessus doit être ajoutée à toutes les sections « WadCfg ».

## <a name="configuring-the-application"></a>Configuration de l’application

L’utilisateur peut définir les configurations suivantes pour modifier le comportement de l’application d’orchestration des correctifs en fonction de ses besoins.

Il peut remplacer les valeurs par défaut en passant le paramètre d’application durant la création ou la mise à jour de l’application. Il est possible de fournir les paramètres d’application en définissant `ApplicationParameter` sur l’applet de commande `Start-ServiceFabricApplicationUpgrade` ou `New-ServiceFabricApplication`

|**Paramètre**        |**Type**                          | **Détails**|
|:-|-|-|
|MaxResultsToCache    |long                              | Nombre maximal d’historiques des résultats de Windows Update à mettre en cache. <br>La valeur par défaut est 3 000 en supposant que : <br> - Les nœuds sont au nombre de 20. <br> - Les mises à jour effectuées sur un nœud sont au nombre de 5 par mois. <br> - Le nombre maximal de résultats par opération est 10. <br> - Les résultats des 3 derniers mois doivent être stockés. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy indique la stratégie que CoordinatorService doit utiliser pour installer les mises à jour Windows Update sur les nœuds du cluster Service Fabric.<br>                         Les valeurs autorisées sont les suivantes : <br><br>                                                           <b>NodeWise</b> : les mises à jour Windows Update sont installées de façon séquentielle, nœud après nœud. <br>                                                           <b>UpgradeDomainWise</b> : les mises à jour Windows Update sont installées de façon séquentielle, domaine de mise à niveau après domaine de mise à niveau (au maximum, tous les nœuds appartenant à un domaine de mise à niveau peuvent recevoir les mises à jour Windows Update).
|LogsDiskQuotaInMB   |long  <br> (Par défaut : 1024)               |Taille maximale en Mo des journaux de l’application d’orchestration des correctifs qui peuvent être conservés localement sur un nœud.
| WUQuery               | string<br>(Par défaut : « IsInstalled=0 »)                | Requête pour obtenir les mises à jour Windows Update. Pour plus d’informations, voir [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
| InstallWindowsOSOnlyUpdates | Bool <br> (Par défaut : True)                 | Cet indicateur autorise l’installation des mises à jour du système d’exploitation.            |
| WUOperationTimeOutInMinutes | Int <br>(Par défaut : 90)                   | Spécifie le délai d’expiration de toute opération Windows Update (rechercher/télécharger/installer). Si l’opération n’est pas terminée dans le délai imparti, elle est abandonnée.       |
| WURescheduleCount     | Int <br> (Par défaut : 5)                  | Cette configuration détermine le nombre maximal de fois que le service replanifie une mise à jour Windows Update en cas d’échec persistent de l’opération.          |
| WURescheduleTimeInMinutes | Int <br>(Par défaut : 30) | Cette configuration détermine la fréquence à laquelle le service replanifie une mise à jour Windows Update en cas d’échec persistent de celle-ci. |
| WUFrequency           | Chaîne de valeurs séparées par des virgules (par défaut : « Hebdomadaire, Mercredi, 7:00:00 »).     | Fréquence d’installation des mises à jour Windows Update. Le format et les valeurs possibles sont les suivants : <br>-   Mensuelle, JJ, HH:MM:SS Ex. : Mensuelle, 05, 12:22:32 <br> -   Hebdomadaire, JOUR, HH:MM:SS Ex. : Hebdomadaire, Mardi, 12:22:32  <br> -   Quotidienne, HH:MM:SS Ex. : Quotidienne, 12:22:32  <br> -  Aucune : indique que les mises à jour Windows Update ne doivent pas être effectuées.  <br><br> REMARQUE : toutes les heures sont au format UTC|
| AcceptWindowsUpdateEula | Bool <br>(Par défaut : true) | Quand cet indicateur est activé, l’application accepte le CLUF de Windows Update pour le compte du propriétaire de l’ordinateur.              |


## <a name="steps-to-deploy-the-application"></a>Étapes de déploiement de l’application

1. Accomplissez toutes les étapes requises pour préparer le cluster.
1. Déployez l’application. Cela est possible à partir de PowerShell en suivant les étapes décrites [ici](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
1. Pour configurer l’application au moment du déploiement, utilisez `ApplicationParamater` pour l’applet de commande `New-ServiceFabricApplication`.

    Pour faciliter la tâche de l’utilisateur, le script Deploy.ps1 est inclus dans notre application. Pour utiliser ce script :

    - Connectez-vous au cluster Service Fabric à l’aide de la commande `Connect-ServiceFabricCluster`.
    - Exécutez le script PowerShell Deploy.ps1 avec la valeur `ApplicationParameter` appropriée.

> [!NOTE]
> Conservez le script et le dossier d'application PatchOrchestrationApplication dans le même répertoire.

## <a name="steps-to-upgrade-the-application"></a>Étapes de mise à niveau de l’application

Pour mettre à niveau une application d’orchestration des correctifs existante à l’aide de PowerShell, voir les étapes de mise à niveau d’application décrites [ici](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

Pour modifier uniquement les paramètres d’application de l’application, définissez le paramètre `ApplicationParamater` pour l’applet de commande `Start-ServiceFabricApplicationUpgrade` sur la version existante de l’application.

## <a name="steps-to-remove-the-application"></a>Procédure de suppression de l’application

Pour supprimer l’application, voir les étapes décrites [ici](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Pour faciliter la tâche de l’utilisateur, le script Undeploy.ps1 est inclus dans notre application. Pour utiliser ce script :
    - Connectez-vous au cluster Service Fabric à l’aide de la commande ```Connect-ServiceFabricCluster```.
    - Exécutez le script PowerShell Undeploy.ps1.

> [!NOTE]
> Conservez le script et le dossier d'application PatchOrchestrationApplication dans le même répertoire.

## <a name="viewing-the-windows-update-results"></a>Affichage des résultats des mises à jour Windows Update

L’application d’orchestration des correctifs expose l’API REST pour afficher l’historique des résultats à l’utilisateur.

Il est possible d’interroger les résultats des mises à jour Windows Update en se connectant au cluster, puis en cherchant l’adresse de réplica du service Coordinateur principal et en atteignant sur l’URL à partir du navigateur.
http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults Le point de terminaison REST pour le service Coordinateur a un port dynamique. Pour vérifier l’URL exacte, référez-vous au Service Fabric Explorer.
Ex. : pour l’exemple ci-dessous, les résultats seraient disponibles à l’adresse `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`
 ![Image du point de terminaison REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png).


L’utilisateur peut également accéder à l’URL à partir de l’extérieur du cluster, en cas d’activation du proxy inverse sur le cluster.
Point de terminaison à atteindre : http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults Le proxy inverse peut être activé sur le cluster en suivant les étapes décrites [ici](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Une fois le proxy inverse configuré, tous les microservices au sein du cluster qui exposent un point de terminaison HTTP sont adressables à partir de l’extérieur du cluster.

## <a name="diagnostics--health-events"></a>Diagnostics / Événements d’intégrité

### <a name="collecting-patch-orchestration-application-logs"></a>Collecte des journaux de l’application d’orchestration des correctifs

Prochainement, les journaux de l’application d’orchestration des correctifs seront collectés en même temps que les journaux de Service Fabric.
En attendant, les journaux sont collectés de l’une des manières suivantes :

#### <a name="locally-on-each-node"></a>Localement sur chaque nœud

Les journaux sont collectés localement sur chaque nœud du cluster Service Fabric. L’emplacement où vous pouvez accéder aux journaux est \[Service Fabric\_Installation\_Drive\]:\\PatchOrchestrationApplication\\logs.

Ex. : si le Service Fabric a été installé sur le lecteur « D », le chemin d’accès est D:\\PatchOrchestrationApplication\\logs.

#### <a name="central-location"></a>Emplacement central

Si les Diagnostics Microsoft Azure ont été configurés dans le cadre des étapes préalables, les journaux de l’application d’orchestration des correctifs sont disponibles dans Stockage Azure.

### <a name="health-reports"></a>Rapports d'intégrité

L’application d’orchestration des correctifs publie également des rapports d’intégrité sur les services CoordinatorService ou NodeAgentService dans les cas suivants :

#### <a name="windows-update-operation-failed"></a>Une opération Windows Update a échoué

En cas d’échec d’une opération Windows Update sur un nœud, un rapport d’intégrité relatif au service NodeAgentService est généré.
Les détails du rapport d’intégrité contiennent le nom du nœud problématique.

Le rapport est automatiquement supprimé une fois la mise à jour corrective effectuée sur le nœud problématique.

#### <a name="node-agent-ntservice-is-down"></a>Le service NT Agent du nœud est arrêté

Si le service NodeAgentNTService est arrêté sur un nœud, un rapport d’intégrité de niveau avertissement est généré pour le service NodeAgentService.

#### <a name="repair-manager-is-not-enabled"></a>Le service de gestion des réparations n’est pas activé

Si le service de gestion des réparations est introuvable sur le cluster, un rapport d’intégrité de niveau avertissement est généré concernant pour le service CoordinatorService.

## <a name="frequently-asked-questions"></a>Forum Aux Questions :

Q : **Je vois mon cluster en état d’erreur lors de l’exécution de l’application d’orchestration des correctifs**.

R : L’application d’orchestration des correctifs désactive et/ou redémarre des nœuds durant le processus d’installation. Cela peut occasionner une dégradation temporaire de l’état d'intégrité du cluster.

Selon la stratégie définie pour l’application, une opération de mise à jour corrective peut entraîner la dégradation soit d’un nœud isolé, soit d’un domaine de mise à niveau entier.

Notez qu’à l’issue de l’installation d’une mise à jour Windows Update, les nœuds sont réactivés après redémarrage.

Ex. : dans le cas ci-dessous, le cluster a été temporairement en état d’erreur suite à une panne de 2 nœuds et à une violation de la stratégie MaxPercentageUnhealthNodes. Il s’agit d’une erreur temporaire qui dure le temps de l’opération de mise à jour corrective.

![Image de cluster défectueux](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Si le problème persiste, voir la section Aide à la résolution des problèmes.

Q : **Puis-je utiliser l’application d’orchestration des correctifs pour des clusters autonomes ?**

R : Pas encore, mais la prise en charge des clusters autonomes sera bientôt disponible.

Q : **L’application d’orchestration des correctifs est en état d’avertissement**

R : Vérifiez si un rapport d’intégrité publié concernant l’application est la cause première. L’avertissement contient généralement les détails du problème.
L’application est supposée récupérer automatiquement de cet état si le problème est temporaire.

Q : **Mon cluster est défectueux. Cependant, je dois effectuer d’urgence une mise à jour du système d’exploitation**

R : L’application d’orchestration des correctifs n’installe pas de mises à jour tant que le cluster est défectueux.
Essayez de rétablir l’intégrité de votre cluster pour débloquer le flux de travail de l’application d’orchestration des correctifs.

Q : **Pourquoi la mise à jour corrective du cluster prend-elle autant de temps ?**

R : Le temps que prend l’application d’orchestration des correctifs dépend principalement des facteurs suivants :

- Stratégie du service CoordinatorService : la stratégie par défaut de `NodeWise` a pour effet d’appliquer la mise à jour corrective de façon séquentielle, nœud après nœud. Plus particulièrement, pour les clusters plus volumineux, nous recommandons d’utiliser une stratégie `UpgradeDomainWise` pour accélérer la mise à jour corrective sur le cluster.
- Nombre de mises à jour disponibles pour téléchargement et installation :  le temps moyen de téléchargement et d’installation d’une mise à jour ne devrait pas dépasser deux heures.
- Performances de la machine virtuelle et de la bande passante réseau.

## <a name="disclaimers"></a>Clauses d’exclusion de responsabilité

- L’application d’orchestration des correctifs accepte le CLUF de Windows Update pour le compte de l’utilisateur. Il est possible de désactiver ce paramètre dans la configuration de l’application.

- L’application d’orchestration des correctifs collecte des données de télémétrie pour effectuer le suivi de l’utilisation et des performances.
    La télémétrie de l’application suit la valeur du paramètre de télémétrie du runtime de Service Fabric (activé par défaut).

## <a name="troubleshooting-help"></a>Aide à la résolution des problèmes

### <a name="node-not-coming-back-to-up-state"></a>Nœud qui ne se réactive pas

**Un nœud peut être bloqué en état désactivé** Cela peut se produire quand un nœud planifié pour une opération ne peut pas être désactivé en raison d’une vérification de sécurité en attente. Pour résoudre ce problème, assurez-vous qu’il y a suffisamment de nœuds en état d’intégrité disponibles.

**Un nœud peut être bloqué en état désactivé pour la raison suivante**

- Le nœud a été désactivé manuellement.
- Le nœud a été désactivé en raison d’un travail en cours sur l’infrastructure Azure.
- Le nœud a été désactivé temporairement par l’application d’orchestration des correctifs afin d’appliquer un correctif au nœud.

**Un nœud peut rester bloqué en état inactif pour la raison suivante**

- Le nœud a été désactivé manuellement.
- Le nœud est en cours de redémarrage (peut-être déclenché par l’application d’orchestration des correctifs).
- Le nœud est en état inactif en raison d’une machine virtuelle ou d’un ordinateur défectueux, voire de problèmes de connectivité réseau.

### <a name="updates-were-skipped-on-some-nodes"></a>Lises à jour ont été ignorés sur certains nœuds

L’application d’orchestration des correctifs tente d’installer Windows Update conformément à une stratégie de replanification. Le service tente de récupérer le nœud et ignore la mise à jour, conformément à la stratégie d’application.

Dans ce cas, un rapport d’intégrité de niveau avertissement est généré concernant le service Agent du nœud.
Le résultat pour Windows Update contient également un motif d’échec possible.

### <a name="health-of-the-cluster-goes-to-error-while-update-was-getting-installed"></a>Erreur d’intégrité du cluster pendant l’installation d’une mise à jour

Si une mise à jour Windows Update dégrade l’intégrité d’une application ou d’un cluster sur un nœud ou un domaine de mise à niveau particulier, l’application d’orchestration des correctifs ne poursuit pas les opérations Windows Update suivantes tant que le cluster n’a pas retrouvé son intégrité.

Un administrateur doit intervenir et comprendre pourquoi une opération Windows Update dégrade l’intégrité de l’application ou du cluster.

