<properties
   pageTitle="Index des guides techniques de résilience | Microsoft Azure"
   description="Index des articles techniques dédiés à la compréhension et à la conception d’applications à tolérance de pannes robustes et hautement disponibles, ainsi qu’à la planification de la continuité d’activité et de la récupération d’urgence"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Guide technique de la résilience Azure

##Introduction

Le respect des exigences de haute disponibilité et de récupération d’urgence requiert deux types de connaissances :

- Compréhension technique détaillée des fonctionnalités de la plateforme cloud
- Connaissance de la façon de concevoir correctement un service distribué

Cette série d’articles couvre les fonctionnalités et limitations de la plateforme Azure en termes de résilience (que l’on appelle parfois « continuité d’activité »). Si vous êtes intéressé par le second aspect, consultez la série d’articles dédiée à [la récupération d’urgence et la haute disponibilité pour les applications Azure](https://aka.ms/drtechguide). Bien que cette série d’articles aborde les modèles d’architecture et de conception, elle ne se concentre pas spécifiquement sur ces aspects. Pour obtenir des conseils de conception, vous pouvez consulter la documentation détaillée dans la section [Ressources supplémentaires](#additional-resources).

Les informations de cette série sont organisées en plusieurs articles :

- [Récupération des défaillances en local](resiliency-technical-guidance-recovery-local-failures.md). Le matériel physique (par exemple, les disques, les serveurs et les périphériques réseau) peut connaître des défaillances. Les ressources peuvent être épuisées lors des pics de charge. Cet article décrit les fonctionnalités fournies par Azure pour assurer une haute disponibilité dans ces conditions.

- [Récupération d’une interruption de service à l’échelle de la région Azure](resiliency-technical-guidance-recovery-loss-azure-region.md). Les défaillances à grande échelle sont rares, mais théoriquement possibles. Des régions entières peuvent être isolées en raison de défaillances du réseau, ou physiquement endommagées suite à des catastrophes naturelles. Cet article explique comment utiliser Azure pour créer des applications qui s’étendent à diverses régions géographiques.

- [Récupération de l’environnement local vers Azure](resiliency-technical-guidance-recovery-on-premises-azure.md). Le cloud modifie de manière significative la dimension économique de la récupération d’urgence, ce qui permet aux organisations d’utiliser Azure pour créer un deuxième site de récupération. Cette alternative peut s’avérer bien plus économique que la création et la maintenance d’un centre de données secondaire. Cet article décrit les fonctionnalités fournies par Azure pour étendre un centre de données local vers le cloud.

- [Récupération suite à une corruption de données ou à une suppression accidentelle](resiliency-technical-guidance-recovery-data-corruption.md). Les applications peuvent avoir des bogues qui endommagent les données. Les opérateurs peuvent supprimer par erreur des données importantes. Cet article décrit les fonctionnalités offertes par Azure pour sauvegarder les données et les restaurer à un point antérieur dans le temps.

##Ressources supplémentaires

- [Récupération d’urgence et haute disponibilité pour les applications exécutées sur Microsoft Azure](resiliency-disaster-recovery-high-availability-azure-applications.md). Cet article offre une présentation détaillée de la haute disponibilité et de la récupération d’urgence. Cet article aborde le problème de la réplication manuelle pour les données de référence et les données transactionnelles. Les dernières sections synthétisent différents types de topologies de récupération d’urgence qui couvrent plusieurs régions Azure pour un niveau de disponibilité optimal.

- [Liste de contrôle de haute disponibilité](resiliency-high-availability-checklist.md). Cet article présente une liste de fonctionnalités, services et conceptions qui peuvent vous aider à améliorer la résilience, ainsi que la disponibilité, de votre application.

- [Guide de résilience des services Microsoft Azure](resiliency-service-guidance-index.md). Cet article est un index de services Azure qui fournit des liens vers des conseils en matière de récupération d’urgence et de conception.

- [Vue d’ensemble : continuité des activités cloud et récupération d’urgence d’une base de données avec la base de données SQL](../sql-database/sql-database-business-continuity.md). Cet article fournit des techniques de base de données SQL Azure pour la disponibilité. Il se concentre principalement sur les stratégies de sauvegarde et de restauration. Si vous utilisez la base de données SQL Azure dans votre service cloud, vous devez consulter ce document et ses ressources connexes.

- [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md). Cet article présente les options de disponibilité que vous pouvez explorer lorsque vous utilisez une solution infrastructure as a service (IaaS) pour héberger vos services de base de données. Cet article traite des groupes de disponibilité AlwaysOn, de la mise en miroir de bases de données, de l’envoi de journaux et des opérations de sauvegarde/restauration. Plusieurs didacticiels montrent comment utiliser ces techniques.

- [Meilleures pratiques de conception de services à grande échelle dans Azure Cloud Services](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/). Cet article est consacré au développement d’architectures cloud hautement évolutives. La plupart des techniques que vous utilisez pour améliorer l’évolutivité contribuent également à améliorer la disponibilité. En outre, si votre application ne peut pas être mise à l’échelle en cas de pic de charge, le problème d’extensibilité devient un problème de disponibilité.

- [Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md). Cet article fournit des conseils techniques sur la façon de sauvegarder et restaurer Microsoft SQL Server sur des machines virtuelles Azure.

- [Failsafe: guidance for resilient cloud architectures (Sécurité intégrée : recommandations applicables à la résilience des architectures cloud)](https://channel9.msdn.com/Series/FailSafe). Cet article fournit des conseils pour le développement d’architectures cloud résilientes et pour l’implémentation de ces architectures sur des technologies Microsoft, ainsi que des recommandations sur l’implémentation de ces architectures dans le cadre de scénarios spécifiques.

- [Étude de cas technique : utilisation des technologies de cloud pour améliorer la récupération d’urgence](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery). Cette étude de cas technique montre comment Microsoft IT a utilisé Azure pour améliorer la récupération d’urgence.

##Étapes suivantes

Cet article fait partie d’une série qui fournit des conseils techniques pour la résilience Azure. Si vous êtes intéressé par d’autres articles de cette série, vous pouvez commencer par [Récupération des défaillances locales](resiliency-technical-guidance-recovery-local-failures.md).

<!---HONumber=AcomDC_0615_2016-->