---
title: "Notes de publication du kit de développement Microsoft Azure Stack | Microsoft Docs"
description: 
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2bd93faf01c5d1790989a0231020ce8340eff57d
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="azure-stack-development-kit-release-notes"></a>Notes de publication du kit de développement Azure Stack

*S’applique à : Kit de développement Azure Stack*

Ces notes de publication fournissent des informations sur les nouvelles fonctionnalités et les problèmes connus.

## <a name="release-build-201706271"></a>Version 20170627.1
Depuis la version [20170627.1](azure-stack-updates.md#determine-the-current-version), Azure Stack Proof of Concept a été renommé kit de développement Azure Stack.  Tout comme Azure Stack POC, le kit de développement Azure Stack constitue un environnement de développement et d’évaluation permettant d’explorer les fonctionnalités d’Azure Stack et d’offrir une plateforme de développement pour Azure Stack.

### <a name="whats-new"></a>Nouveautés
- Vous pouvez maintenant utiliser CLI 2.0 pour gérer les ressources Azure Stack en ligne de commande sur les principaux systèmes d’exploitation.
- Les tailles de machine virtuelle DSV2 assurent la portabilité des modèles entre Azure et Azure Stack.
- Les opérateurs cloud peuvent avoir un aperçu de l’expérience de gestion de la capacité sur le panneau correspondant.
- Il est maintenant possible d’utiliser l’extension Azure Diagnostics pour collecter des données de diagnostic à partir de machines virtuelles.  La capture de ces données est utile pour analyser les performances des charges de travail et examiner les problèmes.
- Une nouvelle [expérience de déploiement](azure-stack-run-powershell-script.md) remplace les précédents scripts de déploiement par étapes.  Elle fournit une interface graphique commune à l’ensemble du cycle de vie de déploiement.
- Les comptes Microsoft (MSA) sont désormais pris en charge lors du déploiement.
- L’authentification multifacteur (MFA) est maintenant prise en charge lors du déploiement.  Auparavant, elle devait être désactivée pendant cette opération.

### <a name="known-issues"></a>Problèmes connus
#### <a name="deployment"></a>Déploiement
* Vous remarquerez peut-être que le déploiement prend plus de temps que dans les versions précédentes. 
* Get-AzureStackLogs génère des journaux de diagnostic, mais ne consigne pas la progression sur la console.
* Vous devez utiliser la nouvelle [expérience de déploiement](azure-stack-run-powershell-script.md) pour déployer Azure Stack, faute de quoi le déploiement pourrait échouer.
* Les déploiements effectués avec le paramètre *PublicVLANID* échouent.

#### <a name="portal"></a>Portail
* Il se peut qu’un tableau de bord vide s’affiche sur le portail.  Vous pouvez le récupérer en sélectionnant l’engrenage dans le coin supérieur droit du portail, puis « Restaurer les paramètres par défaut ».
* Les clients ont la possibilité de parcourir la totalité de la marketplace sans abonnement ; ils peuvent consulter certains éléments d’administration, comme les plans et les offres,  qui ne sont pas fonctionnels pour eux.
* Lorsque l’on sélectionne une instance de rôle d’infrastructure, une erreur de référence s’affiche. Utilisez la fonctionnalité d’actualisation du navigateur pour actualiser le portail d’administration.
* Le bouton « déplacer » est désactivé sur le panneau Groupe de ressources.  Il s’agit du comportement attendu, dans la mesure où le déplacement de groupes de ressources d’un abonnement à l’autre n’est pas pris en charge pour le moment.
* Vous recevrez plusieurs notifications à la fin du téléchargement d’articles de marketplace syndiqués.
* Vous n’avez pas la possibilité d’afficher les autorisations de votre abonnement sur les portails Azure Stack.  Pour contourner ce problème, vous pouvez vérifier les autorisations avec PowerShell.
* Vous devez ajouter `-TenantID` comme indicateur pour exporter un déploiement terminé comme script d’automatisation à partir du portail.

#### <a name="services"></a>Services
* Les services Key Vault doivent être créés à partir du portail locataire ou de l’API locataire.  Si vous êtes connecté en tant qu’administrateur, assurez-vous que le portail client permet de créer de nouveaux coffres Key Vault, de nouveaux secrets et de nouvelles clés.
* Il n’existe aucune expérience de création de groupes de machines virtuelles identiques sur la marketplace, bien qu’il soit possible de le faire avec un modèle.
* Il n’est pas possible d’associer un équilibreur de charge à un réseau principal avec le portail.  Cette tâche peut être effectuée avec PowerShell ou avec un modèle.
* Les groupes de machines virtuelles à haute disponibilité ne sont configurables qu’avec un domaine d’erreur et un domaine de mise à jour chacun de valeur égale à un.  
* Un client doit déjà disposer d’un compte de stockage pour pouvoir créer une nouvelle fonction Azure.
* La machine virtuelle peut échouer avec l’état « Cannot bind argument to parameter ’VM Network Adapter’ because it is null » (« Impossible de lier l’argument au paramètre « Carte réseau de la machine virtuelle », car il est Null »).  Le redéploiement de la machine virtuelle réussit.  
* La suppression d’abonnements clients aboutit à des ressources orphelines.  Pour contourner ce problème, supprimez tout d’abord les ressources clientes ou la totalité du groupe de ressources, puis supprimez les abonnements clients. 
* Il est impératif de créer une règle NAT pour créer un équilibreur de charge réseau ; si vous essayez d’en ajouter une après avoir créé l’équilibreur de charge, une erreur s’affiche.
* Les clients peuvent créer des machines virtuelles plus grandes que le quota ne l’autorise.  En effet, les quotas de calcul ne s’appliquent pas.
* Les clients ont le choix de créer une machine virtuelle avec stockage géoredondant.  Cette configuration fait échouer la création.
* Il faut parfois attendre une heure pour qu’ils puissent créer des bases de données avec une nouvelle référence SQL ou MySQL. 
* La création d’éléments directement sur les serveurs d’hébergement MySQL et SQL, s’ils ne sont pas réalisés par le fournisseur de ressources, n’est pas prise en charge et peut aboutir à un état incompatible.
* AzureRM PowerShell 1.2.10 requiert des étapes de configuration supplémentaires :
    * Exécutez cette commande après avoir lancé Add-AzureRMEnvironment pour les déploiements Azure AD.  Indiquez les valeurs de Name et de GraphAudience en fonction de la sortie de `Add-AzureRMEnvironment`.
      
      ```PowerShell
      Set-AzureRmEnvironment -Name <Environment Name> -GraphAudience <Graph Endpoint URL>
      ```
    * Exécutez cette commande après avoir lancé Add-AzureRMEnvironment pour les déploiements AD FS.  Indiquez les valeurs de Name et de GraphAudience en fonction de la sortie de `Add-AzureRMEnvironment`.
      
      ```PowerShell
      Set-AzureRmEnvironment <Environment Name> -GraphAudience <Graph Endpoint URL> -EnableAdfsAuthentication:$true
      ```
    
    Par exemple, la commande suivante est utilisée pour un environnement Azure AD :

    ```PowerShell
      Set-AzureRmEnvironment AzureStack -GraphAudience https://graph.local.azurestack.external/
    ```

#### <a name="fabric"></a>Structure
* Le fournisseur de ressources de calcul présente un état inconnu.
* Le modèle et l’adresse IP BMC ne s’affichent pas dans les informations essentielles d’un nœud d’unité d’échelle.  Ce comportement est attendu dans le kit de développement Azure Stack.
* L’action de redémarrage du rôle d’infrastructure du contrôleur de calcul (instance AzS-XRP01) ne doit pas être utilisée.
* Le panneau de sauvegarde de l’infrastructure ne doit pas être utilisé.

