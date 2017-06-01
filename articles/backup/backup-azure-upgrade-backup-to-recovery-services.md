---
title: "Mise à niveau d’un coffre de sauvegarde vers un coffre Recovery Services (version préliminaire) | Documents Microsoft"
description: "Commandes et informations de support technique pour mettre à niveau un coffre de sauvegarde Azure vers un coffre Recovery Services."
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
ms.assetid: 228fef19-2f6b-4067-acc3-fb6e501afb88
ms.service: required
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/10/2017
ms.author: markgal;arunak
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 00744d70a7644d80cfd99acbcf96966574f4512e
ms.contentlocale: fr-fr
ms.lasthandoff: 05/16/2017


---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Mettre à niveau un coffre de sauvegarde vers un coffre Recovery Services

Cet article explique comment mettre à niveau un coffre de sauvegarde vers un coffre Recovery Services. Le processus de mise à niveau n’a d’incidence sur aucun travail de sauvegarde en cours, et n’entraîne aucune perte de données de sauvegarde. Principales raisons de mettre à niveau un coffre de sauvegarde vers un coffre Recovery Services :
- Toutes les fonctionnalités d’un coffre de sauvegarde sont conservées dans un coffre Recovery Services.
- Les coffres Recovery Services offrent davantage de fonctionnalités que les coffres de sauvegarde, dont une meilleure sécurité, une surveillance intégrée, des restaurations plus rapides et des restaurations au niveau élément, et la gestion des éléments de sauvegarde à partir d’un portail amélioré et simplifié.
-  Les nouvelles fonctionnalités seront mises en production uniquement pour coffre Recovery Services.

## <a name="impact-to-operations-during-upgrade"></a>Incidence de la mise à niveau sur les opérations

La mise à niveau d’un coffre de sauvegarde vers un coffre Recovery Services n’a aucune incidence sur les opérations de votre plan de données. Tous les travaux de sauvegarde continuent à se dérouler normalement, et tous les travaux de restauration actifs se poursuivent sans interruption. Lors de la mise à niveau, les opérations de gestion sont brièvement interrompues et vous ne pouvez pas protéger de nouveaux éléments ou créer des travaux de sauvegarde ad hoc. Les travaux de restauration de machines virtuelles IaaS ne s’exécutent pendant la mise à niveau. <MG> : quelle est la différence entre les travaux de restauration actifs sortant de la normale et les travaux de restauration le machine virtuelle IaaS ?
La mise à niveau du coffre prend moins d’une heure. Quand elle est terminée, un coffre Recovery Services remplace le coffre de sauvegarde.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Modifications apportées à l’automatisation et à l’outil après mise à niveau

Lors de la préparation de votre infrastructure pour la mise à niveau du coffre, vous devez mettre à jour l’automation ou les outils existants pour vous assurer qu’ils continuent à fonctionner après la mise à niveau.
Consultez les références d’applets de commande PowerShell pour le [modèle de déploiement Service Manager](backup-client-automation-classic.md) et le [modèle de déploiement Resource Manager](backup-client-automation.md).


## <a name="before-you-upgrade"></a>Avant de mettre à niveau

Avant de mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services, vérifiez les points suivants.

- **Version minimale de l’agent** : pour mettre à niveau votre coffre, assurez-vous que la version de l’agent Microsoft Azure Recovery Services (MARS) est au minimum la version 2.0.9070.0. Si la version de l’agent MARS est antérieure à la version 2.0.9070.0, mettez à jour l’agent avant de commencer le processus de mise à niveau.
- **Modèle de facturation basé sur l’instance** : les coffres Recovery Services prennent uniquement en charge le modèle de facturation basée sur l’instance. Si vous avez un coffre de sauvegarde qui utilise l’ancien modèle de facturation basée sur le stockage, convertissez le modèle de facturation durant la mise à niveau.
- **Aucune opération de configuration de la sauvegarde en cours** : durant la mise à niveau, l’accès au plan de gestion est limité. Effectuez toutes les actions du plan de gestion, puis démarrez la mise à niveau.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Utilisation de scripts PowerShell pour mettre à niveau vos coffres

Vous pouvez utiliser des scripts PowerShell pour mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services. Vérifiez que vous disposez des composants PowerShell requis pour déclencher la mise à niveau de coffre. Les scripts PowerShell pour les coffres de sauvegarde ne fonctionnent pas pour les coffres Recovery Services. Préparez votre environnement à la mise à niveau des coffres :

1. Installez ou mettez à niveau [Windows Management Framework (WMF) vers la version 5](https://www.microsoft.com/download/details.aspx?id=50395) ou ultérieure.
2. Téléchargez et installez [Azure PowerShell 3.7.0](https://github.com/Azure/azure-powershell/releases/download/v3.7.0-March2017/azure-powershell.3.7.0.msi).
3. Mettez à jour les modules PowerShell :
  - Pour Windows Server :
    - Install-Module -Name Azure -RequiredVersion 3.8.0
    - Install-Module -Name AzureRM.RecoveryServices -RequiredVersion 2.8.0
    - Install-Module -Name AzureRM.Resources -RequiredVersion 3.8.0
  - Pour Windows 10 :
    - Install-Module -Name Azure -RequiredVersion 3.8.0 -AllowClobber
    - Install-Module -Name AzureRM.RecoveryServices -RequiredVersion 2.8.0 -AllowClobber
    - Install-Module -Name AzureRM.Resources -RequiredVersion 3.8.0 -AllowClobber
4. Téléchargez le [script PowerShell](http://download.microsoft.com/download/1/c/6/1c6ed72e-38f9-4675-9cf9-9b8a06da7cd3/recoveryservicesvaultupgrade.ps1) pour mettre à niveau vos coffres.

### <a name="run-the-powershell-script"></a>Exécution du script PowerShell

Utilisez le script suivant pour mettre à niveau vos coffres. L’exemple de script suivant comprend des explications des paramètres.

RecoveryServicesVaultUpgrade.ps1 **-SubscriptionID** `<subscriptionID>` **-VaultName** `<vaultname>` **-Location** `<location>` **-ResourceType** `BackupVault` **-TargetResourceGroupName** `<rgname>`


SubscriptionID : numéro d’ID d’abonnement du coffre en cours de mise à niveau.
VaultName : nom du coffre de sauvegarde en cours de mise à niveau.
Location : emplacement du coffre en cours de mise à niveau.
ResourceType : utilisez BackupVault.
TargetResourceGroupName : étant donné que vous mettez à niveau le coffre vers un déploiement basé sur Resource Manager, spécifiez un groupe de ressources. Vous pouvez utiliser un groupe de ressources existant ou en créez un en fournissant un nouveau nom. Si vous orthographiez mal le nom d’un groupe de ressources, vous pouvez en créer un autre. Pour plus d’informations sur les groupes de ressources, voir la [Présentation des groupes de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Les noms de groupes de ressources doivent respecter certaines contraintes. Veillez à suivre les recommandations. Le non-respect de celles-ci pourrait entraîner l’échec des mises à niveau de coffre.
>
>

L’extrait de code suivant illustre ce à quoi votre commande PowerShell devrait ressembler :

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

Vous pouvez également exécuter le script sans paramètre. Vous êtes alors invité à fournir des entrées pour tous les paramètres obligatoires.

Le script PowerShell vous invite à entrer vos informations d’identification. Entrez vos informations d’identification à deux reprises : une première fois pour le compte Service Manager et une deuxième fois pour le compte Resource Manager.

### <a name="pre-requisites-checking"></a>Vérification des conditions préalables
Une fois que vous avez entré vos informations d’identification Azure, Azure vérifie que votre environnement remplit les conditions préalables suivantes :

- **Version minimale de l’agent** : la mise à niveau des coffres de sauvegarde vers des coffres Recovery Services exige que la version de l’agent MARS soit au moins la version 2.0.9070. Si vous avez des éléments inscrits auprès d’un coffre de sauvegarde avec un agent dont la version est antérieure à la version 2.0.9070, la vérification des conditions préalables échoue. Si la vérification des conditions préalables échoue, mettez à jour l’agent et réessayez de mettre à niveau le coffre. Vous pouvez télécharger la dernière version de l’agent à partir de l’URL [http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Tâches de configuration en cours** : si quelqu’un est en train de configurer un travail pour un coffre de sauvegarde défini pour être mis à niveau, ou d’inscrire un élément, la vérification des conditions préalables échoue. Achevez la configuration ou l’inscription de l’élément, puis démarrez le processus de mise à niveau du coffre.
- **Modèle de facturation basée sur le stockage** : les coffres Recovery Services prennent en charge le modèle de facturation basée sur l’instance. Si vous exécutez la mise à niveau du coffre sur un coffre de sauvegarde utilisant le modèle de facturation basée sur le stockage, vous devrez mettre à niveau le modèle de facturation en même temps que le coffre. Autrement, vous pouvez commencer par mettre à jour votre modèle de facturation, avant d’exécuter la mise à niveau du coffre.
- Identifiez un groupe de ressources pour le coffre Recovery Services. Pour tirer parti des fonctionnalités de déploiement de Resource Manager, vous devez placer un coffre Recovery Services dans un groupe de ressources. Si vous ignorez le groupe de ressources à utiliser, entrez un nom. Le processus de mise à niveau crée alors le groupe de ressources pour vous. Le processus de mise à niveau associe également le coffre au nouveau groupe de ressources.

Une fois que le processus de mise à niveau a fini de vérifier les conditions préalables, le processus vous invite à démarrer la mise à niveau du coffre. Après que vous avez confirmé, l’exécution du processus de mise à niveau prend généralement de 15 à 20 minutes, selon la taille de votre coffre. Si vous avez un coffre de grande taille, la mise à niveau peut prendre jusqu’à 90 minutes.

## <a name="managing-your-recovery-services-vaults"></a>Accéder à vos coffres Recovery Services

Les écrans suivants montrent un nouveau coffre Recovery Services, mis à niveau à partir du coffre de sauvegarde, dans le portail Azure. Le premier écran présente le tableau de bord du coffre qui affiche les entités clés pour le coffre.

![exemple de coffre Recovery Services mis à niveau à partir d’un coffre de sauvegarde](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Le deuxième écran présente les liens d’aide disponibles pour vous aider à commencer à utiliser le coffre Recovery Services.

![liens d’aide dans le panneau Démarrage rapide](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="frequently-asked-questions"></a>Forum Aux Questions

**Le plan de mise à niveau affecte-t-il mes sauvegardes en cours ?**</br>
Non. Vos sauvegardes en cours se poursuivent sans interruption pendant et après la mise à niveau.

**Qu’advient-il de mes coffres si je n’envisage de mise à niveau prochainement ?**</br>
Étant donné que toutes les nouvelles fonctionnalités sont planifiées pour un coffre Recovery Services et que l’utilisation du portail Azure Classic sera déconseillera, Microsoft déconseillera également les coffres de sauvegarde et le modèle Service Manager. À l’avenir, Microsoft déclenchera une mise à niveau automatique de tous les coffres vers des coffres Recovery Services. En attendant, les clients décident du moment de mise à niveau de leurs coffres.

**Quelles sont les implications de ce plan de migration pour mes outils existants ?**</br>  
La mise à jour de vos outils vers le modèle de déploiement Resource Manager sur lequel les coffres Recovery Services sont basés est l’une des principales modifications que vous devez prendre en compte dans vos plans de mise à niveau.

**Quelle sera la durée du temps d’arrêt ?**</br>
Cette durée dépend du nombre de ressources faisant l’objet de la mise à niveau. Pour les déploiements de taille modeste (quelques dizaines d’instances protégées), la mise à niveau entière doit prendre moins de 20 minutes. Pour les déploiements plus importants, il ne devrait pas excéder une heure.

**Puis-je restaurer après la mise à niveau ?**</br>
Non. La restauration n’est pas prise en charge une fois la mise à niveau des ressources opérée avec succès.

**Puis-je vérifier si mon abonnement ou mes ressources peuvent faire l’objet d’une mise à niveau ?**</br>
Oui. La première étape d’une mise à niveau consiste à valider le fait que les ressources peuvent être mises à niveau. En cas d’échec de la validation des conditions préalables, vous recevez des messages concernant toutes les raisons pour lesquelles la mise à niveau ne peut pas être effectuée.

**Quelles sont les autorisations dont je dois disposer pour déclencher la mise à niveau du coffre ?**</br>
Pour pouvoir effectuer la mise à niveau du coffre, vous devez avoir été ajouté en tant que coadministrateur de l’abonnement dans le portail Azure Classic. Cela est indispensable, même si vous êtes déjà ajouté en tant que propriétaire dans le portail Azure. Essayez d’ajouter un coadministrateur pour l’abonnement dans le portail Azure Classic pour savoir si vous êtes coadministrateur de l’abonnement. Si vous n’êtes pas en mesure d’ajouter un coadministrateur, contactez un administrateur de service ou un coadministrateur de l’abonnement afin qu’il vous ajoute.

**Puis-je mettre à niveau mon coffre de sauvegarde basé sur un fournisseur de services de chiffrement ?**</br>
Non. Actuellement, il n’est pas possible de mettre à niveau des coffres de sauvegarde basés sur un fournisseur de services de chiffrement. Nous ajouterons la prise en charge de cette mise à niveau dans les prochaines publications.

**Puis-je afficher mon coffre Azure Classic après mise à niveau ?**</br>
Non. Vous ne pouvez pas afficher ou gérer votre coffre classique après mise à niveau. Vous pouvez utiliser le nouveau portail Azure uniquement pour toutes les actions de gestion sur le coffre.

**Ma mise à niveau a échoué et que je dois mettre à jour l’agent sur un ordinateur qui n’existe plus. Que faire dans ce cas ?**</br>
Si vous devez stocker les sauvegardes de cet ordinateur pour une rétention à long terme, vous ne pourrez pas mettre à niveau le coffre. Nous ajouterons une prise en charge de la mise à niveau d’un tel coffre dans des versions futures.
Si vous n’avez plus besoin de stocker les sauvegardes de cet ordinateur, veuillez désinscrire celui-ci du coffre et réessayer la mise à niveau.

**Pourquoi ne puis-je pas voir les informations relatives aux travaux de mes ressources locales après la mise à niveau ?**</br>
La surveillance des sauvegardes locales (agent MARS, DPM et MABS) est une nouvelle fonctionnalité que vous obtenez lorsque vous mettez à niveau votre coffre de sauvegarde vers un coffre Recovery Services. La synchronisation des informations de surveillance avec le service prend jusqu’à 12 heures.

**Comment signaler un problème ?**</br>
Si vous êtes confronté à des défaillances durant la mise à niveau, notez la valeur OperationId mentionnée dans le message d’erreur. Le Support Microsoft travaillera de façon proactive à la résolution du problème. Vous pouvez contacter le Support ou nous envoyer un e-mail à l’adresse rsvaultupgrade@service.microsoft.com avec votre Id d’abonnement, le nom du coffre et l’ID de l’opération. Nous nous efforcerons de résoudre le problème au plus tôt. Ne retentez l’opération que sauf si vous y êtes explicitement invité par Microsoft.


## <a name="next-steps"></a>Étapes suivantes
Consultez l’article suivant pour :</br>
[Sauvegarder une machine virtuelle IaaS](backup-azure-arm-vms-prepare.md)</br>
[Sauvegarder un serveur de sauvegarde Azure](backup-azure-microsoft-azure-backup.md)</br>
[Sauvegarder un serveur Windows Server](backup-configure-vault.md)

