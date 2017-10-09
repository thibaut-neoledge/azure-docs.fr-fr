---
title: "Résolution des problèmes de Microsoft Azure Stack | Microsoft Docs"
description: "Résolution des problèmes d’Azure Stack."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 3b40a657ee8eb391d14a38cb95acc0729a8dda21
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="microsoft-azure-stack-troubleshooting"></a>Résolution des problèmes de Microsoft Azure Stack

*S’applique à : Kit de développement Azure Stack*

Ce document fournit des informations de résolution des problèmes courants pour Azure Stack. 

Étant donné que le kit de développement technique Azure Stack est proposé comme environnement d’évaluation, il n’y a aucune prise en charge officielle de la part des services clients Microsoft.  Si vous rencontrez un problème non documenté, veillez à consulter le [Forum MSDN Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) pour obtenir une assistance et des informations supplémentaires.  

Les recommandations pour la résolution des problèmes qui sont décrites dans cette section proviennent de différentes sources ; elles pourront peut-être résoudre votre problème en particulier. Les exemples de code sont fournis en l’état et les résultats attendus ne sont pas garantis. Cette section est susceptible de faire l’objet de modifications et de mises à jour fréquentes au fur et à mesure que des améliorations sont apportées au produit.

## <a name="deployment"></a>Déploiement
### <a name="deployment-failure"></a>Échec du déploiement
Si vous rencontrez un problème lors de l’installation, vous pouvez utiliser l’option de réexécution du script de déploiement pour relancer le déploiement à partir de l’étape qui n’a pas abouti.  


### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>À la fin du déploiement, la session PowerShell est toujours ouverte et ne présente aucune sortie
Ce comportement est probablement tout simplement le résultat du comportement par défaut d’une fenêtre de commande PowerShell, lorsqu’elle a été sélectionnée. Le déploiement du kit de développement a en fait réussi, mais le script a été interrompu au moment de la sélection de la fenêtre. Vous pouvez vérifier que c’est le cas en recherchant le mot « select » dans la barre de titre de la fenêtre de commande.  Appuyez sur la touche Échap pour la désélectionner ; le message d’achèvement devrait alors s’afficher.

## <a name="templates"></a>Modèles
### <a name="azure-template-wont-deploy-to-azure-stack"></a>Le modèle Azure ne se déploie pas sur Azure Stack
Vérifiez les points suivants :

* Le modèle utilise un service Microsoft Azure déjà disponible ou en préversion dans Azure Stack.
* Les API utilisées pour une ressource donnée sont prises en charge par l’instance Azure Stack locale, et vous ciblez un emplacement valide (« local » dans le kit de développement Azure Stack, mais « États-Unis de l’Est » ou « Inde du Sud » dans Azure).
* Vous avez lu [cet article](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md) sur les cmdlets Test-AzureRmResourceGroupDeployment, qui identifie de petites différences dans la syntaxe Azure Resource Manager.

Vous pouvez également utiliser les modèles Azure Stack déjà fournis dans le [référentiel GitHub](http://aka.ms/AzureStackGitHub/) pour commencer.

## <a name="virtual-machines"></a>Machines virtuelles
### <a name="default-image-and-gallery-item"></a>Élément de la galerie et image par défaut
Vous devez d’abord ajouter un élément de la galerie et une image Windows Server pour pouvoir déployer des machines virtuelles dans Azure Stack.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Après le redémarrage de l’hôte Azure Stack, certaines machines virtuelles ne démarrent pas automatiquement
Vous remarquerez peut-être que les services Azure Stack ne sont pas immédiatement disponibles après le redémarrage de votre hôte.  C’est parce que la vérification de la cohérence des RP et des [machines virtuelles d’infrastructure](azure-stack-architecture.md#virtual-machine-roles) Azure Stack demande un peu de temps ; toutefois, ils finiront par démarrer automatiquement.

Vous remarquerez peut-être aussi que les machines virtuelles clientes ne démarrent pas automatiquement après le redémarrage de l’hôte du kit de développement Azure Stack.  Ce problème est connu ; quelques étapes manuelles suffisent pour les mettre en ligne :

1.  Sur l’hôte du kit de développement Azure Stack, démarrez **Gestionnaire du cluster de basculement** dans le menu Démarrer.
2.  Sélectionnez le cluster **S-Cluster.azurestack.local**.
3.  Sélectionnez **Rôles**.
4.  Les machines virtuelles clientes apparaîtront avec l’état *enregistré*.  Lorsque toutes les machines virtuelles d’infrastructure sont en cours d’exécution, cliquez avec le bouton droit sur les machines virtuelles clientes et sélectionnez **Démarrer** pour reprendre la machine virtuelle.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>J’ai supprimé des machines virtuelles, mais je vois toujours les fichiers de VHD sur le disque. Ce comportement est-il attendu ?
Oui, c’est le comportement attendu. Il a été conçu ainsi pour les raisons suivantes :

* La suppression d’une machine virtuelle n’entraîne pas celle des VHD. Les disques sont des ressources distinctes dans le groupe de ressources.
* Lorsqu’un compte de stockage est supprimé, la suppression est visible immédiatement sur Azure Resource Manager (portail, PowerShell), mais les disques qu’il contient éventuellement restent conservés dans le stockage jusqu’à l’exécution du nettoyage de la mémoire.

Si vous voyez des VHD « orphelins », il est important de savoir s’ils font partie du dossier d’un compte de stockage supprimé. Si le compte de stockage n’a pas été supprimé, il est normal qu’ils soient toujours présents.

Pour en savoir plus sur la configuration du seuil de rétention et de la récupération à la demande, consultez la page [Gérer les comptes de stockage](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>Récupération du stockage
La capacité récupérée peut mettre jusqu’à 14 heures à s’afficher sur le portail. La récupération d’espace dépend de différents facteurs, notamment le pourcentage d’utilisation des fichiers conteneurs internes dans le magasin d’objets blob de blocs. Par conséquent, selon la quantité de données supprimées, il n’y a pas de garantie quant à la quantité d’espace récupérable lors de l’exécution du récupérateur de mémoire.

## <a name="powershell"></a>PowerShell
### <a name="resource-providers-not-registered"></a>Fournisseurs de ressources non enregistrés
Vous remarquerez que les fournisseurs de ressources ne sont pas automatiquement enregistrés lorsque vous vous connectez à des abonnements clients avec PowerShell. Utilisez le [module Connect](https://github.com/Azure/AzureStack-Tools/tree/master/Connect) ou exécutez la commande suivante dans PowerShell (après avoir [effectué l’installation et la connexion](azure-stack-connect-powershell.md) en tant que client) : 
  
       Get-AzureRMResourceProvider | Register-AzureRmResourceProvider

## <a name="cli"></a>Interface de ligne de commande

* Le mode interactif de l’interface CLI, c’est-à-dire la commande `az interactive`, n’est pas encore pris en charge dans Azure Stack.
* Pour obtenir la liste des images de machines virtuelles disponibles dans Azure Stack, utilisez la commande `az vm images list --all` au lieu de la commande `az vm image list`. L’option `--all` permet de faire en sorte que la réponse retourne uniquement les images disponibles dans l’environnement Azure Stack. 
* Les alias d’images de machines virtuelles disponibles dans Azure ne s’appliquent pas forcément à Azure Stack. Si vous utilisez des images de machines virtuelles, vous devez utiliser la totalité du paramètre URN (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) plutôt que l’alias de l’image. Cet URN doit par ailleurs correspondre aux spécifications des images dérivées de la commande `az vm images list`.
* Par défaut, CLI 2.0 utilise « Standard_DS1_v2 » comme taille d’image de machine virtuelle par défaut. Toutefois, cette taille n’est pas encore disponible dans Azure Stack : vous devez donc spécifier le paramètre `--size` explicitement lorsque vous créez une machine virtuelle. Vous pouvez obtenir la liste des tailles de machines virtuelles disponibles dans Azure Stack avec la commande `az vm list-sizes --location <locationName>`.


## <a name="windows-azure-pack-connector"></a>Connecteur Windows Azure Pack
* Si vous modifiez le mot de passe du compte azurestackadmin après avoir déployé le kit de développement Azure Stack, vous ne pourrez plus configurer le mode multicloud. Par conséquent, il ne sera pas possible de se connecter à l’environnement Windows Azure Pack cible.
* Après avoir configuré le mode multicloud :
    * Un utilisateur ne peut voir le tableau de bord qu’après avoir réinitialisé les paramètres du portail. (Sur le portail utilisateur, cliquez sur l’icône Paramètres de portail - l’icône d’engrenage dans le coin supérieur droit. Sous **Restaurer les paramètres par défaut**, cliquez sur **Appliquer**.)
    * Il est possible que les titres du tableau de bord n’apparaissent pas. Si ce problème se produit, vous devez les rajouter manuellement.
    * Il est possible que certaines vignettes ne s’affichent pas correctement dès l’ajout au tableau de bord. Pour résoudre ce problème, actualisez le navigateur.




