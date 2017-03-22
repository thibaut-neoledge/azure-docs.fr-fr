---
title: Meilleures pratiques Azure Site Recovery | Microsoft Docs
description: "Cet article décrit les meilleures pratiques pour le déploiement d’Azure Site Recovery"
services: site-recovery
documentationCenter: 
author: rayne-wiselman"
manager: cfreeman
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: site-recovery-support-matrix-to-azure
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: a53e2a09fb2aabe96dd5347ea7509815d92bfdb8
ms.lasthandoff: 03/15/2017

---

# <a name="get-ready-to-deploy-azure-site-recovery"></a>Préparez-vous au déploiement d’Azure Site Recovery

Cet article explique comment préparer le déploiement d’Azure Site Recovery.

## <a name="protecting-hyper-v-virtual-machines"></a>Protection des machines virtuelles Hyper-V

Pour protéger les machines virtuelles Hyper-V, vous avez le choix entre deux options de déploiement. Vous pouvez répliquer les machines virtuelles Hyper-V locales dans Azure ou sur un centre de données secondaire. Chaque déploiement a ses exigences propres.

**Prérequis** | **Réplication dans Azure (avec VMM)** | **Réplication de machines virtuelles Hyper-V dans Azure (sans VMM)** | **Réplication de machines virtuelles Hyper-V sur un site secondaire (avec VMM)** | **Détails**
---|---|---|---|---
**VMM** | VMM exécuté sur System Center 2012 R2 <br/><br/>Au moins un cloud VMM contenant un ou plusieurs groupes d’hôtes VMM. | N/D | Serveurs VMM sur les sites principal et secondaire s’exécutant au moins sur System Center 2012 SP1 avec les dernières mises à jour. <br/><br/> Au moins un cloud sur chaque serveur VMM. Le profil de capacité Hyper-V doit être défini sur les clouds.<br/><br/> Le cloud source doit avoir au moins un groupe d’hôtes VMM. | facultatif. Vous n’avez pas besoin de déployer System Center VMM pour répliquer des machines virtuelles Hyper-V dans Azure. En revanche, si vous le faites, vérifiez que le serveur VMM est correctement configuré. Vérifiez notamment que vous exécutez la bonne version de VMM et que les clouds sont configurés.
**Hyper-V** | Au moins un serveur hôte Hyper-V sur le site local exécutant Windows Server 2012 R2 ou une version ultérieure | Au moins un serveur Hyper-V sur les sites source et cible exécutant au minimum Windows Server 2012 avec les dernières mises à jour installées, et connecté à Internet.<br/><br/> Les serveurs Hyper-V doivent se trouver dans un groupe hôte d’un cloud VMM. | Au moins un serveur Hyper-V sur les sites source et cible exécutant au minimum Windows Server 2012 avec les dernières mises à jour installées, et connecté à Internet.<br/><br/> Les serveurs Hyper-V doivent se trouver dans un groupe hôte d’un cloud VMM. |
**Machines virtuelles** | Au moins une machine virtuelle sur le serveur hôte Hyper-V source. | Au moins une machine virtuelle sur le serveur hôte Hyper-V dans le cloud VMM source. | Au moins une machine virtuelle sur le serveur hôte Hyper-V dans le cloud VMM source. |  Les machines virtuelles répliquées sur Azure doivent remplir les conditions préalables applicables aux machines virtuelles Azure
**Compte Azure** | Vous aurez besoin d’un compte Azure et d’un abonnement au service Site Recovery. | Vous aurez besoin d’un compte Azure et d’un abonnement au service Site Recovery. | N/D | Si vous n’avez pas de compte, commencez avec un essai gratuit.
**Stockage Azure** | Vous aurez besoin d’un abonnement à un compte de stockage Azure pour lequel la géoréplication est activée. | Vous aurez besoin d’un abonnement à un compte de stockage Azure pour lequel la géoréplication est activée. | N/D | Le compte doit se trouver dans la même région que le coffre Azure Site Recovery et être associé au même abonnement.
**Mise en réseau** | Configurez le mappage réseau pour que toutes les machines virtuelles dont le basculement s’effectue sur le même réseau Azure puissent se connecter les unes aux autres, indépendamment du plan de récupération auquel elles sont intégrées. Par ailleurs, si une passerelle réseau est configurée sur le réseau cible Azure, les machines virtuelles peuvent se connecter aux autres machines virtuelles locales. Si vous ne définissez pas le mappage réseau, seules les machines basculant dans le même plan de récupération peuvent se connecter. | N/D |  <br/><br/>Configurez le mappage réseau pour vous assurer que les machines virtuelles sont connectées aux bons réseaux après le basculement et que les machines virtuelles de réplica sont placées de manière optimale sur les serveurs hôtes Hyper-V. Si vous ne configurez pas le mappage réseau, les machines répliquées ne seront connectées à aucun réseau de machines virtuelles après le basculement. |  Pour configurer le mappage réseau avec VMM, vous devez vérifier que le réseau logique VMM et le réseau de machines virtuelles sont correctement configurés.
**Fournisseurs et agents** | Pendant le déploiement, vous devez installer le fournisseur Azure Site Recovery sur les serveurs VMM. Sur les serveurs Hyper-V des clouds VMM, vous devez installer l’agent Azure Recovery Services. | Pendant le déploiement, vous devez installer le fournisseur Azure Site Recovery et l’agent Azure Recovery Services sur le serveur hôte ou le cluster Hyper-V.| Pendant le déploiement, vous devez installer le fournisseur Azure Site Recovery sur les serveurs VMM. Sur les serveurs Hyper-V des clouds VMM, vous devez installer l’agent Azure Recovery Services. | Fournisseur et agents se connectent à Site Recovery via Internet en utilisant une connexion HTTPS chiffrée. Il n’est pas nécessaire d’ajouter d’exceptions de pare-feu ni de créer de proxy spécifique pour la connexion Fournisseur.
**Connectivité Internet** | Seuls les serveurs VMM ont besoin d’une connexion Internet | Seuls les serveurs hôtes Hyper-V ont besoin d’une connexion Internet | Seuls les serveurs VMM ont besoin d’une connexion Internet | Les machines virtuelles n’ont besoin d’aucun composant en particulier et ne se connectent pas directement à Internet.



## <a name="protect-vmware-virtual-machines-or-physical-servers"></a>Protéger des machines virtuelles VMware ou des serveurs physiques

Pour protéger des machines virtuelles VMware ou des serveurs physiques Windows/Linux, deux options de déploiement s’offrent à vous. Vous pouvez les répliquer sur Azure ou dans un centre de données secondaire. Chaque déploiement a ses exigences propres.

**Prérequis** | **Répliquer des machines virtuelles WMware/serveurs physiques sur Azure** | * **Répliquer des machines virtuelles VMware/serveurs physiques sur un site secondaire**  
---|---|---
**Site principal** | **Serveur de processus** : serveur Windows dédié (physique ou virtuel) | **Serveur de processus** : serveur Windows dédié (ordinateur physique ou machine virtuelle VMware)<br/><br/>  
**Site local secondaire** | N/D | **Serveur de configuration** : serveur Windows dédié (physique ou virtuel) <br/><br/> **Serveur cible maître** : serveur dédié (physique ou virtuel). Configurez avec Windows pour protéger les ordinateurs Windows ou avec Linux pour protéger Linux.
**Microsoft Azure** | **Abonnement** : vous avez besoin d’un abonnement au service Site Recovery. <br/><br/> **Compte de stockage** : vous avez besoin d’un compte de stockage pour lequel la géoréplication est appliquée. Le compte doit se trouver dans la même région que le coffre Site Recovery et être associé au même abonnement. <br/><br/> **Serveur de configuration** : vous devez configurer le serveur de configuration en tant que machine virtuelle Azure <br/><br/> **Serveur cible maître** : vous devez configurer le serveur cible maître en tant que machine virtuelle Azure <br/><br/> Configurez avec Windows pour protéger les ordinateurs Windows ou avec Linux pour protéger Linux.<br/><br/> **Réseau virtuel Azure** : vous avez besoin d’un réseau virtuel Azure sur lequel le serveur de configuration et le serveur cible maître seront déployés. Il doit être dans le même abonnement et la même région que le coffre Azure Site Recovery | N/D  
**Machines virtuelles/serveurs physiques** | Au moins une machine virtuelle VMware ou un serveur Windows/Linux physique.<br/><br/>Pendant le déploiement, le service Mobilité est installé sur chaque machine| Au moins une machine virtuelle VMware ou un serveur physique Linux/Windows.<br/><br/> Pendant le déploiement, l’Agent unifié est installé sur chaque machine.




## <a name="azure-virtual-machine-requirements"></a>Configuration requise des machines virtuelles Azure

Vous pouvez déployer Site Recovery pour répliquer des machines virtuelles et des serveurs physiques exécutant n’importe quel système d’exploitation pris en charge par Azure. La plupart des versions de Windows et Linux sont concernées. Vous devez vérifier que les machines virtuelles locales à protéger répondent à la configuration requise d’Azure.


## <a name="optimizing-your-deployment"></a>Optimisation de votre déploiement

Pour optimiser et mettre à l’échelle votre déploiement, tenez compte des conseils suivants.

- **Taille du volume du système d’exploitation**: quand vous répliquez une machine virtuelle dans Azure, la taille du volume du système d’exploitation doit être inférieure à 1 To. Si vous disposez de volumes supérieurs, vous pouvez les déplacer manuellement vers un disque différent avant de démarrer le déploiement.
- **Taille du disque de données**: si vous répliquez vers Microsoft Azure, vous pouvez posséder jusqu’à 32 disques de données sur une machine virtuelle, chacun présentant une taille maximale d’1 To. Vous pouvez répliquer et basculer efficacement une machine virtuelle présentant une taille d’environ&32; To.
- **Limites de plan de récupération**: Scan Recovery peut prendre en charge des milliers de machines virtuelles. Les plans de récupération sont conçus comme un modèle dédié aux applications devant basculer simultanément, de manière à ce que le nombre de machines du plan soit limité à 50.
- **Limites des services Microsoft Azure** : chaque abonnement Microsoft Azure comporte un ensemble de limites par défaut liées aux services principaux, cloud, etc. Pour valider la disponibilité des ressources de votre abonnement, nous vous recommandons d’exécuter un basculement test. Vous pouvez modifier ces limites via le support Microsoft Azure.
- **Planification de la capacité** : planifiez l’évolutivité et les performances.
- **Bande passante de réplication**: si vous disposez d’une bande passante de réplication insuffisante, notez les points suivants :
    - **ExpressRoute**: Site Recovery s’associe à des optimiseurs Microsoft Azure ExpressRoute et WAN, tels que Riverbed.
    - **Trafic de réplication**: Site Recovery exécute une réplication initiale intelligente uniquement à l’aide de blocs de données, pas de l’intégralité du disque dur virtuel. Seules les modifications sont répliquées au cours de la réplication en continu.
    - **Trafic réseau** : pour contrôler le trafic réseau utilisé pour la réplication, configurez le réseau de Qualité de service (QoS) Windows à l’aide d’une stratégie basée sur l’adresse IP et le port de destination.  En plus si vous répliquez vers Microsoft Azure Site Recovery à l’aide de l’agent de sauvegarde Microsoft Azure. Vous pouvez configurer la limitation de cet agent.
- **RTO** : si vous souhaitez mesurer l’objectif de délai de récupération offert par Site Recovery, nous vous recommandons d’exécuter un basculement test et d’examiner les travaux Site Recovery afin d’analyser le délai d’exécution des opérations. Si vous effectuez un basculement vers Microsoft Azure, vous avez tout intérêt à automatiser l’ensemble des actions manuelles en intégrant avec Microsoft Azure Automation et les plans de récupération.
- **RPO**: Site Recovery prend en charge un objectif de point de récupération quasi synchrone lorsque vous répliquez vers Microsoft Azure. Ceci est valable uniquement si vous disposez d’une bande passante suffisante entre votre centre de données et Azure.

