---
title: "Préparer la cible (Physique vers Azure) | Microsoft Docs"
description: "Cet article décrit comment préparer votre environnement Azure pour lancer la réplication des serveurs physiques exécutant Windows ou Linux vers Azure."
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 2/11/2017
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: b7f26df96ddedb579cf5d9d20fee6b2599e762e0
ms.openlocfilehash: 9dd4e9007da6cf276f976e2f3f25305286830544

---

# <a name="prepare-target-vmware-to-azure"></a>Préparer la cible (VMware vers Azure)
> [!div class="op_single_selector"]
> * [Machines virtuelles VMware](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Serveurs physiques](./site-recovery-prepare-target-physical-to-azure.md)

Cet article décrit comment préparer votre environnement Azure pour lancer la réplication des serveurs physiques (x64) exécutant Windows ou Linux vers Azure.

## <a name="prerequisites"></a>Composants requis

Cet article part des principes suivants :
- Vous avez créé un coffre Recovery Services pour protéger vos serveurs physiques. Vous pouvez créer un coffre Recovery Services dans le [portail Azure](http://portal.azure.com "portail Azure").
- Vous avez [configuré votre environnement local](./site-recovery-set-up-physical-to-azure.md) pour répliquer les serveurs physiques vers Azure.

## <a name="prepare-target"></a>Préparer la cible

Après avoir réalisé **l’Étape 1 : Sélection de l’objectif de la protection** et **l’Étape 2 : Préparation de la source**, vous passez à **l’Étape 3 : Cible**.

![Préparer la cible](./media/site-recovery-prepare-target-physical-to-azure/prepare-target-physical-to-azure.png)

1. **Abonnement :** dans le menu déroulant, sélectionnez l’abonnement vers lequel vous souhaitez répliquer vos serveurs physiques.
2. **Modèle de déploiement :** sélectionnez le modèle de déploiement (Classique ou Gestionnaire de ressources).

Selon le modèle de déploiement choisi, une validation est exécutée pour vérifier que vous disposez au moins d’un compte de stockage et d’un réseau virtuel compatibles dans l’abonnement cible pour la réplication et le basculement de vos serveurs physiques.

Une fois les validations terminées avec succès, cliquez sur OK pour passer à l’étape suivante.

Si vous n’avez pas de compte de stockage Resource Manager ou réseau virtuel compatible, ou si vous souhaitez en ajouter d’autres, vous pouvez le faire en cliquant sur les boutons **+ Compte de stockage** ou **+ Réseau** en haut du panneau.

## <a name="next-steps"></a>Étapes suivantes
[Configurez les paramètres de réplication](./site-recovery-setup-replication-settings-vmware.md).



<!--HONumber=Feb17_HO2-->


