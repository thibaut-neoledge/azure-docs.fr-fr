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
ms.date: 10/13/2017
ms.author: helaw
ms.openlocfilehash: 5ee2f8164d13e61f9a43abcbda729298f2168518
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2017
---
# <a name="azure-stack-development-kit-release-notes"></a>Notes de publication du kit de développement Azure Stack

*S’applique à : Kit de développement Azure Stack*

Ces notes de publication fournissent des informations sur les nouvelles fonctionnalités et les problèmes connus dans le Kit de développement Azure Stack. Si vous n’êtes pas sûr de la version que vous exécutez, consultez le [portail pour vérifier](azure-stack-updates.md#determine-the-current-version).

## <a name="release-build-201709283"></a>Version 20170928.3

### <a name="known-issues"></a>Problèmes connus

#### <a name="powershell"></a>PowerShell

- La version du module PowerShell AzureRM 1.2.11 est fournie avec une liste des modifications importantes. Pour des informations sur la mise à niveau à partir de la version 1.2.10, consultez le guide de migration à l’adresse [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration).


#### <a name="deployment"></a>Déploiement
* Vous devez spécifier un serveur de temps par adresse IP au cours du déploiement.  


#### <a name="portal"></a>Portail
* Il se peut qu’un tableau de bord vide s’affiche sur le portail.  Vous pouvez le récupérer en sélectionnant l’engrenage dans le coin supérieur droit du portail, puis « Restaurer les paramètres par défaut ».
* Les clients ont la possibilité de parcourir la totalité de la marketplace sans abonnement ; ils peuvent consulter certains éléments d’administration, comme les plans et les offres,  qui ne sont pas fonctionnels pour eux.
* Le bouton « déplacer » est désactivé sous Groupe de ressources.  Il s’agit du comportement attendu, dans la mesure où le déplacement de groupes de ressources d’un abonnement à l’autre n’est pas pris en charge pour le moment.
* Vous n’avez pas la possibilité d’afficher les autorisations de votre abonnement sur les portails Azure Stack.  Pour contourner ce problème, vous pouvez vérifier les autorisations avec PowerShell.
* Vous verrez une alerte vous invitant à enregistrer votre Kit de développement Azure Stack.  Cette alerte est normale.  
  

#### <a name="services"></a>Services
* Il n’existe aucune expérience de création de groupes de machines virtuelles identiques sur la marketplace, bien qu’il soit possible de le faire avec un modèle.
* Vous ne pouvez pas créer d’équilibreur de charge avec une adresse IP publique à l’aide du portail.  Pour résoudre ce problème, vous pouvez utiliser PowerShell pour créer l’équilibreur de charge.
* Les groupes de machines virtuelles à haute disponibilité ne sont configurables qu’avec un domaine d’erreur et un domaine de mise à jour chacun de valeur égale à un.  
* Un client doit inscrire le fournisseur de ressources de stockage avant de créer sa première fonction Azure dans l’abonnement.
* La suppression d’abonnements clients aboutit à des ressources orphelines.  Pour contourner ce problème, supprimez tout d’abord les ressources clientes ou la totalité du groupe de ressources, puis supprimez les abonnements clients. 
* Vous devez créer une règle NAT lorsque vous créez un équilibreur de charge réseau. Si vous ne le faites pas, une erreur s’affiche lorsque vous essayez d’ajouter une règle NAT après avoir créé l’équilibreur de charge.
* Les clients ont le choix de créer une machine virtuelle avec stockage géoredondant.  Cette configuration fait échouer la création.
* Il faut parfois attendre une heure pour qu’ils puissent créer des bases de données avec une nouvelle référence SQL ou MySQL. 
* La création d’éléments directement sur les serveurs d’hébergement MySQL et SQL, s’ils ne sont pas réalisés par le fournisseur de ressources, n’est pas prise en charge et peut aboutir à un état incompatible.
* Le panneau de sauvegarde de l’infrastructure ne doit pas être utilisé.


#### <a name="fabric"></a>Structure
* Le fournisseur de ressources de calcul présente un état inconnu.
* Le modèle et l’adresse IP du BMC ne s’affichent pas dans les informations essentielles d’un nœud d’unité d’échelle.  Ce comportement est attendu dans le kit de développement Azure Stack.

