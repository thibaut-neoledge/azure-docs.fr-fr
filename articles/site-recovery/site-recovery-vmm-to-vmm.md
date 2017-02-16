---
title: "Répliquer des machines virtuelles Hyper-V dans VMM sur un site secondaire avec Azure Site Recovery | Microsoft Docs"
description: "Explique comment déployer Azure Site Recovery pour orchestrer la réplication, le basculement et la récupération des machines virtuelles Hyper-V dans des clouds VMM vers un site VMM secondaire avec le portail Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: b33a1922-aed6-4916-9209-0e257620fded
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 32c6e6d5f18dd0ec105b7ba66bb2ff1aed10558a
ms.openlocfilehash: b3ac842794665030a7c99383b221a5e61282dd81


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>Répliquer des machines virtuelles Hyper-V dans des clouds VMM sur un site VMM secondaire avec le portail Azure
> [!div class="op_single_selector"]
> * [Portail Azure](site-recovery-vmm-to-vmm.md)
> * [Portail classique](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

Bienvenue dans le service Azure Site Recovery.

Site Recovery est un service Azure qui participe à votre stratégie de continuité des activités et de récupération d’urgence. Il orchestre la réplication des machines virtuelles et des serveurs physiques locaux vers le cloud (Azure) ou un centre de données secondaire. Lorsque des pannes se produisent sur votre site principal, vous effectuez un basculement sur le site secondaire pour préserver la disponibilité des applications et des charges de travail. Vous restaurez votre site principal dès lors qu’il retrouve un fonctionnement normal. Pour en savoir plus, voir [Qu’est-ce que Site Recovery ?](site-recovery-overview.md)

Cet article décrit comment utiliser Site Recovery dans le portail Azure pour répliquer des machines virtuelles Hyper-V locales gérées sur les clouds System Center Virtual Machines Manager (VMM) dans un site secondaire.

Après avoir lu cet article, n’hésitez pas à publier un commentaire en bas de la section des commentaires de Disqus. Publiez vos questions techniques sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="quick-summary"></a>Résumé
Pour un déploiement complet, nous vous recommandons vivement de suivre les étapes de l’article. Si vous ne disposez pas de suffisamment de temps, voici un résumé rapide.

| **Zone** | **Détails** |
| --- | --- |
| **Scénario de déploiement** |Répliquer des ordinateurs virtuels Hyper-V sur des hôtes Hyper-V entre des clouds VMM|
| **Configuration requise en local** | Serveurs VMM locaux exécutant au moins System Center 2012 SP1 avec les dernières mises à jour.<br/><br/> Nous vous recommandons un serveur VMM sur chaque site, mais vous pouvez répliquer entre des clouds sur le même serveur VMM. [En savoir plus](site-recovery-single-vmm.md)<br/><br/> Le serveur VMM a un ou plusieurs clouds contenant des hôtes Hyper-V.<br/><br/> Serveurs Hyper-V exécutant au minimum Windows Server 2012 avec les dernières mises à jour.<br/><br/> Les serveurs VMM et l’hôte Hyper-V nécessite un accès internet.<br/><br/> [URL nécessaires](#on-premises-prerequisites) (directement ou par le biais d’un proxy)  |
| **Limitations en local** | Les proxies basés sur HTTPS ne sont pas pris en charge.
| **Conditions requises pour Azure** | Compte Azure<br/><br/> Coffre Recovery Services |
| **Réplication de machines virtuelles** | Toute machine virtuelle prise en charge par Hyper-V |
| **Agents** | Fournisseur Azure Site Recovery installés sur des serveurs VMM<br/><br/> Aucun agent nécessaire sur les hôtes Hyper-V |
| **Étapes du déploiement** | **1)** Préparez le serveur VMM local -> **2)** Créez le coffre Recovery Services -> **3)** Configurez le serveur VMM et enregistrez-le dans le coffre -> **4)** Configurez les paramètres de réplication -> **5)** Configurez le mappage de réseau -> **6)** Activez la réplication -> **7)** Testez la réplication et le basculement.


## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery dans le portail Azure

Azure dispose de deux [modèles de déploiement](../azure-resource-manager/resource-manager-deployment-model.md) différents pour créer et utiliser des ressources : le déploiement Azure Resource Manager et le déploiement classique. Azure propose également deux portails : le portail Azure Classic et le portail Azure.

Cet article explique comment effectuer un déploiement dans le portail Azure, qui offre une expérience de déploiement optimisée. Le portail classique peut être utilisé pour gérer les coffres existants. Il est impossible de créer des coffres à l’aide du portail classique.


## <a name="site-recovery-in-your-business"></a>Site Recovery dans votre entreprise
Les organisations ont besoin d’une stratégie BCDR qui détermine la façon dont les applications et les données demeurent opérationnelles et disponibles pendant les temps d’arrêt prévus et imprévus, et qui précise comment rétablir au plus vite des conditions de travail normales. Voici ce que Site Recovery peut faire :

- fournir une protection hors site pour les charges de travail professionnelles s’exécutant sur des machines virtuelles Hyper-V ;
- fournir un emplacement unique pour configurer, gérer et surveiller la réplication, le basculement et la récupération ;
- fournir un basculement et une restauration simples sur des sites locaux ;
- fournir une réplication et basculement pour plusieurs machines virtuelles.  Vous pouvez rassembler plusieurs machines dans des plans de récupération afin que les charges de travail d’applications hiérarchisées sur plusieurs machines basculent ensemble.

## <a name="scenario-architecture"></a>Architecture du scénario

* **Site principal** : dans le site principal, vous avez besoin d’un ou de plusieurs serveurs VMM. Ces serveurs ont des clouds privés VMM contenant des serveurs hôtes Hyper-V qui ont des machines virtuelles que vous souhaitez répliquer.
* **Site secondaire** : dans le site secondaire, vous avez besoin d’un ou de plusieurs serveurs VMM. Les serveurs hôtes Hyper-V dans des clouds VMM exécutent des machines virtuelles cibles vers lesquelles vous répliquez les machines virtuelles principales.
* **Fournisseur** : lors du déploiement de Site Recovery, vous installez le fournisseur Azure Site Recovery sur des serveurs VMM et inscrivez ceux-ci dans un coffre Recovery Services. Le fournisseur communique avec Site Recovery par le biais de HTTPS 443 pour répliquer l’orchestration. La réplication des données se produit entre les serveurs hôtes Hyper-V principaux et secondaires. Les données répliquées restent au sein des réseaux et sites locaux et ne sont pas envoyées à Azure. En savoir plus sur la [confidentialité](#privacy-information-for-site-recovery).

![Topologie E2E](./media/site-recovery-vmm-to-vmm/architecture.png)

### <a name="data-privacy-overview"></a>Présentation de la confidentialité des données

Le tableau suivant résume la façon dont les données sont stockées dans ce scénario :

- - -
| Action | **Détails** | **Données collectées** | **Utilisation** | **Obligatoire** |
| --- | --- | --- | --- | --- |
| **Inscription** | Vous inscrivez un serveur VMM dans un coffre Recovery Services. Si, par la suite, vous souhaitez annuler l’inscription d’un serveur, vous pouvez le faire en supprimant les informations du serveur sur le portail Azure. | Une fois un serveur VMM inscrit, Site Recovery collecte, traite et transfère les métadonnées du serveur VMM et les noms des clouds VMM détectés par Site Recovery. | Les données sont utilisées pour identifier et communiquer avec le serveur VMM approprié et configurer les paramètres des clouds VMM appropriés. | Cette fonctionnalité est requise. Si vous ne souhaitez pas envoyer ces informations à Site Recovery, n’utilisez pas le service Site Recovery. |
| **Activer la réplication** | Le fournisseur Azure Site Recovery est installé sur le serveur VMM et est l’intermédiaire qui permet de communiquer avec le service Site Recovery. Le fournisseur est une bibliothèque de liens dynamiques (DLL) hébergée dans le processus VMM. Une fois que le fournisseur est installé, la fonctionnalité « Récupération de centre de données » est activée dans la console Administrateur VMM. Les machines virtuelles actuelles et nouvelles peuvent activer cette fonctionnalité pour assurer la protection d’une machine virtuelle. |Cette propriété étant définie, le fournisseur envoie le nom et l’ID de la machine virtuelle à Site Recovery.  La réplication est activée par le réplica Hyper-V de Windows Server 2012 ou Windows Server 2012 R2. Les données des machines virtuelles sont répliquées d'un hôte Hyper-V vers un autre (généralement situé dans un autre centre de données de « récupération »). |Site Recovery utilise les métadonnées pour renseigner les informations de la machine virtuelle dans le portail Azure. | Cette fonctionnalité joue un rôle essentiel dans le service et ne peut pas être désactivée. Si vous ne souhaitez pas envoyer ces informations, n’activez pas la protection des machines virtuelles Site Recovery. Notez que toutes les données que le fournisseur transmet à Site Recovery sont envoyées au moyen du protocole HTTPS. |
| **Plan de récupération** | Les plans de récupération vous permettent de créer un plan d’orchestration pour le centre de données de récupération. Vous pouvez définir l’ordre de démarrage des machines virtuelles ou d’un groupe de machines virtuelles sur le site de récupération. Pour chaque machine virtuelle, vous pouvez aussi spécifier les scripts automatisés à exécuter ou l’action manuelle à entreprendre au moment de la récupération. Le basculement est généralement déclenché au niveau du plan de récupération pour une récupération coordonnée. | Site Recovery recueille, traite et transmet les métadonnées pour le plan de récupération, notamment les métadonnées des machines virtuelles et les métadonnées des éventuels scripts d’automatisation et notes d’actions manuelles. |Les métadonnées sont utilisées pour générer le plan de récupération dans le portail Azure. |Cette fonctionnalité joue un rôle essentiel dans le service et ne peut pas être désactivée. Si vous ne souhaitez pas envoyer ces informations à Site Recovery, ne créez pas de plan de récupération. |
| **Mappage réseau** | Mappe les informations de réseau du centre de données principal vers le centre de données de récupération. Dès lors que les machines virtuelles sont récupérées sur le site de récupération, le mappage réseau permet d’établir une connectivité réseau. |Site Recovery collecte, traite et transmet les métadonnées des réseaux logiques pour chaque site (principal et centre de données). |Les métadonnées sont utilisées pour remplir les paramètres de réseau afin de vous permettre de mapper les informations réseau. | Cette fonctionnalité joue un rôle essentiel dans le service et ne peut pas être désactivée. Si vous ne souhaitez pas envoyer ces informations à Site Recovery, n’utilisez pas le mappage réseau. |
| **Basculement (planifié/non planifié/test)** | Le basculement bascule les machines virtuelles d’un centre de données géré par VMM à un autre. L’action de basculement est déclenchée manuellement dans le portail Azure. |Le fournisseur du serveur VMM est averti de l’événement de basculement par Site Recovery et exécute une action de basculement sur l’hôte Hyper-V au moyen des interfaces VMM. Le basculement réel d’une machine virtuelle s’effectue d’un hôte Hyper-V à un autre et est géré par un réplica Hyper-V Windows Server 2012 ou Windows Server 2012 R2. Site Recovery utilise les informations envoyées pour spécifier l’état des informations de l’action de basculement dans le portail Azure. | Cette fonctionnalité joue un rôle essentiel dans le service et ne peut pas être désactivée. Si vous ne souhaitez pas envoyer ces informations à Site Recovery, n’utilisez pas le basculement. |

## <a name="azure-prerequisites"></a>Conditions préalables pour Azure
Voici les éléments requis pour vous permettre de déployer ce scénario dans Azure :

| **Configuration requise** | **Détails** |
| --- | --- |
| **Microsoft Azure** |Vous avez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/) . Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery. |

## <a name="on-premises-prerequisites"></a>Conditions préalables locales
Voici ce que dont avez besoin sur les sites locaux principal et secondaire pour déployer ce scénario :

| **Configuration requise** | **Détails** |
| --- | --- |
| **VMM** | Nous vous recommandons de déployer un serveur VMM dans le site principal et un autre dans le site secondaire.<br/><br/> Vous pouvez [effectuer la réplication entre des clouds sur un seul serveur VMM](site-recovery-single-vmm.md). Pour ce faire, vous avez besoin d’au moins deux clouds configurés sur le serveur VMM.<br/><br/> Les serveurs VMM doivent exécuter au moins System Center 2012 SP1 avec les dernières mises à jour.<br/><br/> Chaque serveur VMM doit disposer d’un ou de plusieurs clouds. Le profil Capacité Hyper-V doit être défini pour tous les clouds. <br/><br/>Les clouds doivent contenir un ou plusieurs groupes hôtes VMM.<br/><br/> [En savoir plus](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) sur la configuration des clouds VMM.<br/><br/> Les serveurs VMM doivent avoir accès à Internet. |
| **Hyper-V** | Les serveurs Hyper-V doivent exécuter au moins Windows Server 2012 avec le rôle Hyper-V et les dernières mises à jour doivent être installées.<br/><br/> Un serveur Hyper-V doit contenir au moins une machine virtuelle.<br/><br/>  Les serveurs hôtes Hyper-V doivent être situés dans des groupes hôtes dans les clouds VMM principaux et secondaires.<br/><br/> Si vous exécutez Hyper-V dans un cluster Windows Server 2012 R2, vous devez installer la [mise à jour 2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Si vous exécutez Hyper-V dans un cluster sous Windows Server 2012, notez que le répartiteur de clusters n’est pas créé automatiquement si vous avez un cluster basé sur des adresses IP statiques. Vous devez configurer manuellement le service Broker de cluster. [En savoir plus](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **Fournisseur** | Pendant un déploiement de Site Recovery, vous installez le fournisseur Azure Site Recovery sur des serveurs VMM. Le fournisseur communique avec Site Recovery par le biais de HTTPS 443 pour orchestrer la réplication. La réplication des données a lieu entre les serveurs Hyper-V principaux et secondaires par le biais du réseau local ou d’une connexion VPN.<br/><br/> Le fournisseur s’exécutant sur le serveur VMM a besoin d’accéder aux URL suivantes :<br/><br/> ``*.accesscontrol.windows.net``<br/><br/> ``*.backup.windowsazure.com``<br/><br/> ``*.hypervrecoverymanager.windowsazure.com``<br/><br/>  ``*.store.core.windows.net``<br/><br/> ``*.blob.core.windows.net`` <br/><br/> ``https://www.msftncsi.com/ncsi.txt``<br/><br/> ``time.windows.com``<br/><br/> ``time.nist.gov``<br/><br/> Autorisez la communication de pare-feu des serveurs VMM vers les [plages IP de centre de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et autorisez le protocole HTTPS (443). |

## <a name="prepare-for-deployment"></a>Préparation du déploiement
Pour préparer un déploiement, vous devez :

1. [Préparez le serveur VMM](#prepare-the-vmm-server) au déploiement de Site Recovery.
2. [Préparez le mappage réseau](#prepare-for-network-mapping). Configurer des réseaux de façon à pouvoir définir le mappage réseau

### <a name="prepare-the-vmm-server"></a>Préparez le serveur VMM
Vérifiez que le serveur VMM respecte la [configuration requise](#on-premises-prerequisites) et peut accéder aux URL répertoriées.

### <a name="prepare-for-network-mapping"></a>Préparer le mappage réseau
Le mappage réseau effectue un mappage entre les réseaux de machines virtuelles VMM sur les serveurs VMM principaux et secondaires pour :

* Optimiser le positionnement des machines virtuelles de réplication sur les hôtes Hyper-V secondaires après le basculement.
* Connecter les machines virtuelles de réplication aux réseaux de machines virtuelles appropriés.
* Si vous ne configurez pas de mappage réseau, les machines virtuelles de réplicas ne sont connectées à aucun réseau après le basculement.
* Si vous voulez configurer le mappage réseau lors du déploiement de Site Recovery, vous avez besoin des éléments suivants :

  * Assurez-vous que l’ensemble des machines virtuelles du serveur hôte Hyper-V source sont connectées à un réseau de machines virtuelles VMM. Ce réseau doit être lié à un réseau logique lui-même associé au cloud.
  * Vérifiez que le cloud secondaire que vous utilisez pour la récupération est configuré avec un réseau de machines virtuelles correspondant. Ce réseau de machines virtuelles doit être lié à un réseau logique lui-même associé au cloud secondaire.
* [En savoir plus](site-recovery-network-mapping.md) sur le fonctionnement du mappage réseau.

## <a name="prepare-for-deployment-with-a-single-vmm-server"></a>Préparer le déploiement avec un seul serveur VMM

Si vous n’avez qu’un seul serveur VMM, vous pouvez répliquer des machines virtuelles dans des hôtes Hyper-V du cloud VMM vers [Azure](site-recovery-vmm-to-azure.md) ou vers un cloud VMM secondaire. Nous vous recommandons la première option, car la réplication entre clouds n’est pas fluide. Toutefois, si vous devez choisir la deuxième option, voici ce que vous devez faire :

1. **Configurez VMM sur une machine virtuelle Hyper-V**. Nous vous conseillons de stocker l’instance SQL Server utilisée par VMM sur la même machine virtuelle. De cette manière, vous pouvez gagner du temps, car une seule machine virtuelle doit être créée. Si vous souhaitez utiliser une instance distante de SQL Server, vous devez récupérer cette instance pour pouvoir récupérer VMM en cas de défaillance.
2. **Vérifiez que le serveur VMM a au moins deux clouds configurés**. Un cloud contient les machines virtuelles à répliquer et l’autre cloud sert d’emplacement secondaire. Le cloud qui contient les machines virtuelles que vous souhaitez protéger doit remplir les [conditions préalables](#on-premises-prerequisites).
3. Configurez Site Recovery comme le décrit cet article. Créez et inscrivez le serveur VMM dans le coffre, configurez une stratégie de réplication et activez la réplication. Vous devez spécifier que la réplication initiale s’effectue via le réseau.
4. Lorsque vous configurez le mappage réseau, vous mappez le réseau de machines virtuelles du cloud principal au réseau de machines virtuelles du cloud secondaire.
5. Dans la console Gestionnaire Hyper-V, activez la réplication Hyper-V sur l’hôte Hyper-V qui contient la machine virtuelle VMM et activez la réplication sur la machine virtuelle. Assurez-vous que vous n’ajoutez pas la machine virtuelle VMM à des clouds protégés par Site Recovery afin de garantir que les paramètres de réplication Hyper-V ne sont pas remplacés par Site Recovery.
6. Si vous créez des plans de récupération pour le basculement, vous utilisez le même serveur VMM source et cible.
7. Vous effectuez le basculement et la récupération comme suit :

   * Basculez manuellement la machine virtuelle VMM vers le site secondaire à l’aide du Gestionnaire Hyper-V avec un basculement planifié.
   * Basculer les machines virtuelles.
   * Une fois la machine virtuelle VMM principale récupérée, connectez-vous au portail Azure -> Coffre Recovery Services, puis et effectuez un basculement non planifié des machines virtuelles du site principal vers le site secondaire.
   * Une fois que le basculement non planifié est terminé, l’utilisateur peut à nouveau accéder à toutes les ressources sur le site principal.

### <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau** > **Gestion** > **Recovery Services**. Vous pouvez également sélectionner **Parcourir** > **Coffres **Recovery Services > **Ajouter**.

    ![Nouveau coffre](./media/site-recovery-vmm-to-vmm/new-vault3.png)
3. Dans **Nom**, indiquez un nom convivial permettant d’identifier le coffre. Si vous avez plusieurs abonnements, sélectionnez-en un.
4. [Créez un groupe de ressources](../azure-resource-manager/resource-group-template-deploy-portal.md) ou sélectionnez un groupe existant et spécifiez une région Azure. Les machines seront répliquées dans cette région. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Détails des prix d'Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)
5. Si vous souhaitez accéder rapidement au coffre à partir du tableau de bord, cliquez sur **Épingler au tableau de bord** > **Créer un archivage**.

    ![Nouveau coffre](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

Le nouveau coffre s’affiche dans la zone **Tableau de bord** > **Toutes les ressources**, et dans le panneau **Coffres Recovery Services** principal.

## <a name="getting-started"></a>Prise en main
Site Recovery propose une expérience de prise en main, qui vous permet d’effectuer le déploiement aussi rapidement que possible. Ainsi, la fonction Prise en main vérifie la configuration requise et vous guide à travers les étapes de déploiement de Site Recovery, dans l’ordre adéquat.

Grâce à cette fonction, vous pouvez sélectionner le type de machine que vous souhaitez répliquer, voire l’endroit auquel vous souhaitez effectuer la réplication. Vous pouvez configurer des serveurs locaux, créer des stratégies de réplication et effectuer la planification de la capacité. Une fois que vous avez configuré votre infrastructure, vous activez la réplication pour les machines virtuelles. Vous pouvez exécuter le basculement de machines spécifiques, ou créer des plans de récupération pour effectuer le basculement de plusieurs machines.

Lancez la fonction Prise en main en sélectionnant le mode de déploiement de Site Recovery. Le flux de la fonction Prise en main change légèrement selon vos exigences en matière de réplication.

## <a name="step-1-choose-your-protection-goal"></a>Étape 1 : choisir votre objectif en matière de protection
Sélectionnez les éléments à répliquer et l’emplacement de la réplication.

1. Dans **Coffres Recovery Services**, choisissez votre coffre et cliquez sur **Paramètres**.
2. Dans **Paramètres** > **Prise en main**, cliquez sur **Site Recovery** > **Étape 1 : Préparer l’infrastructure** > **Objectif de protection**.
3. Dans **Objectif de protection**, sélectionnez **Vers le site de récupération**, puis **Oui, avec Hyper-V**.
4. Sélectionnez **Oui** pour indiquer que vous utilisez VMM pour gérer les hôtes Hyper-V, puis sélectionnez **Oui** si vous avez un serveur VMM secondaire. Si vous déployez la réplication entre des clouds sur un seul serveur VMM, cliquez sur **Non**. Cliquez ensuite sur **OK**.

    ![Sélectionner des objectifs](./media/site-recovery-vmm-to-vmm/choose-goals.png)

## <a name="step-2-set-up-the-source-environment"></a>Étape 2 : configurer l’environnement source
Installez le fournisseur Azure Site Recovery sur les serveurs VMM et inscrivez ces derniers dans le coffre.

1. Cliquez sur **Étape 2 : Préparer l’infrastructure** > **Source**.

    ![Configurer la source](./media/site-recovery-vmm-to-vmm/goals-source.png)
2. Dans **Préparer la source**, cliquez sur **+ VMM** pour ajouter un serveur VMM.

    ![Configurer la source](./media/site-recovery-vmm-to-vmm/set-source1.png)
3. Dans **Ajouter un serveur**, vérifiez que **Serveur System Center VMM** s’affiche dans le champ **Type de serveur** et que le serveur VMM répond à la [configuration requise et aux exigences concernant les URL](#on-premises-prerequisites).
4. Téléchargez le fichier d’installation du fournisseur Azure Site Recovery.
5. Téléchargez la clé d’inscription. Vous en avez besoin pour exécuter le programme d’installation. Une fois générée, la clé est valide pendant 5 jours.

    ![Configurer la source](./media/site-recovery-vmm-to-vmm/set-source3.png)
6. Installez le fournisseur Azure Site Recovery sur le serveur VMM.

> [!NOTE]
> Vous n’avez besoin de rien installer explicitement sur les serveurs hôtes Hyper-V.
>
>

### <a name="set-up-the-azure-site-recovery-provider"></a>Configurer le fournisseur Azure Site Recovery
1. Exécutez le fichier d’installation du fournisseur sur chaque serveur VMM. Si VMM est déployé dans un cluster et que vous installez le fournisseur pour la première fois, installez-le sur un nœud actif et terminez l'installation pour inscrire le serveur VMM dans le coffre. Ensuite, installez le fournisseur sur les autres nœuds. Les nœuds de cluster doivent exécuter la même version du fournisseur.
2. Le programme d’installation exécute quelques vérifications préalables et demande l’autorisation d’arrêter le service VMM. Le service VMM redémarre automatiquement une fois l’installation terminée. Si vous installez sur un cluster VMM, vous êtes invité à arrêter le rôle de cluster.
3. Dans **Microsoft Update**, vous pouvez choisir des mises à jour, pour que celles du fournisseur soient installées conformément à votre stratégie Microsoft Update.
4. Dans le champ **Installation**, acceptez ou modifiez l’emplacement d’installation du fournisseur par défaut et cliquez sur **Installer**.

    ![Emplacement d’installation](./media/site-recovery-vmm-to-vmm/provider-location.png)
5. Une fois l’installation terminée, cliquez sur **Inscrire** pour inscrire le serveur dans le coffre.

    ![Emplacement d’installation](./media/site-recovery-vmm-to-vmm/provider-register.png)
6. Dans **Vault name**, vérifiez le nom du coffre dans lequel le serveur est enregistré. Cliquez sur *Next*.

    ![Enregistrement du serveur](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)
7. Dans **Connexion Internet**, indiquez la façon dont le fournisseur exécuté sur le serveur VMM se connecte à Internet. Sélectionnez **Se connecter avec des paramètres de proxy existants** pour utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.

    ![Paramètres Internet](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

   * Si vous souhaitez utiliser un proxy personnalisé, vous devez le configurer avant d'installer le fournisseur. Quand vous configurez les paramètres de proxy personnalisé, un test s'exécute pour vérifier la connexion proxy.
   * Si vous n’utilisez pas de proxy personnalisé ou si votre proxy par défaut nécessite une authentification, vous devez saisir les détails du proxy, dont son adresse et son port.
   - Vérifiez que les URL nécessaires sont accessibles à partir du serveur VMM et des hôtes Hyper-V.
   * Si vous utilisez un proxy personnalisé, un compte RunAs VMM (DRAProxyAccount) est créé automatiquement avec les informations d'identification du proxy spécifiées. Configurez le serveur proxy pour que ce compte puisse s'authentifier correctement. Vous pouvez modifier les paramètres du compte RunAs VMM dans la console VMM. Pour cela, ouvrez l'espace de travail **Paramètres**, développez **Sécurité**, cliquez sur **Comptes d'identification**, puis modifiez le mot de passe de DRAProxyAccount. Vous devez redémarrer le service VMM pour que ce paramètre prenne effet.
8. Dans **Registration Key**, sélectionnez la clé que vous avez téléchargée depuis Azure Site Recovery et copiez-la sur le serveur VMM.
9. Le paramètre de chiffrement est uniquement utilisé quand vous répliquez des machines virtuelles Hyper-V dans des clouds VMM sur Azure. Si vous répliquez sur un site secondaire, il n’est pas utilisé.
10. Dans **Server name**, entrez un nom convivial pour identifier le serveur VMM dans le coffre. Dans une configuration de cluster, spécifiez le nom de rôle de cluster VMM.
11. Dans **Synchroniser les métadonnées du cloud** , indiquez si vous voulez synchroniser les métadonnées de tous les clouds sur le serveur VMM à l’aide du coffre. Cette action se produit une seule fois sur chaque serveur. Si vous ne souhaitez pas synchroniser tous les clouds, vous pouvez désactiver ce paramètre et synchroniser individuellement chaque cloud dans les propriétés du cloud de la console VMM.
12. Cliquez sur **Suivant** pour terminer le processus. Une fois l'inscription terminée, les métadonnées du serveur VMM sont extraites par Azure Site Recovery. Le serveur apparaît sous l’onglet **Serveurs VMM** de la page **Serveurs** du coffre.

    ![Serveur](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)
13. Une fois le serveur disponible dans la console Site Recovery, dans **Source** > **Préparer la source** , sélectionnez le serveur VMM, puis sélectionnez le cloud dans lequel se trouve l’hôte Hyper-V. Cliquez ensuite sur **OK**.

#### <a name="command-line-installation"></a>Installation à partir de la ligne de commande
Le fournisseur Azure Site Recovery peut être installé à partir de la ligne de commande. Vous pouvez utiliser cette méthode pour installer le fournisseur sur un module Server Core pour Windows Server 2012 R2.

1. Téléchargez le fichier d’installation du fournisseur et la clé d’inscription dans un dossier, par exemple C:\ASR.
2. Arrêtez le service System Center Virtual Machine Manager.
3. À partir d’une invite de commandes avec élévation de privilèges, exécutez les commandes suivantes pour extraire le programme d’installation du fournisseur :

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. Exécutez cette commande pour installer le fournisseur :

        C:\ASR> setupdr.exe /i
5. Ensuite, exécutez les commandes suivantes pour inscrire le serveur dans le coffre :

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Où les paramètres sont :

* **/Credentials**: paramètre obligatoire, qui spécifie l’emplacement où se trouve le fichier de clé d’inscription.  
* **/FriendlyName**: paramètre obligatoire, qui correspond au nom du serveur hôte Hyper-V qui s’affiche sur le portail Microsoft Azure Site Recovery.
* **/EncryptionEnabled**: paramètre facultatif que vous utilisez uniquement lors de la réplication de VMM vers Azure.
* **/proxyAddress**: paramètre facultatif qui spécifie l’adresse du serveur proxy.
* **/proxyport**: paramètre facultatif qui spécifie le port du serveur proxy.
* **/proxyUsername**: paramètre facultatif qui spécifie le nom d’utilisateur proxy (si le proxy nécessite une authentification).
* **/proxyPassword**: paramètre facultatif qui spécifie le mot de passe pour l’authentification auprès du serveur proxy (si le proxy nécessite une authentification).  

## <a name="step-3-set-up-the-target-environment"></a>Étape 3 : configurer l’environnement cible
Sélectionnez le cloud et le serveur VMM cible.

1. Cliquez sur **Préparer l’infrastructure** > **Cible** et sélectionnez le serveur VMM cible à utiliser.
2. Les clouds du serveur qui sont synchronisés avec Site Recovery s’affichent. Sélectionnez le cloud cible.

   ![Cible](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="step-4-set-up-replication-settings"></a>Étape 4 : configurer les paramètres de réplication
1. Pour créer une stratégie de réplication, cliquez sur **Préparer l’infrastructure** > **Paramètres de réplication** > **+Créer et associer**.

    ![Réseau](./media/site-recovery-vmm-to-vmm/gs-replication.png)
2. Dans **Créer et associer une stratégie**, indiquez le nom de la stratégie. Le type source et cible doit être **Hyper-V**.
3. Dans **Version de l’hôte Hyper-V**, sélectionnez le système d’exploitation sous lequel fonctionne l’hôte.

   > [!NOTE]
   > Le cloud VMM peut contenir des hôtes Hyper-V sous différentes versions (prises en charge) de Windows Server, mais une stratégie de réplication s’applique aux hôtes sous le même système d’exploitation. Si vous avez des hôtes sous plusieurs versions de système d’exploitation, créez des stratégies de réplication distinctes.
   >
   >
4. Dans **Type d’authentification** et **Port d’authentification**, indiquez le mode d’authentification du trafic entre les serveurs hôtes Hyper-V principaux et de récupération. Sélectionnez **Certificat** , sauf si vous avez un environnement Kerberos opérationnel. Azure Site Recovery configurera automatiquement des certificats pour l'authentification HTTPS. Vous n’avez pas besoin de faire quoi que ce soit manuellement. Par défaut, les ports 8083 et 8084 (pour les certificats) seront ouverts dans le Pare-feu Windows sur les serveurs hôtes Hyper-V. Si vous sélectionnez **Kerberos**, un ticket Kerberos est utilisé pour l'authentification mutuelle des serveurs hôtes. Notez que ce paramètre n'est utile que pour les serveurs hôtes Hyper-V s'exécutant sur Windows Server 2012 R2.
5. Dans **Fréquence de copie**, spécifiez la fréquence selon laquelle répliquer les données delta après la réplication initiale (toutes les 30 secondes ou toutes les 5 ou 15 minutes).
6. Dans **Rétention des points de récupération**, spécifiez la durée de la fenêtre de rétention pour chaque point de récupération (en heures). Les machines protégées peuvent être récupérées à tout moment pendant cette fenêtre temporelle.
7. Dans **Fréquence des captures instantanées cohérentes de l’application**, spécifiez la fréquence de création des points de récupération contenant des instantanés cohérents au niveau des applications (entre&1; et&12; heures). Hyper-V utilise deux types d’instantanés : un instantané standard qui fournit un instantané incrémentiel de la machine virtuelle complète et un instantané cohérent avec l'application qui prend un instantané des données d'application d'une machine virtuelle. Les instantanés cohérents avec l'application utilisent le service VSS (Volume Shadow Copy Service) pour s'assurer que les applications sont dans un état cohérent lors de la prise des instantanés. Si vous activez les instantanés cohérents avec l'application, cela affecte les performances des applications exécutées sur les machines virtuelles sources. Assurez-vous que la valeur définie est inférieure au nombre de points de récupération supplémentaires que vous configurez.
8. Dans **Compression du transfert de données**, indiquez si les données répliquées transférées doivent être compressées.
9. Sélectionnez **Supprimer une machine virtuelle de réplica** pour spécifier que la machine virtuelle doit être supprimée si vous désactivez la protection de la machine virtuelle source. Si vous activez ce paramètre et que vous désactivez la protection de la machine virtuelle source, elle est supprimée de la console Site Recovery, les paramètres Site Recovery de VMM sont supprimés de la console VMM et le réplica est supprimé.
10. Dans **Méthode de réplication initiale**, si vous effectuez une réplication sur le réseau, indiquez si vous souhaitez lancer la réplication initiale ou la planifier. Pour économiser de la bande passante réseau, il peut être intéressant de la planifier en dehors des heures de pointe. Cliquez ensuite sur **OK**.

     ![Stratégie de réplication](./media/site-recovery-vmm-to-vmm/gs-replication2.png)
11. Lorsque vous créez une stratégie, elle est automatiquement associée au cloud VMM. Dans **Stratégie de réplication**, cliquez sur **OK**. Vous pouvez associer des clouds VMM supplémentaires (ainsi que les machines virtuelles qu’ils contiennent) à cette stratégie de réplication en cliquant sur **Paramètres** > **Réplication** > nom de la stratégie > **Associate VMM Cloud** (Associer un cloud VMM).

     ![Stratégie de réplication](./media/site-recovery-vmm-to-vmm/policy-associate.png)

### <a name="prepare-for-offline-initial-replication"></a>Préparer la réplication initiale hors connexion
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

### <a name="configure-network-mapping"></a>configurer le mappage réseau
Configurez le mappage réseau entre les réseaux source et cible.

* [Lisez cette section](#prepare-for-network-mapping) pour obtenir une présentation rapide du mappage réseau. Pour une explication plus détaillée, [voir ce document](site-recovery-network-mapping.md) .
* Vérifiez que les machines virtuelles sur les serveurs VMM sont connectées à un réseau de machines virtuelles.

Configurez le mappage comme suit :

1. **Paramètres** > **Infrastructure Site Recovery** > **Mappage réseau** > **Mappages réseau**, cliquez sur **+Mappage réseau**.

    ![Mappage réseau](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. Dans l’onglet **Ajouter un mappage réseau**, sélectionnez les serveurs VMM source et cible. Les réseaux de machines virtuelles associés aux serveurs VMM sont récupérés.
3. Dans la zone **Réseau source**, sélectionnez le réseau à utiliser dans la liste de réseaux de machines virtuelles associés au serveur VMM principal.
4. Dans **Réseau cible**, sélectionnez le réseau que vous souhaitez utiliser sur le serveur VMM secondaire. Cliquez ensuite sur **OK**.

    ![Mappage réseau](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

Voici le processus exécuté lorsque le mappage réseau démarre :

* Toute machine virtuelle de réplication existante qui correspond au réseau de machines virtuelles source sera connectée aux réseaux de machines virtuelles cibles.
* Les nouvelles machines virtuelles qui sont connectées au réseau de machines virtuelles source seront connectées au réseau mappé cible après la réplication.
* Si vous modifiez un mappage existant avec un nouveau réseau, les machines virtuelles de réplication seront connectées à l'aide des nouveaux paramètres.
* Remarque : si le réseau cible est associé à plusieurs sous-réseaux et que l’un d’eux présente le même nom que le sous-réseau dans lequel se trouve la machine virtuelle source, l’ordinateur virtuel de réplication est connecté à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible avec un nom correspondant, la machine virtuelle sera connectée au premier sous-réseau du réseau.

### <a name="configure-storage-mapping"></a>configurer le mappage de stockage
Par défaut, lorsque vous répliquez une machine virtuelle Hyper-V vers un serveur Hyper-V cible, les données répliquées sont stockées dans l'emplacement par défaut pour l'hôte Hyper-V cible dans le Gestionnaire Hyper-V. Pour contrôler l’emplacement de stockage des données répliquées, vous pouvez configurer un mappage de stockage.<br/><br/> Pour cela, vous configurez des classifications de stockage sur les serveurs VMM source et cible avant de commencer le déploiement.

Le mappage de stockage n’est actuellement pas pris en charge dans le nouveau portail Azure. Toutefois, il peut être activé à l’aide de Powershell. [En savoir plus](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-7-configure-storage-mapping).

## <a name="step-5-capacity-planning"></a>Étape 5 : planifier la capacité

Votre infrastructure de base est désormais configurée. Vous pouvez donc réfléchir à la planification de la capacité et déterminer si des ressources supplémentaires sont nécessaires.

Site Recovery propose une fonctionnalité Excel, Capacity Planner, qui permet d’allouer les bonnes ressources à l’environnement source, aux composants de récupération de sites, à la mise en réseau et au stockage. Vous pouvez exécuter Capacity Planner en mode rapide, afin d’obtenir une estimation basée sur le nombre moyen de machines virtuelles et de disques ainsi que sur l’espace de stockage disponible, ou en mode détaillé. Dans ce mode, vous saisissez des chiffres au niveau des charges de travail. Avant de commencer, vous devez :

* collecter les informations relatives à votre environnement de réplication, notamment les machines virtuelles, le nombre de disques par machine virtuelle et le stockage par disque ;
* déterminer le taux de modification (l’évolution) quotidienne des données delta répliquées ; Pour cela, vous pouvez utiliser la fonction [Capacity Planner pour réplica Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) .

1. Cliquez sur **Télécharger** pour télécharger l’outil, puis exécutez-le. [Lisez l’article](site-recovery-capacity-planner.md) relatif à cet outil.
2. Quand vous avez terminé, sélectionnez **Oui** sous **Have you run the Capacity Planner?** (Avez-vous exécuté Capacity Planner ?)

   ![Planification de la capacité](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="control-bandwidth"></a>Contrôler la bande passante
Une fois que vous avez collecté les informations de réplication delta en temps réel à l’aide du Capacity Planner pour réplica Hyper-V, l’outil Capacity Planner basé sur Excel vous aide à calculer la bande passante nécessaire pour la réplication (initiale et delta). Pour contrôler la quantité de bande passante utilisée pour la réplication, vous pouvez configurer la stratégie NetQos à l’aide d’une stratégie de groupe ou de Windows PowerShell. Il existe plusieurs façons de le faire :

| **PowerShell** | **Détails** |
| --- | --- |
| **New -NetQosPolicy "QoS to destination subnet"** |Limiter le trafic à partir d’un hôte Hyper-V sous Windows Server 2012 R2 à un sous-réseau secondaire. À utiliser si vos sous-réseaux principaux et secondaires sont différents. |
| **New -NetQosPolicy "QoS to destination port"** |Limiter le trafic à partir d’un hôte Hyper-V sous Windows Server 2012 R2 à un port de destination. |
| **New -NetQosPolicy "Throttle traffic from VMM"** |Limiter le trafic à partir de vmms.exe. Cela limite le trafic Hyper-V et le trafic de migration dynamique. Vous pouvez associer des ports et des protocoles IP pour affiner la limitation. |

Vous pouvez utiliser des paramètres de poids de la bande passante ou limiter le trafic en bits par réseau secondaire. Si vous utilisez un cluster, vous devez le faire sur tous les nœuds de cluster. Pour plus d’informations, consultez les pages suivantes :

* Blog de Thomas Maurer sur la [Limitation du trafic des réplicas Hyper-V](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/)
* Informations supplémentaires sur [l’applet de commande New-NetQosPolicy](https://technet.microsoft.com/library/hh967468.aspx).

## <a name="step-6-enable-replication"></a>Étape 6 : activer la réplication
À présent, activez la réplication comme suit :

1. Cliquez sur **Étape 2 : Répliquer l’application** > **Source**. Après avoir activé la réplication pour la première fois, cliquez sur l’option **+Répliquer** dans le coffre pour activer la réplication des machines supplémentaires.

    ![Activer la réplication](./media/site-recovery-vmm-to-vmm/enable-replication1.png)
2. Dans **Source**, sélectionnez le serveur VMM et le cloud hébergeant les hôtes Hyper-V à répliquer. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmm-to-vmm/enable-replication2.png)
3. Dans **Cible**, vérifiez le serveur VMM secondaire et le cloud.
4. Dans **Machines virtuelles**, sélectionnez les machines virtuelles que vous souhaitez protéger dans la liste.

    ![Activer la protection pour les machines virtuelles](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

Vous pouvez suivre la progression de l’action **Activer la protection** dans **Paramètres** > **Travaux** > **Travaux Site Recovery**. Lorsque la tâche **Finaliser la protection** s’exécute, la machine virtuelle est prête à être basculée.

> [!NOTE]
> Vous pouvez également activer la protection des machines virtuelles dans la console VMM. Cliquez sur **Activer la protection** dans la barre d’outils dans les propriétés de la machine virtuelle > onglet **Azure Site Recovery**.
>
>

Une fois que vous avez activé la réplication, vous pouvez afficher les propriétés de la machine virtuelle dans **Paramètres** > **Éléments répliqués**. Dans le tableau de bord **Essentials**, vous pouvez voir des informations sur la stratégie de réplication pour la machine virtuelle et son état. Cliquez sur **Propriétés** pour obtenir plus de détails.

### <a name="onboard-existing-virtual-machines"></a>Intégrer des machines virtuelles existantes
Si vous avez des machines virtuelles existantes dans VMM qui sont répliquées à l’aide du réplica Hyper-V, vous pouvez les intégrer à la réplication Azure Site Recovery comme suit :

1. Vérifiez que le serveur Hyper-V qui héberge la machine virtuelle se trouve dans le cloud principal et que le serveur Hyper-V qui héberge la machine virtuelle de réplication se trouve dans le cloud secondaire.
2. Assurez-vous que la stratégie de réplication est configurée pour le cloud VMM principal.
3. Activez la réplication de la machine virtuelle principale. Azure Site Recovery et VMM s’assurent que la même machine virtuelle et le même hôte de réplication seront détectés, et Azure Site Recovery réutilisera et rétablira la réplication à l’aide des paramètres spécifiés.

## <a name="step-7-test-your-deployment"></a>Étape 7 : tester votre déploiement
Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle ou créer un plan de récupération qui contient une ou plusieurs machines virtuelles.

### <a name="prepare-for-failover"></a>Préparer un basculement
* Pour tester entièrement votre déploiement, vous avez besoin d’une infrastructure pour permettre à la machine répliquée de fonctionner comme prévu. Si vous souhaitez tester Active Directory et DNS, vous pouvez créer une machine virtuelle jouant le rôle de contrôleur de domaine avec DNS, puis la répliquer sur Azure par le biais d’Azure Site Recovery. Pour en savoir plus, lisez [Considérations en matière de test de basculement pour Active Directory](site-recovery-active-directory.md#test-failover-considerations).
* Les instructions de cet article décrivent comment exécuter un test de basculement sans réseau. Cette option vérifie que la machine virtuelle bascule, mais ne teste pas les paramètres réseau de la machine virtuelle. [En savoir plus](site-recovery-failover.md#run-a-test-failover) sur les autres options.
* Si vous souhaitez exécuter un basculement non planifié au lieu d’un test de basculement, notez les éléments suivants :

  * Il est préférable d’arrêter les machines principales avant d’exécuter un basculement non planifié lorsque c’est possible. Vous êtes ainsi sûr que les machines source et les réplicas ne fonctionnent pas en même temps.
  * Lorsque vous effectuez un basculement non planifié, la réplication des données depuis les machines principales s’arrête et les différences dans les données ne sont pas transférées après qu’un basculement non planifié a commencé. En outre, si vous exécutez un basculement non planifié sur un plan de récupération, il sera exécuté jusqu’à la fin, même si une erreur se produit.

### <a name="run-a-test-failover"></a>Exécution d’un test de basculement
1. Pour effectuer le basculement d’une seule machine virtuelle, dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle, puis sur **+Test de basculement**.

    ![Test de basculement](./media/site-recovery-vmm-to-vmm/test-failover.png)
2. Pour effectuer le basculement d’un plan de récupération, dans **Paramètres** > **Plans de récupération**, cliquez avec le bouton droit sur le plan et sélectionnez **Test de basculement**. Pour créer un plan de récupération, suivez [ces instructions](site-recovery-create-recovery-plans.md).
3. Dans **Test de basculement**, sélectionnez **Aucun**. Avec cette option, vous vérifiez que la machine virtuelle bascule comme prévu, mais que la machine virtuelle répliquée n’est connectée à aucun réseau.

    ![Test de basculement](./media/site-recovery-vmm-to-vmm/test-failover1.png)
4. Cliquez sur **OK** pour commencer le basculement. Vous pouvez suivre la progression du basculement en cliquant sur la machine virtuelle pour ouvrir ses propriétés, ou en sélectionnant la tâche **Test de basculement** dans **Paramètres** > **Travaux** > **Travaux Site Recovery**.
5. Quand le travail de basculement atteint la phase **Terminer le test** , procédez comme suit :

   * Affichez la machine virtuelle de réplication dans le cloud VMM secondaire.
   * Cliquez sur **Terminer le test** pour finir le test de basculement.
   * Cliquez sur **Notes** pour consigner et enregistrer les éventuelles observations associées au test de basculement.
6. La machine virtuelle de test est créée sur le même hôte que celui sur lequel la machine virtuelle de réplication existe. Elle est ajoutée au cloud où se trouve la machine virtuelle de réplication.
7. Après avoir vérifié que les machines virtuelles se lancent correctement, cliquez sur **Le test de basculement est terminé**. À cette étape, les éléments créés automatiquement par Site Recovery lors du test de basculement sont supprimés.  

   > [!NOTE]
   > Si un test de basculement s’étend sur plus de deux semaines, le système le force à se terminer.
   >
   >

### <a name="update-dns-with-the-replica-vm-ip-address"></a>Mettre à jour le DNS avec l’adresse IP de la machine virtuelle de réplication
Après le basculement, la machine virtuelle de réplication peut ne pas avoir la même adresse IP que la machine virtuelle principale.

* Les machines virtuelles mettront à jour le serveur DNS qu'elles utilisent après leur démarrage.
* Vous pouvez mettre à jour le DNS manuellement comme suit :

#### <a name="retrieve-the-ip-address"></a>Récupérer l’adresse IP
Exécutez cet exemple de script pour récupérer l'adresse IP.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="update-dns"></a>Mettre à jour le DNS
Exécutez cet exemple de script pour mettre à jour DNS, en spécifiant l'adresse IP que vous avez récupérée à l'aide de l'exemple de script précédent.

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## <a name="next-steps"></a>Étapes suivantes
Une fois votre déploiement configuré et en cours d’exécution, découvrez [plus d’informations](site-recovery-failover.md) sur les différents types de basculement.



<!--HONumber=Jan17_HO5-->


