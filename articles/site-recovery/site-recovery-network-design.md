<properties
	pageTitle="Considérations relatives au déploiement de VMM et de Site Recovery | Microsoft Azure"
	description="Cet article aborde les considérations de conception pratiques pour l’intégration de VMM à Azure Site Recovery"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="02/15/2016"
	ms.author="raynew"/>

#  Considérations relatives au déploiement de VMM et de Site Recovery

Le service Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Pour obtenir un rapide aperçu, consultez [Qu’est-ce qu’Azure Site Recovery ?](site-recovery-overview.md)


## Vue d'ensemble

Cet article vous permet de concevoir et d’implémenter une infrastructure et une solution BCDR qui inclut System Center VMM et Azure Site Recovery.

L'objectif de votre stratégie BCDR est de conserver vos applications d'entreprise en cours d'exécution et de restaurer les charges de travail et les services ayant échoué afin que l'organisation puisse rapidement reprendre ses opérations habituelles. Le développement de stratégies de récupération d’urgence représente un défi en raison de la difficulté à prévoir des événements imprévus et du coût élevé de l’implémentation d’une protection adéquate contre les défaillances importantes. Site Recovery vous permet de mettre en œuvre la protection et le basculement de votre centre de données principal vers un centre de données secondaire (ou vers Azure) en effectuant une copie (réplication) initiale de vos données primaires, puis en actualisant régulièrement les réplicas.

En tant que partie intégrante de votre planification BCDR, vous devez définir votre objectif de délai de récupération (RTO) et votre objectif de point de récupération (RPO) afin que vous puissiez remettre en ligne les données de votre entreprise aussi rapidement que possible (avec une faible RTO) et avec une perte minimale de données (RPO faible). La conception du réseau de votre organisation est un goulot d'étranglement potentiel pour vos objectifs RTO et RPO et une planification solide de la conception permet d’éviter cette situation.


Si vous déployez Site Recovery pour répliquer des machines virtuelles Hyper-V dans des clouds VMM, vous devez prendre en compte la façon dont VMM s’intègre à votre stratégie de basculement et de réplication de Site Recovery.

## Intégration d’un serveur VMM autonome

Dans cette topologie, déployez un serveur VMM autonome sur une machine virtuelle sur le site principal, puis répliquez cette machine virtuelle sur un site secondaire avec Site Recovery et Réplica Hyper-V. Vous pouvez envisager l’installation du serveur VMM et de sa prise en charge SQL Server sur la même machine virtuelle, ce qui permettrait de réduire le temps d’arrêt, car une seule machine virtuelle doit être instanciée. Lorsque VMM utilise un SQL Server distant, vous devez récupérer l’instance SQL Server avant de récupérer le serveur VMM.

Pour déployer un seul VMM sur une machine virtuelle avec Réplica Hyper-V :

1. Configurez le VMM sur une machine virtuelle avec SQL Server installé.
2. Ajoutez les hôtes qui doivent être gérés dans les clouds sur ce serveur VMM.
3. Connectez-vous au portail Azure, puis configurez les clouds pour la protection.
4. Activez la réplication pour toutes les machines virtuelles qui doivent être protégées par le serveur VMM.
5. Accédez à la console Hyper-V Manager, choisissez Réplica Hyper-V, puis activez la réplication sur la machine virtuelle VMM.
6. Assurez-vous que la machine virtuelle VMM n'est pas ajoutée aux clouds protégés par le service Azure Site Recovery afin que les paramètres de Réplica Hyper-V ne soient pas remplacés par ceux d’Azure Site Recovery.

En cas de récupération d’urgence, vous pouvez récupérer les charges de travail en procédant comme suit :

1. Basculez le réplica de la machine virtuelle VMM sur le site de récupération à l'aide de Hyper-V Manager.
2. Une fois la machine virtuelle VMM récupérée, l'utilisateur peut se connecter à Hyper-V Recovery Manager à partir du site secondaire.
3. Lorsque le basculement non planifié est terminé, l’utilisateur peut accéder à toutes les ressources sur le site principal.
4. Notez que la machine virtuelle VMM devra être basculée manuellement vers le site secondaire avant que les charges de travail ne puissent être basculées.


### Intégration d’un cluster VMM


Le [déploiement de VMM dans un cluster](https://technet.microsoft.com/library/gg610675.aspx) fournit une haute disponibilité et une protection contre le basculement du matériel. Si vous déployez votre cluster VMM avec Site Recovery, notez que :

- Le serveur VMM doit être déployé dans un cluster étiré sur des sites géographiquement distincts.
- La base de données SQL Server utilisée par VMM doit être protégée avec des groupes de disponibilité SQL Server AlwaysOn avec un réplica sur le site secondaire.
- Si un problème survient, le serveur VMM et son serveur SQL correspondant basculent automatiquement vers le site de récupération. Vous pouvez ensuite basculer les charges de travail à l'aide de Site Recovery.

	![Cluster VMM étiré](./media/site-recovery-network-design/network-design1.png)


## Étapes suivantes

[Découvrir](site-recovery-network-mapping.md) comment Site Recovery mappe les réseaux source et cible.

<!---HONumber=AcomDC_0218_2016-->