---
title: Gestion des notifications de maintenance pour les machines virtuelles Windows dans Azure | Microsoft Docs
description: "Affichez les notifications de maintenance pour les machines virtuelles Windows s’exécutant dans Azure et démarrez la maintenance en libre-service."
services: virtual-machines-windows
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: zivr
ms.openlocfilehash: 80c029866f3d28712be823692f3bf4ce6e210405
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2017
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Gestion de notifications de maintenance planifiées pour les machines virtuelles Windows

Azure exécute régulièrement des mises à jour afin d’améliorer la fiabilité, les performances et la sécurité de l’infrastructure hôte des machines virtuelles. Les mises à jour sont des modifications telles que la mise à jour corrective de l’environnement d’hébergement ou la mise à niveau et la désactivation de matériel. Une majorité de ces mises à jour ont lieu sans affecter les machines virtuelles hébergées. Cependant, il existe des cas où les mises à jour ont un impact :

- Lorsque la maintenance ne nécessite pas de redémarrage, Azure utilise une migration sur place pour mettre en pause la machine virtuelle pendant la mise à jour de l’hôte.

- Si la maintenance nécessite un redémarrage, une notification vous dira pour quand est prévue la maintenance. Dans ces cas, vous disposez d’une période pour commencer la maintenance vous-même, au moment qui vous convient.


La maintenance planifiée nécessitant un redémarrage s’effectue par vagues. Chaque vague a une portée différente (régions).

- Une vague commence par une notification aux clients. Par défaut, la notification est envoyée aux propriétaire et copropriétaires de l’abonnement. Vous pouvez ajouter plusieurs destinataires et options de messagerie, telles que les e-mails, les SMS et les Webhooks, aux notifications.  
- Peu après la notification, une fenêtre de libre-service est définie. Pendant cet intervalle, vous pouvez rechercher quelles machines virtuelles sont incluses dans cette vague et démarrer la maintenance à l’aide d’un redéploiement proactif. 
- Après la fenêtre de libre-service, une fenêtre de maintenance planifiée commence. À ce stade, Azure planifie et applique la maintenance requise à votre machine virtuelle. 

L’objectif de ces deux fenêtres est de vous donner suffisamment de temps pour commencer la maintenance et redémarrer votre machine virtuelle tout en sachant à quel moment Azure démarrera automatiquement la maintenance.


Vous pouvez utiliser le portail Azure, PowerShell, les API REST et l’interface de ligne de commande pour demander les fenêtres de maintenance pour vos machines virtuelles et démarrer la maintenance en libre-service.

 > [!NOTE]
 > Si vous essayez de démarrer la maintenance mais que cette dernière échoue, Azure marque votre machine virtuelle en tant que **ignorée** et ne la redémarre pas au cours de la fenêtre de maintenance planifiée. À la place, vous êtes contacté ultérieurement avec une nouvelle planification. 


[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Vérifier l’état de maintenance à l’aide de PowerShell

Vous pouvez également utiliser Azure Powershell pour voir quand les machines virtuelles sont planifiées pour la maintenance. Les informations de maintenance planifiée sont disponibles à partir de l’applet de commande [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) lorsque vous utilisez le paramètre `-status`.
 
Les informations de maintenance sont retournées uniquement en cas de maintenance planifiée. S’il n’existe aucune maintenance planifiée qui affecte la machine virtuelle, l’applet de commande ne retourne pas d’informations de maintenance. 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
```

Les propriétés suivantes sont retournées sous MaintenanceRedeployStatus : 
| Valeur | Description   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indique si vous pouvez démarrer la maintenance sur la machine virtuelle maintenant ||
| PreMaintenanceWindowStartTime         | Début de la fenêtre de maintenance en libre-service lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| PreMaintenanceWindowEndTime           | Fin de la fenêtre de maintenance en libre-service lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| MaintenanceWindowStartTime            | Début de la fenêtre de maintenance planifiée lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| MaintenanceWindowEndTime              | Fin de la fenêtre de maintenance planifiée lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| LastOperationResultCode               | Résultat de la dernière tentative de lancement de la maintenance sur la machine virtuelle ||



Vous pouvez également obtenir l’état de maintenance pour toutes les machines virtuelles dans un groupe de ressources à l’aide de [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) sans spécifier de machine virtuelle.
 
```powershell
Get-AzureRmVM -ResourceGroupName rgName -Status
```

La fonction PowerShell suivante accepte votre ID d’abonnement et imprime une liste de machines virtuelles qui sont planifiées pour la maintenance.

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Démarrer la maintenance sur votre machine virtuelle à l’aide de PowerShell

À l’aide des informations de la fonction de la section précédente, la commande suivante démarre la maintenance sur une machine virtuelle si **IsCustomerInitiatedMaintenanceAllowed** est défini sur true.

```powershell
Restart-AzureRmVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Déploiements Classic

Si vous avez encore des machines virtuelles qui ont été déployées à l’aide du modèle de déploiement Classic, vous pouvez utiliser PowerShell pour interroger ces machines virtuelles et démarrer la maintenance.

Pour obtenir l’état de la maintenance d’une machine virtuelle, tapez :

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Pour démarrer la maintenance sur votre machine virtuelle Classic, tapez :

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>Forum Aux Questions


**Q : Pourquoi devez-vous redémarrer mes machines virtuelles maintenant ?**

**R :** Alors que la majorité des mises à jour et des mises à niveau vers la plateforme Azure n’affectent pas la disponibilité des machines virtuelles, il existe des cas où nous ne pouvons pas éviter le redémarrage des machines virtuelles hébergées dans Azure. Nous avons accumulé plusieurs modifications qui nous obligent à redémarrer nos serveurs, ce qui entraînera le redémarrage des machines virtuelles.

**Q : Si je suis vos recommandations concernant la haute disponibilité en utilisant un groupe à haute disponibilité, suis-je sécurisé ?**

**R :** Les machines virtuelles déployées dans un groupe à haute disponibilité ou les groupes de machines virtuelles identiques incluent la notion de Domaine de mise à jour (UD). Lors de la réalisation de la maintenance, Azure respecte la contrainte de domaine de mise à jour et ne redémarre pas les machines virtuelles à partir de différents domaines de mise à jour (dans le même groupe à haute disponibilité).  Azure attend également au moins 30 minutes avant de passer au groupe de machines virtuelles suivant. 

Pour plus d’informations sur la haute disponibilité, consultez l’article Gestion de la disponibilité des machines virtuelles Windows dans Azure ou Gestion de la disponibilité des machines virtuelles Linux dans Azure.

**Q : J’ai un jeu de récupération d’urgence dans une autre région. Suis-je sécurisé ?**

**R :**Chaque région Azure est associée à une autre région au sein de la même région géographie (par exemple, États-Unis, Europe ou Asie). Les mises à jour Azure planifiées sont déployées vers les régions jumelées une par une pour limiter les interruptions de service et les risques de panne de l’application. Pendant la maintenance planifiée, Azure peut planifier une fenêtre similaire pour que les utilisateurs démarrent la maintenance. Toutefois, la fenêtre de maintenance planifiée est différente entre les régions jumelées.  

Pour plus d’informations sur les régions Azure, reportez-vous à l’article Régions et disponibilité des machines virtuelles dans Azure.  Vous pouvez afficher la liste des paires de régions ici.

**Q : Comment être averti d’une maintenance planifiée ?**

**R :** Une vague d’opérations de maintenance planifiées commence par une planification sur une ou plusieurs régions Azure. Peu après, une notification par e-mail est envoyée aux propriétaires d’abonnement (un e-mail par abonnement). Il est possible de configurer des canaux et des destinataires supplémentaires pour cette notification à l’aide de la fonctionnalité Alertes de journal d’activité. Dans le cas où vous déployez une machine virtuelle dans une région où la planification de la maintenance est déjà effectuée, vous ne recevez pas la notification. Au lieu de cela, vous devez vérifier l’état de maintenance de la machine virtuelle.

**Q : Je ne vois aucune indication de maintenance planifiée dans le portail, dans PowerShell ou dans l’interface de ligne de commande (CLI). Quel est le problème ?**

**R :** Les informations relatives à la maintenance planifiée ne sont disponibles pendant une vague d’opérations de maintenance planifiées que pour les machines virtuelles qu’elle affectera. En d’autres termes, si vous ne voyez pas de données, il est possible que la vague d’opérations de maintenance soit déjà terminée (ou qu’elle n’ait pas démarré), ou bien que votre machine virtuelle soit déjà hébergée sur un serveur mis à jour.

**Q : Dois-je démarrer la maintenance sur ma machine virtuelle ?**

**R :** En général, les charges de travail déployées dans un service cloud, un groupe à haute disponibilité ou un groupe de machines virtuelles identiques résistent à la maintenance planifiée.  Lors de la maintenance planifiée, un seul domaine de mise à jour est affecté à un moment donné. Avertissement : L’ordre des domaines de mise à jour impactés n’est pas forcément séquentiel.

Vous pouvez souhaiter démarrer vous-même la maintenance dans les cas suivants :
- Votre application s’exécute sur une seule machine virtuelle et vous devez appliquer toutes les opérations de maintenance pendant les heures creuses.
- Vous devez coordonner l’heure de la maintenance dans le cadre de votre contrat SLA.
- Vous devez disposer de plus de 30 minutes entre chaque redémarrage de machine virtuelle, même au sein d’un groupe à haute disponibilité.
- Vous souhaitez arrêter toute l’application (plusieurs niveaux, plusieurs domaines de mise à jour) pour effectuer plus rapidement la maintenance.

**Q : Est-il possible de savoir exactement quand ma machine virtuelle sera affectée ?**

**R :** Lors de la définition de la planification, nous définissons une fenêtre de temps de plusieurs jours. Toutefois, le séquencement exact des serveurs (et des machines virtuelles) au sein de cette fenêtre est inconnu. Les clients qui souhaitent connaître l’heure exacte pour leurs machines virtuelles peuvent utiliser des événements planifiés, faire une requête à partir de la machine virtuelle et recevoir une notification 10 minutes avant le redémarrage d’une machine virtuelle.
  
**Q : Combien de temps vous faudra-t-il pour redémarrer ma machine virtuelle ?**

**R :** En fonction de la taille de votre machine virtuelle, le redémarrage peut prendre plusieurs minutes. Notez que dans le cas où vous utilisez des services cloud, des groupes identiques ou un groupe à haute disponibilité, vous disposerez de 30 minutes entre chaque groupe de machines virtuelles (UD). 

**Q : Quelle sera l’expérience dans le cas des services cloud, des groupes identiques et de Service Fabric ?**

**R :** Alors que ces plateformes sont affectées par une maintenance planifiée, les clients qui utilisent ces plateformes sont considérés comme sécurisés étant donné que seules les machines virtuelles d’un domaine de mise à niveau (UD) seront affectées à un moment donné.  

**Q : J’ai reçu un e-mail concernant la désaffectation de matériel. Est-ce la même chose qu’une maintenance planifiée ?**

**R :** Alors que la désaffectation de matériel est un événement de maintenance planifié, nous n’avons pas encore intégré ce cas d’utilisation au nouvel environnement.  Nous estimons que les clients seraient déconcertés s’ils recevaient deux e-mails similaires concernant deux vagues d’opérations de maintenance planifiées différentes.

**Q : Je ne vois aucune information de maintenance sur mes machines virtuelles. Quelle est la cause du problème ?**

**R :** Plusieurs raisons peuvent expliquer pourquoi vous ne voyez aucune information de maintenance sur vos machines virtuelles :
1.  Vous utilisez un abonnement marqué comme interne à Microsoft.
2.  Vos machines virtuelles ne sont pas planifiées pour la maintenance. Il est possible que la vague d’opérations de maintenance soit terminée, annulée ou modifiée, de sorte que celle-ci n’affecte plus vos machines virtuelles.
3.  La colonne « Maintenance » n’a pas été ajoutée à la vue liste de vos machines virtuelles. Alors que nous avons ajouté cette colonne à la vue par défaut, les clients qui ont effectué une configuration pour afficher des colonnes non définies par défaut doivent ajouter manuellement la colonne **Maintenance** à la vue liste de leurs machines virtuelles.

**Q : La maintenance de ma machine virtuelle est planifiée pour la deuxième fois. Pourquoi ?**

**R :** Il existe plusieurs cas d’utilisation où vous verrez votre machine virtuelle planifiée pour la maintenance après avoir déjà effectué votre maintenance-redéploiement :
1.  Nous avons annulé la vague d’opérations de maintenance et l’avons redémarrée avec une charge utile différente. Il est possible que nous ayons détecté une charge utile ayant généré une erreur et nous voulons simplement déployer une charge utile supplémentaire.
2.  Votre machine virtuelle a été *réparée par service* sur un autre nœud en raison d’une panne matérielle.
3.  Vous avez choisi d’arrêter (de libérer) et de redémarrer la machine virtuelle.
4.  Vous avez activé **Arrêt automatique** pour la machine virtuelle.


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment vous pouvez vous inscrire aux événements de maintenance à partir de la machine virtuelle à l’aide de [Événements planifiés](scheduled-events.md).
