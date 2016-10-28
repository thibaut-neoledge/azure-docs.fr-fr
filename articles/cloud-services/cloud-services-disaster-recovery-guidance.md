<properties
	pageTitle="Que faire si une interruption de service Azure affecte Azure Cloud Services | Microsoft Azure"
	description="Découvrez que faire si une interruption de service Azure affecte Azure Cloud Services."
	services="cloud-services"
	documentationCenter=""
	authors="kmouss"
	manager="drewm"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="cloud-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="kmouss;aglick"/>

#Que faire si une interruption de service Azure affecte Azure Cloud Services

Microsoft s’engage à déployer tous les efforts nécessaires pour vous garantir en permanence la disponibilité de ses services quand vous en avez besoin. Il arrive parfois que des phénomènes incontrôlables entraînent des interruptions de service non planifiées.

Microsoft fournit un Contrat de niveau de service (SLA) pour ses services en guise d’engagement en matière de disponibilité et de connectivité. Le contrat de niveau de service des différents services Azure se trouve à la page [Contrats de niveau de service Azure](https://azure.microsoft.com/support/legal/sla/).

Azure dispose déjà de nombreuses fonctionnalités intégrées de plateforme qui prennent en charge des applications hautement disponibles. Pour plus d’informations sur ces services, consultez [Récupération d’urgence et haute disponibilité pour les applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Cet article aborde un scénario réel de récupération d’urgence, dans lequel une région entière connaît une panne en raison d’une catastrophe naturelle majeure ou d’une interruption de service importante. Bien que ces cas soient rares, vous devez envisager l’éventualité d’une panne affectant l’ensemble d’une région. Si une région entière est confrontée à une interruption de service, les copies localement redondantes de vos données sont temporairement indisponibles. Si vous avez activé la géoréplication, trois copies supplémentaires de vos tables et objets blob Azure Storage sont stockées dans une autre région. En cas de panne régionale totale ou de sinistre rendant la région primaire irrécupérable, Azure remappe toutes les entrées DNS sur la région géorépliquée.

>[AZURE.NOTE]N’oubliez pas que vous n’avez aucun contrôle sur ce processus et qu’il ne se produit que pour les interruptions du service au niveau du centre de données. Ainsi, vous devez également vous appuyer sur d’autres stratégies de sauvegarde propres à l’application pour atteindre le plus haut niveau de disponibilité. Pour plus d’informations, consultez la section consacrée aux [stratégies de données pour une récupération d’urgence](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md#DSDR). Si vous souhaitez avoir un impact sur votre propre basculement, vous pouvez envisager l’utilisation du [stockage géoredondant avec accès en lecture (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage), qui crée une copie en lecture seule de vos données dans une autre région.

Pour vous aider à gérer ces rares occurrences, nous vous fournissons les conseils suivants pour les machines virtuelles Azure dans le cas d’une interruption de service de l’ensemble de la région où votre application Azure Virtual Machine est déployée.

##Option 1: attente de récupération
Dans ce cas, aucune action n’est requise de votre part. Sachez que les équipes Azure travaillent assidûment à la restauration de la disponibilité des services. Vous pouvez consulter l’état actuel du service dans notre [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Ceci est la meilleure option si un client n’a pas configuré Azure Site Recovery ou dispose d’un déploiement secondaire dans une autre région.

Pour les clients souhaitant un accès immédiat à leurs services cloud déployés, les options suivantes sont disponibles.

>[AZURE.NOTE]Gardez à l’esprit que ces options peuvent entraîner une perte partielle des données.

##Option 2 : redéploiement de la configuration de votre service cloud sur une nouvelle région

Si vous disposez de votre code d’origine, vous pouvez tout simplement redéployer l’application, ainsi que la configuration et les ressources associées, sur un nouveau service cloud dans une nouvelle région.

Pour plus d’informations sur la façon de créer et de déployer une application de service cloud, consultez [Création et déploiement d’un service cloud](./cloud-services-how-to-create-deploy-portal.md).

Suivant vos sources de données d’application, vous pouvez être amené à vérifier les procédures de récupération.
  * Pour les sources de données Azure Storage, consultez [Réplication Azure Storage](../storage/storage-redundancy.md#read-access-geo-redundant-storage) pour vérifier les options disponibles selon le modèle de réplication choisi pour votre application.
  * Pour les sources SQL Database, consultez [Vue d’ensemble : continuité des activités cloud et récupération d’urgence d’une base de données avec SQL Database](../sql-database/sql-database-business-continuity.md) pour vérifier les options disponibles selon le modèle de réplication choisi pour votre application.

##Option 3 : utilisation d’un déploiement de sauvegarde par le biais du gestionnaire de trafic Azure
Cette option suppose que vous avez déjà conçu votre solution d’application avec une récupération d’urgence régionale à l’esprit. Vous pouvez utiliser cette option si vous disposez déjà d’un déploiement d’application Cloud Services secondaire en cours d’exécution dans une autre région et connecté par le biais d’un canal de gestionnaire de trafic. Dans ce cas, vérifiez l’intégrité du déploiement secondaire. S’il est sain, vous pouvez rediriger le trafic vers celui-ci via Azure Traffic Manager. Avec cette stratégie, vous pouvez bénéficier de la méthode de routage du trafic et des configurations d’ordre de basculement d’Azure Traffic Manager. Pour en savoir plus, voir [Configuration des paramètres Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-to-configure-traffic-manager-settings).

![Équilibrage d’Azure Cloud Services entre différentes régions avec Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

##Étapes suivantes

Pour plus d’informations sur la façon d’implémenter une stratégie de récupération d’urgence et de haute disponibilité, consultez [Récupération d’urgence et haute disponibilité pour les applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Pour une compréhension technique détaillée des fonctionnalités de la plateforme cloud, consultez le [Guide technique de la résilience Azure](../resiliency/resiliency-technical-guidance.md).

Si les instructions ne sont pas claires ou que vous souhaitez que Microsoft effectue les opérations en votre nom, contactez le [service clientèle](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

<!---HONumber=AcomDC_0629_2016-->