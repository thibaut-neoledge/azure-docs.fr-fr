<properties
	pageTitle="Déterminer les options de redondance du stockage Azure Backup | Microsoft Azure"
	description="Notez la différence entre le stockage géo-redondant et le stockage localement redondant pour déterminer votre option de redondance de stockage Azure Backup."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="jimpark; markgal"/>


# Options de redondance de stockage pour Azure Backup

Votre entreprise devra déterminer l'option de redondance de stockage appropriée pour le stockage Azure Backup. Si vous utilisez Azure en tant que point de terminaison de stockage de sauvegarde principal (par exemple, vous sauvegardez vos données dans Azure à partir de Windows Server), choisissez l’option de stockage géo-redondant (par défaut). Si vous utilisez Azure comme point de terminaison de stockage de sauvegarde tertiaire (par exemple, vous utilisez SCDPM pour disposer d’une copie de sauvegarde localement et utilisez Azure pour vos besoins de rétention à long terme), choisissez le stockage localement redondant.

## Stockage géo-redondant (GRS)

Le stockage géo-redondant effectue six copies de vos données. Avec le stockage GRS, vos données sont répliquées trois fois dans la région principale et trois fois dans une région secondaire située à des centaines de kilomètres de la région principale, ce qui confère aux données le plus haut niveau de durabilité disponible. En cas de défaillance au niveau de la région principale, en stockant les données dans GRS, Azure Backup garantit que vos données sont conservées dans deux régions distinctes.

## Stockage localement redondant (LRS)

Le stockage localement redondant effectue trois copies de vos données. Le stockage LRS est répliqué trois fois par installation et par région. Ce stockage protège vos données des défaillances matérielles normales, mais pas de la défaillance de l’intégralité d’une installation Azure. Cela vous permet de diminuer les coûts de stockage de données dans Azure tout en fournissant un niveau inférieur de durabilité de vos données pouvant être acceptables pour des copies tertiaires.

Vous pouvez sélectionner l'option adaptée à vos besoins à partir de l’option **Configurer** de votre archivage de sauvegarde.

## Étapes suivantes

- Assurez-vous que votre environnement est [prêt pour la sauvegarde sur un serveur ou un ordinateur client Windows](backup-configure-vault.md)
- Si vous avez encore des questions, consultez le [Forum aux questions Azure Backup](backup-azure-backup-faq.md).
- Rendez-vous sur le [forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0211_2016-->