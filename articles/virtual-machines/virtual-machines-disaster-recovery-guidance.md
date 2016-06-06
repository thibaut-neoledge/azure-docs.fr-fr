<properties
	pageTitle="Que faire si une interruption du service Azure affecte Azure Virtual Machines ? | Microsoft Azure"
	description="Découvrez la procédure à suivre si une interruption du service Azure affecte vos instances Azure Virtual Machines."
	services="virtual-machines"
	documentationCenter=""
	authors="kmouss"
	manager="drewm"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="virtual-machines"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="kmouss;aglick"/>

#Que faire si une interruption du service Azure affecte Azure Virtual Machines ?

Microsoft s’engage à déployer tous les efforts nécessaires pour vous garantir en permanence la disponibilité de ses services quand vous en avez besoin. Il arrive parfois que des phénomènes incontrôlables entraînent des interruptions de service non planifiées.

Microsoft fournit un Contrat de niveau de service (SLA) pour ses services en guise d’engagement en matière de temps d’activité et de connectivité. Le contrat SLA des différents services Azure se trouve à la page [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/).

Azure dispose déjà de nombreuses fonctionnalités de plateforme intégrées qui prennent en charge les applications hautement disponibles. Pour plus d’informations sur ces services, consultez [Récupération d’urgence et haute disponibilité pour les applications Azure](https://aka.ms/drtechguide).

Ce document aborde une récupération d’urgence réelle, dans laquelle une région entière connaît une panne en raison d’une catastrophe naturelle majeure ou d’une interruption de service importante. Bien que ces cas soient rares, vous devez envisager l’éventualité d’une panne affectant l’ensemble de la région. Si une région entière est confrontée à une interruption de service, les copies localement redondantes de vos données sont temporairement indisponibles. Si vous avez activé la géoréplication, trois copies supplémentaires de vos tables et objets blob Azure Storage sont stockées dans une autre région. En cas de panne régionale totale ou de sinistre rendant la région primaire irrécupérable, Azure remappe toutes les entrées DNS sur la région géorépliquée.
 
>[AZURE.NOTE]N’oubliez pas que vous n’avez aucun contrôle sur ce processus et qu’il ne se produit que pour les défaillances au niveau du centre de données. Ainsi, vous devez également vous appuyer sur d’autres stratégies de sauvegarde propres à l’application pour atteindre le plus haut niveau de disponibilité. Pour plus d’informations, consultez la section consacrée aux [stratégies de données pour une récupération d’urgence](https://aka.ms/drtechguide#DSDR).

Pour vous aider à gérer ces rares occurrences, nous vous fournissons les conseils suivants pour Azure Virtual Machine dans le cas d’une interruption de service de l’ensemble de la région où votre application Azure Virtual Machine est déployée.

##Option 1: attente de récupération 
Dans ce cas, aucune action n’est requise de votre part. Soyez assuré que nous travaillons assidûment à la restauration de la disponibilité du service. Vous pouvez consulter l’état actuel du service dans notre [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Cette option est idéale si vous n’avez pas installé Azure Site Recovery (ASR), la sauvegarde de machines virtuelles, le stockage géo-redondant avec accès en lecture (RA-GRS) ou le stockage géo-redondant (GRS) avant l’interruption. Si vous avez configuré le stockage GRS (ou RA-GRS) pour le compte de stockage où sont stockés les disques durs virtuels de vos machines virtuelles, vous pouvez essayer de récupérer le disque dur virtuel de l’image de base et l’utiliser pour tenter d’approvisionner une nouvelle machine virtuelle. Cette option n’est pas recommandée, car elle ne garantit aucune synchronisation des données (à moins d’utiliser une sauvegarde de machines virtuelles Azure ou ASR) et ne peut donc fonctionner de façon garantie.

Les clients souhaitant un accès immédiat à leurs machines virtuelles disposent des deux options suivantes.

>[AZURE.NOTE]Gardez à l’esprit que ces deux options peuvent entraîner une perte partielle des données.

##Option 2 : restaurer une machine virtuelle à partir d’une sauvegarde 
Les clients qui ont configuré une sauvegarde de machines virtuelles ont la possibilité de restaurer la machine virtuelle à partir de son point de sauvegarde et de restauration.

Suivez ces étapes pour restaurer une machine virtuelle à partir d’une sauvegarde Azure (voir la rubrique relative à la restauration des machines virtuelles dans Azure).

Pour vous aider à planifier votre infrastructure de sauvegarde de machines virtuelles Azure, lisez l’article [Planification de votre infrastructure de sauvegarde de machines virtuelles dans Azure](../backup/backup-azure-vms-introduction.md).

##Option 3 : initier un basculement à l’aide d’Azure Site Recovery (ASR) 
Si vous avez configuré Azure Site Recovery pour gérer les machines virtuelles Azure concernées, vous pouvez restaurer vos machines virtuelles à partir de leurs réplicas. Ces réplicas peuvent résider dans Azure ou même sur site. Dans ce cas, vous pouvez créer une nouvelle machine virtuelle à partir de son réplica. Procédez comme suit pour restaurer vos machines virtuelles à partir d’un réplica Azure Site Recovery. Voir [Migrer des machines virtuelles IaaS Azure entre différentes régions Azure avec Azure Site Recovery](../site-recovery/site-recovery-migrate-azure-to-azure.md).

>[AZURE.NOTE]Bien que le système d’exploitation et les disques de données Azure Virtual Machines seront répliquées vers un disque dur virtuel secondaire, s’ils se trouvent dans un compte de stockage GRS ou RA-GRS, chaque disque dur virtuel sera répliqué séparément. Or, ce niveau de réplication ne garantit pas la cohérence entre les disques durs virtuels répliqués. Si l’application et/ou les bases de données qui utilisent ces disques de données sont interdépendantes, il n’est pas garanti que tous les disques durs virtuels seront répliquées sous la forme d’un seul instantané. De même, rien ne garantit que le réplica du disque dur virtuel à partir du stockage GRS ou RA-GRS générera un instantané cohérent avec l’application pour le démarrage de la machine virtuelle.

##Références 
[Récupération d’urgence et haute disponibilité pour les applications Azure](https://aka.ms/drtechguide)

[Guide technique Azure Business Continuity](http://aka.ms/bctechguide)

[Sauvegarde des machines virtuelles Azure](../backup/backup-azure-vms.md)

[Azure Site Recovery](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)

Si les instructions ne sont pas claires ou que vous souhaitez que Microsoft effectue les opérations en votre nom, contactez le [service clientèle](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

<!---HONumber=AcomDC_0525_2016-->