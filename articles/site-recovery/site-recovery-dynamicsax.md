---
title: "Répliquer un déploiement Dynamics AX multiniveau à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Cet article explique comment répliquer et protéger Dynamics AX à l’aide d’Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: asgang
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: cf568d20f60709dbb64774bcbcc1b4aa6c43d8d3
ms.contentlocale: fr-fr
ms.lasthandoff: 06/16/2017


---
# <a name="replicate-a-multi-tier-dynamics-ax-application-using-azure-site-recovery"></a>Répliquer une application Dynamics AX multiniveau à l’aide d’Azure Site Recovery

## <a name="overview"></a>Vue d'ensemble


Microsoft Dynamics AX est une des solutions ERP les plus populaires parmi les entreprises pour standardiser le processus dans tous les emplacements, gérer les ressources et simplifier la conformité. Étant donné que cette application est essentielle pour une entreprise, il est très important de s’assurer qu’en cas d’incident, l’application est opérationnelle en un minimum de temps.

Aujourd'hui, Microsoft Dynamics AX ne fournit aucune capacité de récupération d’urgence prête à l’emploi. Microsoft Dynamics AX comprend de nombreux composants de serveur comme Application Object Server, Active Directory (AD), SQL Database Server, SharePoint Server, Reporting Server, etc. La gestion manuelle de la récupération d’urgence de chacun de ces composants est non seulement coûteuse, mais elle est également sujette aux erreurs.

Cet article explique en détail comment créer une solution de récupération d’urgence pour votre application Dynamics AX à l’aide d’[Azure Site Recovery](site-recovery-overview.md). Sont également couverts les basculements planifiés/non planifiés/de test à l’aide d’un plan de récupération en un seul clic, les configurations prises en charge et les conditions requises préalables.
La solution de récupération d’urgence basée sur Azure Site Recovery est entièrement testée, certifiée et recommandée par Microsoft Dynamics AX.



## <a name="prerequisites"></a>Prérequis

L’implémentation de la récupération d’urgence pour l’application Dynamics AX à l’aide d’Azure Site Recovery nécessite de remplir les conditions requises préalables suivantes.

•    Un déploiement local de Dynamics AX a été configuré

•    Un coffre Azure Site Recovery Services a été créé dans un abonnement Microsoft Azure

•    Si Azure est votre site de récupération d’urgence, exécutez l’outil d’évaluation de la disponibilité des machines virtuelles Azure sur des machines virtuelles afin de vérifier qu’elles sont compatibles avec les machines virtuelles Azure et Azure Site Recovery Services


## <a name="site-recovery-support"></a>Prise en charge de Site Recovery

Pour les besoins de création de cet article, des machines virtuelles VMware avec Dynamics AX 2012 R3 sur Windows Server 2012 R2 Enterprise ont été utilisées. Comme la réplication Site Recovery est indépendante des applications, les recommandations indiquées ici sont censées également s’appliquer aux scénarios suivants.

### <a name="source-and-target"></a>Source et cible

**Scénario** | **Vers un site secondaire** | **Vers Azure**
--- | --- | ---
**Hyper-V** | Oui | Oui
**VMware** | Oui | Oui
**Serveur physique** | Oui | Oui

## <a name="enable-dr-of-dynamics-ax-application-using-asr"></a>Activer la récupération d’urgence de l’application Dynamics AX à l’aide de la récupération automatique du système
### <a name="protect-your-dynamics-ax-application"></a>Protéger votre application Dynamics AX
Chaque composant de Dynamics AX doit être protégé pour activer la réplication et la récupération de l’application complète. Cette section couvre les points suivants :

**1. Protection d’Active Directory**

**2. Protection du niveau SQL**

**3. Protection des niveaux application et web**

**4. Configuration de la mise en réseau**

**5. Plan de récupération**

### <a name="1-setup-ad-and-dns-replication"></a>1. Configurer la réplication Active Directory et DNS

Active Directory est requis sur le site de récupération d’urgence pour que l’application Dynamics AX fonctionne. En fonction de la complexité de l’environnement local du client, deux choix vous sont recommandés.

**Option 1 :**

Si le client dispose d’un nombre d’applications limité et d’un seul contrôleur de domaine pour l’ensemble de son site local, et qu’il souhaite basculer l’intégralité du site, nous recommandons l’utilisation de la réplication ASR pour répliquer l’ordinateur contrôleur de domaine sur un site secondaire (applicable à la fois pour les scénarios de site à site et de site à Azure).

**Option 2 :**

Si le client possède un grand nombre d’applications, qu’il exécute une forêt Active Directory et qu’il souhaite basculer quelques applications à la fois, nous recommandons de configurer un contrôleur de domaine supplémentaire sur le site de récupération d’urgence (sur un site secondaire ou dans Azure).

Reportez-vous au [guide d’accompagnement sur la mise à disposition d’un contrôleur de domaine sur le site de récupération d’urgence](site-recovery-active-directory.md). Pour le reste de ce document, nous supposons qu'un contrôleur de domaine est disponible sur le site de récupération d’urgence.

### <a name="2-setup-sql-server-replication"></a>2. Configurer la réplication SQL Server
Reportez-vous au guide d’accompagnement pour des conseils techniques détaillés sur l’option recommandée pour la protection du [niveau SQL](site-recovery-sql.md).

### <a name="3-enable-protection-for-dynamics-ax-client-and-aos-vms"></a>3. Activer la protection des machines virtuelles AOS et du client Dynamics AX
Effectuez la configuration Azure Site Recovery appropriée selon que les machines virtuelles sont déployées sur [Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou sur [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Pour la fréquence cohérente en cas d’incident, le réglage recommandé est de 15 minutes.
>

La capture instantanée ci-après montre l’état de la protection des machines virtuelles des composants Dynamics dans le scénario de protection « Site VMware vers Azure ».
![Éléments protégés ](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Configurer la mise en réseau
Configurer les paramètres Calcul et réseau des machines virtuelles

Pour le client AX et les machines virtuelles AOS, configurez les paramètres réseau dans l’ASR afin que les réseaux des machines virtuelles soient associés au bon serveur de récupération d’urgence après le basculement. Vérifiez que le réseau de récupération d’urgence pour ces niveaux est routable vers le niveau SQL.

Vous pouvez sélectionner la machine virtuelle dans les éléments répliqués afin de configurer les paramètres réseau comme indiqué dans la capture instantanée ci-dessous.

* Pour les serveurs AOS, sélectionnez le bon groupe à haute disponibilité.

* Si vous utilisez une adresse IP statique, spécifiez l’adresse IP que vous souhaitez attribuer à la machine virtuelle dans le champ **Adresse IP cible** des ![paramètres réseau ](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)



### <a name="5-creating-a-recovery-plan"></a>5. Création d’un plan de récupération

Vous pouvez créer un plan de récupération dans ASR pour automatiser le processus de basculement. Ajoutez un niveau application et un niveau web dans le plan de récupération. Organisez-les en différents groupes afin que les frontales s’arrêtent avant celles de niveau application.

1)  Sélectionnez le coffre ASR dans votre abonnement, puis cliquez sur la vignette « Plans de récupération ».

2)  Cliquez sur « + Plan de récupération » et spécifiez un nom.

3)  Sélectionnez la « Source » et la « Cible ». La cible peut être Azure ou un site secondaire. Si vous choisissez Azure, vous devez spécifier le modèle de déploiement

![Créer un plan de récupération](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4)  Sélectionnez les machines virtuelles AOS et machines virtuelles du client pour le plan de récupération puis cliquez sur ✓.
![Créer un plan de récupération](./media/site-recovery-dynamics-ax/selectvms.png)


![Plan de récupération](./media/site-recovery-dynamics-ax/recoveryplan.png)

Vous pouvez personnaliser le plan de récupération pour l’application Dynamics AX en ajoutant plusieurs étapes détaillées ci-dessous. La capture instantanée ci-dessus montre l’intégralité du plan de récupération après avoir ajouté toutes les étapes.

*Étapes :*

*1. Étapes du basculement SQL Server*

Reportez-vous au guide d’accompagnement [« Solution de récupération d’urgence de SQL Server »](site-recovery-sql.md) pour plus d’informations sur les étapes de récupération spécifiques à SQL Server.

*2. Groupe de basculement 1 : basculement des machines virtuelles AOS*

Assurez-vous que le point de récupération sélectionné est aussi proche que possible du PIT de la base de données mais pas antérieur à lui.

*3. Script : ajouter un équilibrage de charge (E-A uniquement)* Ajouter un script (via Azure Automation) après l’affichage du groupe de machines virtuelles AOS pour lui ajouter un équilibrage de charge. Vous pouvez utiliser un script pour effectuer cette tâche. Consultez l’article [Comment ajouter un équilibrage de charge pour la récupération d’urgence d’application multiniveau](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/)

*4. Groupe de basculement 2 : basculement des machines virtuelles du client AX.*
Basculez les machines virtuelles de niveau web dans le cadre du plan de récupération.


### <a name="doing-a-test-failover"></a>Exécution d’un test de basculement

Reportez-vous aux guides d’accompagnement « Solution de récupération d’urgence d’Active Directory » et « Solution de récupération d’urgence de SQL Server » pour obtenir des considérations spécifiques à Active Directory et SQL Server durant le test de basculement.

1.  Accédez au portail Azure et sélectionnez votre coffre Site Recovery.
2.  Cliquez sur le plan de récupération créé pour Dynamics AX.
3.  Cliquez sur « Test de basculement ».
4.  Sélectionnez le réseau virtuel pour démarrer le processus de test de basculement.
5.  Lorsque l’environnement secondaire est opérationnel, vous pouvez effectuer vos validations.
6.  Une fois les validations terminées, vous pouvez sélectionner « Validations terminées ». L’environnement de test de basculement est alors nettoyé.

Suivez [ce guide](site-recovery-test-failover-to-azure.md) pour effectuer un test de basculement.

### <a name="doing-a-failover"></a>Exécution d’un basculement

1.  Accédez au portail Azure et sélectionnez votre coffre Site Recovery.
2.  Cliquez sur le plan de récupération créé pour Dynamics AX.
3.  Cliquez sur « Basculement » et sélectionnez « Basculement ».
4.  Sélectionnez le réseau cible et cliquez sur ✓ pour démarrer le processus de basculement.

Suivez [ce guide](site-recovery-failover.md) lorsque vous effectuez un basculement.

### <a name="perform-a-failback"></a>Effectuer une restauration automatique

Reportez-vous au guide d’accompagnement « Solution de récupération d’urgence de SQL Server » pour obtenir des considérations spécifiques à SQL Server lors de la restauration automatique.

1.  Accédez au portail Azure et sélectionnez votre coffre Site Recovery.
2.  Cliquez sur le plan de récupération créé pour Dynamics AX.
3.  Cliquez sur « Basculement » et sélectionnez le basculement.
4.  Cliquez sur « Changer de direction ».
5.  Sélectionnez les options de synchronisation de données et de création de machines virtuelles appropriées
6.  Cliquez sur ✓ pour démarrer le processus de « restauration automatique ».


Suivez [ce guide](site-recovery-failback-azure-to-vmware.md) lorsque vous effectuez une restauration automatique.

##<a name="summary"></a>Résumé
À l’aide d’Azure Site Recovery, vous pouvez créer un plan de récupération d’urgence automatisée complet pour votre application Dynamics AX. Vous pouvez lancer le basculement en quelques secondes, où que vous soyez, et bénéficier d’une application opérationnelle en quelques minutes.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la protection des charges de travail d’entreprise avec Azure Site Recovery, consultez [Quelles charges de travail pouvez-vous protéger avec Azure Site Recovery ?](site-recovery-workload.md).

