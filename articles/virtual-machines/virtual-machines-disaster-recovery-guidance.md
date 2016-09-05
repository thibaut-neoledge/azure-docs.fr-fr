<properties
	pageTitle="Que faire si une interruption du service Azure affecte de machines virtuelles Azure ? | Microsoft Azure"
	description="Découvrez quoi faire si une interruption du service Azure affecte de machines virtuelles Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="kmouss"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="virtual-machines"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="kmouss;aglick"/>

#Que faire si une interruption du service Azure affecte des machines virtuelles Azure ?

Microsoft s’engage à déployer tous les efforts nécessaires pour vous garantir en permanence la disponibilité de ses services quand vous en avez besoin. Il arrive parfois que des phénomènes incontrôlables entraînent des interruptions de service non planifiées.

Microsoft fournit un Contrat de niveau de service (SLA) pour ses services en guise d’engagement en matière de disponibilité et de connectivité. Le contrat de niveau de service des différents services Azure se trouve à la page [Contrats de niveau de service Azure](https://azure.microsoft.com/support/legal/sla/).

Azure dispose déjà de nombreuses fonctionnalités intégrées de plateforme qui prennent en charge des applications hautement disponibles. Pour plus d’informations sur ces services, consultez [Récupération d’urgence et haute disponibilité pour les applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Cet article aborde un scénario réel de récupération d’urgence, dans lequel une région entière connaît une panne en raison d’une catastrophe naturelle majeure ou d’une interruption de service importante. Bien que ces cas soient rares, vous devez envisager l’éventualité d’une panne affectant l’ensemble d’une région. Si une région entière est confrontée à une interruption de service, les copies localement redondantes de vos données sont temporairement indisponibles. Si vous avez activé la géoréplication, trois copies supplémentaires de vos tables et objets blob Azure Storage sont stockées dans une autre région. En cas de panne régionale totale ou de sinistre rendant la région primaire irrécupérable, Azure remappe toutes les entrées DNS sur la région géorépliquée.

>[AZURE.NOTE]N’oubliez pas que vous n’avez aucun contrôle sur ce processus et qu’il ne se produit que pour des interruptions du service au niveau régional. Ainsi, vous devez également vous appuyer sur d’autres stratégies de sauvegarde propres à l’application pour atteindre le plus haut niveau de disponibilité. Pour plus d’informations, consultez la section consacrée aux [stratégies de données pour une récupération d’urgence](../resiliency/resiliency-disaster-recovery-azure-applications.md#data-strategies-for-disaster-recovery).

Pour vous aider à gérer ces rares occurrences, nous vous fournissons les conseils suivants pour les machines virtuelles Azure dans le cas d’une interruption de service sur l’ensemble de la région où votre application Azure Virtual Machine est déployée.

##Option 1: attente de récupération
Dans ce cas, aucune action n’est requise de votre part. Soyez assuré que nous travaillons assidûment à la restauration de la disponibilité du service. Vous pouvez consulter l’état actuel du service dans notre [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Cette option est idéale si vous n’avez pas activé la sauvegarde de machines virtuelles Azure Site Recovery, le stockage géoredondant avec accès en lecture ou le stockage géoredondant avant l’interruption. Si vous avez configuré le stockage géoredondant ou le stockage géoredondant avec accès en lecture pour le compte de stockage où sont stockés les disques durs virtuels de vos machines virtuelles, vous pouvez essayer de récupérer le disque dur virtuel de l’image de base et l’utiliser pour tenter d’approvisionner une nouvelle machine virtuelle. Cette option n’est pas recommandée, car elle ne garantit aucune synchronisation des données (à moins d’utiliser la sauvegarde de machines virtuelles Azure ou Azure Site Recovery). Par conséquent, le fonctionnement de cette option n’est pas garanti.

Les clients souhaitant un accès immédiat à leurs machines virtuelles disposent des deux options suivantes.

>[AZURE.NOTE]Gardez à l’esprit que ces deux options peuvent entraîner une perte partielle des données.

##Option 2 : restaurer une machine virtuelle à partir d’une sauvegarde
Les clients qui ont configuré une sauvegarde de machines virtuelles ont la possibilité de restaurer la machine virtuelle à partir de son point de sauvegarde et de restauration.

Pour restaurer une machine virtuelle à partir d’une sauvegarde Azure, voir la rubrique relative à la [restauration des machines virtuelles dans Azure](../backup/backup-azure-restore-vms.md).

Pour vous aider à planifier votre infrastructure de sauvegarde de machines virtuelles Azure, référez-vous à la [Planification de votre infrastructure de sauvegarde de machines virtuelles dans Azure](../backup/backup-azure-vms-introduction.md).

##Option 3 : initier un basculement à l’aide d’Azure Site Recovery
Si vous avez configuré Azure Site Recovery pour gérer les machines virtuelles Azure concernées, vous pouvez restaurer vos machines virtuelles à partir de leurs réplicas. Ces réplicas peuvent résider dans Azure ou même sur site. Dans ce cas, vous pouvez créer une machine virtuelle à partir de son réplica. Pour restaurer vos machines virtuelles à partir d’un réplica Azure Site Recovery, voir [Migrer des machines virtuelles IaaS Azure entre différentes régions Azure avec Azure Site Recovery](../site-recovery/site-recovery-migrate-azure-to-azure.md).

>[AZURE.NOTE]Même si le système d’exploitation et les disques de données de machines virtuelles Azure sont répliqués sur un disque dur virtuel secondaire, chaque disque dur virtuel est répliqué séparément s’ils se trouvent dans un stockage géoredondant ou un compte de stockage géoredondant avec accès en lecture. Ce niveau de réplication ne garantit pas la cohérence entre les disques durs virtuels répliqués. Si l’application et/ou les bases de données qui utilisent ces disques de données sont interdépendantes, il n’est pas garanti que tous les disques durs virtuels seront répliqués sous la forme d’un seul instantané. De même, rien ne garantit que le réplica du disque dur virtuel depuis un stockage géoredondant ou stockage géoredondant avec accès en lecture générera un instantané cohérent avec l’application pour le démarrage de la machine virtuelle.

##Étapes suivantes
Pour plus d’informations sur la façon d’implémenter une stratégie de récupération d’urgence et de haute disponibilité, consultez [Récupération d’urgence et haute disponibilité pour les applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Pour une compréhension technique détaillée des fonctionnalités de la plateforme cloud, consultez le [Guide technique de la résilience Azure](../resiliency/resiliency-technical-guidance.md).

Pour savoir comment sauvegarder des machines virtuelles, consultez [Sauvegarde des machines virtuelles](../backup/backup-azure-vms.md).

Pour découvrir comment utiliser Azure Site Recovery pour orchestrer et automatiser la protection de vos ordinateurs Windows et Linux physiques (et virtuels) qui s’exécutent sur des machines virtuelles VMWare et Hyper-V, consultez [Azure Site Recovery](https://azure.microsoft.com/documentation/learning-paths/site-recovery/) (en anglais).

Si les instructions ne sont pas claires ou que vous souhaitez que Microsoft effectue les opérations en votre nom, contactez le [service clientèle](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

<!---HONumber=AcomDC_0824_2016-->