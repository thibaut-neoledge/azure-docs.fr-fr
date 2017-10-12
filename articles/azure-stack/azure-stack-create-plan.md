---
title: "Créer un plan dans Azure Stack | Microsoft Docs"
description: "En tant qu’administrateur cloud, créez un plan permettant aux abonnés d’approvisionner des machines virtuelles."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 30759dca746fd7fd02653556cb105f419f5bf854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-plan-in-azure-stack"></a>Créer un plan dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Les [plans](azure-stack-key-features.md) regroupent un ou plusieurs services. En tant que fournisseur, vous pouvez créer des plans à proposer à vos utilisateurs. En retour, ceux-ci s’abonnent à vos offres pour utiliser les plans et les services qu’elles comprennent. Cet exemple montre comment créer un plan comprenant les fournisseurs de ressources de stockage, réseau et de calcul. Ce plan donne aux abonnés la possibilité d’approvisionner des machines virtuelles.

1. Connectez-vous au portail d’administration Azure Stack (https://adminportal.local.azurestack.external). Entrez les informations d’identification du compte que vous avez créé à l’étape 5 de la section [Exécuter le script PowerShell](azure-stack-run-powershell-script.md).

2. Pour créer un plan et une offre auxquels les utilisateurs peuvent s’abonner, cliquez sur **Nouveau** > **Plans + offres clients** > **Plan**.

   ![](media/azure-stack-create-plan/image01.png)
3. Sur le panneau **Nouveau plan**, renseignez le **Nom d’affichage** et le **Nom de la ressource**. Le nom d’affichage correspond au nom convivial du plan, que les locataires voient. Seul l’administrateur peut voir le nom de la ressource. Il s’agit du nom que les administrateurs utilisent pour gérer le plan en tant que ressource Azure Resource Manager.

   ![](media/azure-stack-create-plan/image02.png)
4. Créez un **Groupe de ressources** ou sélectionnez-en un qui servira de conteneur pour le plan.

   ![](media/azure-stack-create-plan/image02a.png)
5. Cliquez sur **Services**, sélectionnez **Microsoft.Compute**, **Microsoft.Network** et **Microsoft.Storage**, puis cliquez sur **Sélectionner**.

   ![](media/azure-stack-create-plan/image03.png)
6. Cliquez sur **Quotas** et sur **Microsoft.Storage (local)**, puis sélectionnez le quota par défaut ou cliquez sur **Créer un quota** pour personnaliser le quota.

   ![](media/azure-stack-create-plan/image04.png)
7. Si vous créez un quota, entrez un nom pour le quota > définissez ses valeurs > cliquez sur **OK** > cliquez sur le nom du nouveau quota.

   ![](media/azure-stack-create-plan/image06.png)
8. Cliquez sur **Microsoft.Network (local)**, puis sélectionnez le quota par défaut ou cliquez sur **Créer un quota** pour le personnaliser.

    ![](media/azure-stack-create-plan/image07.png)
9. Si vous créez un quota, tapez un nom pour le quota > définissez ses valeurs > cliquez sur **OK** > cliquez sur le nom du nouveau quota.

    ![](media/azure-stack-create-plan/image08.png)
10. Cliquez sur **Microsoft.Compute (local)**, puis sélectionnez le quota par défaut ou cliquez sur **Créer un quota** pour le personnaliser.

    ![](media/azure-stack-create-plan/image09.png)
11. Si vous créez un quota, tapez un nom pour le quota > définissez ses valeurs > cliquez sur **OK** > cliquez sur le nom du nouveau quota.

    ![](media/azure-stack-create-plan/image10.png)
12. Sur le panneau **Quotas**, cliquez sur **OK**, puis, sur le panneau **Nouveau plan**, cliquez sur **Créer** pour créer le plan.

    ![](media/azure-stack-create-plan/image11.png)
13. Pour voir votre nouveau plan, cliquez sur **Toutes les ressources**, puis recherchez-le et cliquez sur son nom.

    ![](media/azure-stack-create-plan/image12.png)

### <a name="next-steps"></a>Étapes suivantes
[Créer une offre](azure-stack-create-offer.md)
