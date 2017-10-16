---
title: "Configurer une stratégie de réplication pour la réplication Hyper-V vers un site secondaire à l’aide d’Azure Site Recovery | Documents Microsoft"
description: "Décrit comment configurer une stratégie pour la réplication des machines virtuelles Hyper-V vers un site VMM à l’aide d’Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5d9b79cf-89f2-4af9-ac8e-3a32ad8c6c4d
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: a3ecc555e049914d7bffdddce7b0522f09362d24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="step-8-set-up-a-replication-policy"></a>Étape 8 : Configurer une stratégie de réplication

Après avoir configuré [le mappage réseau](vmm-to-vmm-walkthrough-network-mapping.md), utilisez cet article pour définir une stratégie de réplication pour la réplication de machine virtuelle (VM) Hyper-V vers un site secondaire, à l’aide [d’Azure Site Recovery](site-recovery-overview.md).

Après avoir lu cet article, publiez des commentaires au bas de ce dernier ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Avant de commencer

- Lorsque vous créez une stratégie de réplication, l’ensemble des hôtes utilisant la stratégie doivent posséder le même système d’exploitation. Le cloud VMM peut contenir des hôtes Hyper-V exécutant différentes versions de Windows Server. Dans ce cas, vous devez posséder plusieurs stratégies de réplication.
- Vous pouvez exécuter la réplication initiale hors connexion.

## <a name="configure-replication-settings"></a>Configurer les paramètres de réplication

1. Pour créer une stratégie de réplication, cliquez sur **Préparer l’infrastructure** > **Paramètres de réplication** > **+Créer et associer**.

    ![Réseau](./media/vmm-to-vmm-walkthrough-replication/gs-replication.png)
2. Dans **Créer et associer une stratégie**, indiquez le nom de la stratégie. Le type source et cible doit être **Hyper-V**.
3. Dans **Version de l’hôte Hyper-V**, sélectionnez le système d’exploitation sous lequel fonctionne l’hôte.
4. Dans **Type d’authentification** et **Port d’authentification**, indiquez le mode d’authentification du trafic entre les serveurs hôtes Hyper-V principaux et de récupération. Sélectionnez **Certificat** , sauf si vous avez un environnement Kerberos opérationnel. Azure Site Recovery configurera automatiquement des certificats pour l'authentification HTTPS. Vous n’avez pas besoin de faire quoi que ce soit manuellement. Par défaut, les ports 8083 et 8084 (pour les certificats) seront ouverts dans le Pare-feu Windows sur les serveurs hôtes Hyper-V. Si vous sélectionnez **Kerberos**, un ticket Kerberos est utilisé pour l'authentification mutuelle des serveurs hôtes. Notez que ce paramètre n'est utile que pour les serveurs hôtes Hyper-V s'exécutant sur Windows Server 2012 R2.
5. Dans **Fréquence de copie**, spécifiez la fréquence selon laquelle répliquer les données delta après la réplication initiale (toutes les 30 secondes ou toutes les 5 ou 15 minutes).
6. Dans **Rétention des points de récupération**, spécifiez la durée de la fenêtre de rétention pour chaque point de récupération (en heures). Les machines protégées peuvent être récupérées à tout moment pendant cette fenêtre temporelle.
7. Dans **Fréquence des captures instantanées cohérentes de l’application**, spécifiez la fréquence de création des points de récupération contenant des instantanés cohérents au niveau des applications (entre 1 et 12 heures). Hyper-V utilise deux types d’instantanés : un instantané standard qui fournit un instantané incrémentiel de la machine virtuelle complète et un instantané cohérent avec l'application qui prend un instantané des données d'application d'une machine virtuelle. Les instantanés cohérents avec l'application utilisent le service VSS (Volume Shadow Copy Service) pour s'assurer que les applications sont dans un état cohérent lors de la prise des instantanés. Si vous activez les instantanés cohérents avec l'application, cela affecte les performances des applications exécutées sur les machines virtuelles sources. Assurez-vous que la valeur définie est inférieure au nombre de points de récupération supplémentaires que vous configurez.
8. Dans **Compression du transfert de données**, indiquez si les données répliquées transférées doivent être compressées.
9. Sélectionnez **Supprimer une machine virtuelle de réplica** pour spécifier que la machine virtuelle doit être supprimée si vous désactivez la protection de la machine virtuelle source. Si vous activez ce paramètre et que vous désactivez la protection de la machine virtuelle source, elle est supprimée de la console Site Recovery, les paramètres Site Recovery de VMM sont supprimés de la console VMM et le réplica est supprimé.
10. Dans **Méthode de réplication initiale**, si vous effectuez une réplication sur le réseau, indiquez si vous souhaitez lancer la réplication initiale ou la planifier. Pour économiser de la bande passante réseau, il peut être intéressant de la planifier en dehors des heures de pointe. Cliquez ensuite sur **OK**.

     ![Stratégie de réplication](./media/vmm-to-vmm-walkthrough-replication/gs-replication2.png)
11. Lorsque vous créez une stratégie, elle est automatiquement associée au cloud VMM. Dans **Stratégie de réplication**, cliquez sur **OK**. Vous pouvez associer des clouds VMM supplémentaires (ainsi que les machines virtuelles correspondantes) à cette stratégie de réplication en cliquant sur **Réplication** > Nom de la stratégie > **Associer un Cloud VMM**.

     ![Stratégie de réplication](./media/vmm-to-vmm-walkthrough-replication/policy-associate.png)



## <a name="prepare-for-offline-initial-replication"></a>Préparer la réplication initiale hors connexion

Vous pouvez effectuer la réplication hors connexion pour la copie initiale des données. Vous pouvez la préparer comme suit :

* Sur le serveur source, vous spécifiez un emplacement de chemin d'accès à partir duquel l'exportation des données sera exécutée. Attribuez un contrôle total pour NTFS et des autorisations de partage au service VMM sur le chemin d'exportation. Sur le serveur cible, vous spécifiez un emplacement de chemin d'accès à partir duquel l'importation des données sera exécutée. Attribuez les mêmes autorisations sur ce chemin d'accès d'importation.
* Si le chemin d'accès d'importation ou d'exportation est partagé, attribuez l'appartenance au groupe Administrateur, Utilisateur avec pouvoir, Opérateur d'impression ou Opérateur de serveur pour le compte de service VMM sur l'ordinateur distant sur lequel le partage réside.
* Si vous utilisez des comptes d'identification quelconques pour ajouter des hôtes, sur les chemins d'accès d'importation et d'exportation, attribuez des autorisations de lecture et d'écriture aux comptes d'identification dans VMM.
* Les partages d'importation et d'exportation ne doivent pas se trouver sur un ordinateur utilisé comme serveur hôte Hyper-V, car la configuration en boucle n'est pas prise en charge par Hyper-V.
* Dans Active Directory, sur chaque serveur hôte Hyper-V qui contient des machines virtuelles à protéger, activez et configurez la délégation de contrainte pour approuver les ordinateurs distants sur lesquels se trouvent les chemins d'accès d'importation et d'exportation, comme suit :
  1. Sur le contrôleur de domaine, ouvrez **Utilisateurs et ordinateurs Active Directory**.
  2. Dans l’arborescence de la console, cliquez sur **DomainName** > **Ordinateurs**.
  3. Cliquez avec le bouton droit sur le nom du serveur hôte Hyper-V > **Propriétés**.
  4. Sous l’onglet **Délégation**, cliquez sur **N’approuver cet ordinateur que pour la délégation aux services spécifiés**.
  5. Cliquez sur **Utiliser tout protocole d’authentification**.
  6. Cliquez sur **Ajouter** > **Utilisateurs et ordinateurs**.
  7. Saisissez le nom de l'ordinateur qui héberge le chemin d'accès d'exportation > **OK**. Dans la liste des services disponibles, maintenez la touche CTRL enfoncée et cliquez sur **cifs** > **OK**. Répétez ces étapes pour le nom de l'ordinateur qui héberge le chemin d'accès d'importation. Répétez cette procédure pour les serveurs hôtes Hyper-V supplémentaires.



## <a name="next-steps"></a>Étapes suivantes

Aller à [l’étape 9 : activer la réplication](vmm-to-vmm-walkthrough-enable-replication.md).
