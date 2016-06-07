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
Pour répondre à vos besoins de haute disponibilité et de récupération d’urgence, vous devez : 1) comprendre les aspects techniques des fonctionnalités d’une plateforme cloud et 2) apprendre à concevoir correctement un service distribué. Cette série d’articles couvre les fonctionnalités et limitations de la plateforme Azure en termes de résilience (que l’on appelle également « continuité d’activité »). Si vous êtes intéressé par le second aspect, consultez la série d’articles dédiée à [la récupération d’urgence et la haute disponibilité pour les applications Azure](https://aka.ms/drtechguide). Bien que cette série d’articles aborde les modèles d’architecture et de conception, elle ne se concentre pas spécifiquement sur ces aspects. Le lecteur est invité à consulter la documentation détaillée dans la section [Ressources supplémentaires](#additional-resources) pour obtenir des conseils de conception.

Les informations de cette série sont organisées en plusieurs articles :
##[Récupération des défaillances en local](./resiliency-technical-guidance-recovery-local-failures.md)
Le matériel physique (par exemple, lecteurs, serveurs et périphériques réseau) peut subir une défaillance et entraîner un épuisement des ressources lors des pics de charge. Cette section décrit les fonctionnalités fournies par Azure pour assurer une haute disponibilité dans ces conditions.

##[Récupération d’une interruption de service à l’échelle de la région Azure](./resiliency-technical-guidance-recovery-loss-azure-region.md)
Les défaillances à grande échelle sont rares, mais théoriquement possibles. Des régions entières peuvent être isolées en raison de défaillances du réseau, ou être physiquement endommagées suite à des catastrophes naturelles. Cette section explique comment utiliser les fonctionnalités d’Azure pour créer des applications qui s’étendent à diverses régions géographiques.

##[Récupération de l’environnement local vers Azure](./resiliency-technical-guidance-recovery-on-premises-azure.md)
Le cloud modifie de manière significative la dimension économique de la récupération d’urgence, ce qui permet aux organisations d’utiliser Azure pour créer un deuxième site de récupération. Cette alternative peut s’avérer bien plus économique que la création et la maintenance d’un centre de données secondaire. Cette section décrit les fonctionnalités fournies par Azure pour étendre un centre de données local vers le cloud.

##[Récupération suite à une corruption de données ou à une suppression accidentelle](./resiliency-technical-guidance-recovery-data-corruption.md)
Les applications peuvent contenir des bogues qui endommagent les données ; de même, les opérateurs peuvent parfois supprimer par erreur des données importantes. Cette section décrit les fonctionnalités offertes par Azure pour sauvegarder les données et les restaurer à un point antérieur dans le temps.

##Ressources supplémentaires

###[Guide technique Azure Business Continuity](./resiliency-technical-guidance.md)
Liste détaillée des concepts et pratiques recommandées pour garantir la continuité d’activité et la résilience avec Microsoft Azure pour vos applications locales et vos applications de cloud, hybride ou public.

###[Récupération d’urgence et haute disponibilité pour les applications exécutées sur Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md)
Présentation détaillée de la haute disponibilité et de la récupération d’urgence. Cet article aborde le problème de la réplication manuelle pour les données de référence et les données transactionnelles. Les dernières sections synthétisent différents types de topologies de récupération d’urgence qui couvrent plusieurs régions Azure pour un niveau de disponibilité optimal.

###[Vue d’ensemble : continuité des activités cloud et récupération d’urgence d’une base de données avec SQL Database](../sql-database/sql-database-business-continuity.md)
Axé exclusivement sur les techniques de disponibilité utilisées par la base de données SQL Azure, notamment les stratégies de sauvegarde et de restauration. Si vous utilisez la base de données SQL Azure dans votre service cloud, vous devez consulter ce document et ses ressources connexes.

###[Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)
Présente les options de disponibilité dont vous pouvez bénéficier lorsque vous utilisez une solution IaaS (Infrastructure-as-a-Service) pour héberger vos services de base de données. Cet article traite des groupes de disponibilité AlwaysOn, de la mise en miroir de base de données, de l’envoi de journaux et des opérations de sauvegarde/restauration. Notez que la même section comporte également plusieurs didacticiels qui expliquent comment utiliser ces techniques.

###[Meilleures pratiques de conception de services à grande échelle dans Azure Cloud Services](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/)
Article consacré au développement d’architectures cloud hautement évolutives. La plupart des techniques que vous utilisez pour améliorer l’évolutivité contribuent également à améliorer la disponibilité. En outre, si votre application ne peut pas être mise à l’échelle en cas de pic de charge, le problème d’évolutivité devient alors un problème de disponibilité.

###[Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md)
Conseils techniques sur la façon de sauvegarder et restaurer Microsoft SQL Server sur Azure Virtual Machines.

###[Failsafe: guidance for resilient cloud architectures](https://channel9.msdn.com/Series/FailSafe) (Sécurité intégrée : recommandations applicables à la résilience des architectures cloud)
Conseils pour le développement d’architectures cloud résilientes et pour l’implémentation de ces architectures sur des technologies Microsoft, et recommandations sur l’implémentation de ces architectures dans le cadre de scénarios spécifiques.

##Étapes suivantes
Cet article fait partie d’une série intitulée [Guide technique de la résilience Azure](./resiliency-technical-guidance.md). Si vous êtes intéressé par d’autres articles de cette série, commencez par l’article consacré à [la récupération des défaillances locales](./resiliency-technical-guidance-recovery-local-failures.md).

<!---HONumber=AcomDC_0525_2016-->