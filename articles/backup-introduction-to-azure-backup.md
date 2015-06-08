<properties
	pageTitle="Introduction à la sauvegarde Azure"
	description="Cet article fournit une vue d'ensemble du service sauvegarde Azure qui permet aux clients de sauvegarder les données vers Azure"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/08/2015"
	ms.author="prvijay"/>

# Introduction à la sauvegarde Azure
Cet article fournit une vue d'ensemble de haut niveau de la solution sauvegarde de Microsoft cloud intégré qui permet aux clients de sauvegarder leurs données locales vers Azure.

## Quelle est la sauvegarde Azure ?
Sauvegarde Azure est un service Azure multi-avec clients qui vous permet de sauvegarder vos données sur site vers Azure. Il remplace votre local existant ou une solution de sauvegarde hors site par un cloud compétitif fiable, sécurisé et coût en fonction de l'offre. Sauvegarde Azure repose sur une infrastructure de classe mondiale est évolutive, fiable et hautement disponible. À l'aide de cette solution, vous pouvez sauvegarder des données et des applications à partir de leurs serveurs de System Center Data Protection Manager (SCDPM), les serveurs Windows ou les machines clientes Windows. Sauvegarde Azure et SCDPM sont les technologies fondamentales qui composent la solution de sauvegarde intégrée de cloud de Microsoft.

## Point de conception de cloud
Solutions de sauvegarde traditionnelles ont évolué pour traiter de nuage comme un point de terminaison similaire sur disque ou bande. Bien que cette approche est simple et facile à déployer et offre une expérience cohérente, il a des utilisations limitées et ne pas tire pleinement parti de la plate-forme sous-jacente. Cela se traduit par une solution inefficace et coûteuse pour les clients finaux. En traitant Azure comme « simplement en tant que stockage point de terminaison », les solutions de sauvegarde ne peuvent pas tirer parti de la richesse et la puissance de la plateforme de cloud public. Sauvegarde Azure, propose quant à lui, un service qui utilise le cloud construit pour offrir une solution puissante et abordable. Il s'intègre à votre solution de sauvegarde sur site (SCDPM) pour fournir une solution hybride de bout en bout.

Les avantages de cette approche sont :

+ Architecture de stockage cloud efficace qui assure le stockage des données économiques et résilientes

+ Non intrusive, mise à l'échelle du service avec des garanties de haute disponibilité

+ Sauvegarde sur site, hybrides et les déploiements IaaS de manière cohérente

Les fonctionnalités clées de cette solution sont :

1. **Service fiable**: en adoptant Azure Backup, vous obtenez un service de sauvegarde qui est hautement disponible. Le service est multi-avec clients et vous n'avez pas à vous soucier de la gestion de calcul sous-jacent ou stockage.

2. **Stockage fiable**: sauvegarde Azure est basé sur le stockage cloud fiable qui est sauvegardé par SLA élevés. Vous n'avez pas à vous soucier des dépenses de capital ou opération à la gestion du stockage. En outre, vous avez le choix de la sauvegarde pour LRS (stockage localement redondant) ou GRS (géo-réplication) de stockage. Tandis que LRS permet 3 copies des données dans le même géographique qui protège contre les défaillances matérielles locales ; GRS fournit des copies supplémentaires 3 (total de 6 copies) dans un centre de données associés. Cela garantit que vos données de sauvegarde soient hautement disponibles. Même s'il existe un problème grave Azure-au niveau du site, les données de sauvegarde sont sécurisées avec nous.

3. **Secure**: les données de sauvegarde Azure sont chiffrées à la source, durant la transmission et Azure cryptée dans stockées. La clé de chiffrement sont stockée sur la source et jamais transmise ou stockée dans Azure. La clé de chiffrement est nécessaire pour restaurer les données et que le client a un accès complet aux données dans le service.

4. **Protection hors site**: au lieu de payer pour des solutions de sauvegarde sur bande hors site, les clients peuvent sauvegarder vers Azure qui offre une solution incontournable sémantique de type de bande à un coût très bas.

5. **Simplicité**: sauvegarde Azure fournit une interface familière capable d'évoluer pour protéger un déploiement de n'importe quelle taille. Que le service évolue, fonctionnalités, telles que la gestion centralisée vous permettra de gérer votre infrastructure de sauvegarde depuis un emplacement unique.

6. **Rentable**: tarification Azure Backup inclut un frais de gestion des sauvegardes par instance et le coût du stockage (prix de blob de bloc) consommée sur Azure. Contrairement aux autre offre sauvegarde cloud grâce à Azure Backup ne facture pas ses clients pour chaque opération de restauration. En outre, les clients n'êtes pas facturés pour les transferts de données (sortant) de sortie de coûts pendant une opération de restauration.


## Applications et charges de travail qui peuvent être sauvegardés dans Azure
Combiné avec SCDPM Azure Backup peut sauvegarder :

+ Fichiers et des dossiers à partir du client d'entreprise, les ordinateurs de serveur

+ Sauvegardes d'ordinateurs virtuels au niveau de l'hôte d'ordinateurs virtuels Hyper-V de Microsoft

+ Microsoft SQL Server

+ Microsoft SharePoint

+ Microsoft Exchange

## Étapes suivantes
+ Forum aux questions sur le service de sauvegarde Azure sont répertoriée [ici](backup-azure-backup-faq.md).
+ Consultez le [forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=GIT-SubDir-->