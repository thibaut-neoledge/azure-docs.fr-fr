<properties
	pageTitle="Utilisation d’instantanés incrémentiels pour la sauvegarde et la récupération de machines virtuelles Azure | Microsoft Azure"
	description="Créer une solution personnalisée pour la sauvegarde et la récupération de vos disques de machine virtuelle Azure à l’aide d’instantanés incrémentiels."
	services="storage"
	documentationCenter="na"
	authors="ms-prkhad"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/21/2016"
	ms.author="tamram;prkhad"/>


# Sauvegarder des disques de machine virtuelle Azure avec des instantanés incrémentiels

## Vue d’ensemble 

Azure Storage offre la possibilité de prendre des instantanés d’objets blob. Les instantanés capturent l’état de l’objet blob à un instant précis. Dans cet article, nous allons décrire comment gérer des sauvegardes de disques de machine virtuelle à l’aide d’instantanés. Vous pouvez utiliser cette méthode comme une alternative à Azure Backup et Recovery Service et si vous voulez créer une stratégie de sauvegarde personnalisée pour vos disques de machine virtuelle.

Les disques de machine virtuelle Azure sont stockés en tant qu’objets blob de pages dans Azure Storage. Étant donné que cet article aborde la stratégie de sauvegarde des disques de machine virtuelle, nous faisons référence aux instantanés dans le contexte des objets blob de pages. Pour plus d’informations sur les instantanés, reportez-vous à [Création d’un instantané d’objet blob](https://msdn.microsoft.com/library/azure/hh488361.aspx).

## Qu’est-ce qu’un instantané ?

Un instantané d’objet blob est une version en lecture seule d’un objet blob capturé à un instant donné. Un instantané peut être lu, copié ou supprimé, mais pas modifié. Les instantanés sont une façon de sauvegarder un objet blob à un instant T. Jusqu’à la version REST XX, vous aviez la possibilité de copier des instantanés complets. À partir de la version REST YY, vous pouvez copier des instantanés incrémentiels.

## Copie d’un instantané complet

Les instantanés peuvent être copiés vers un autre compte de stockage en tant qu’objet blob pour conserver des sauvegardes de l’objet blob de base. Vous pouvez également copier un instantané sur l’objet blob de base, ce qui revient à restaurer l’objet blob à une version antérieure. Quand un instantané est copié d’un compte de stockage à un autre, il occupe le même espace que l’objet blob de pages de base dans le compte de stockage cible. Par conséquent, la copie d’instantanés complets d’un compte de stockage à l’autre est lente et consomme également beaucoup d’espace dans le compte de stockage cible.

>[AZURE.NOTE] Si vous copiez l’objet blob de base vers une autre destination, les instantanés de l’objet blob ne sont pas copiés en même temps. De même, si vous remplacez un objet blob de base par une copie, les instantanés associés à l’objet blob de base ne sont pas affectés et restent intacts sous le nom de l’objet blob de base.

### Sauvegarder des disques à l’aide d’instantanés

En tant que stratégie de sauvegarde pour vos disques de machine virtuelle, vous pouvez prendre des instantanés périodiques du disque ou de l’objet blob de pages, et les copier dans un autre compte de stockage à l’aide d’outils tels que l’opération [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou [AzCopy](storage-use-azcopy.md). Vous pouvez copier un instantané sur un objet blob de pages de destination avec un nom différent. L’objet blob de pages de destination qui en découle est modifiable et n’est pas un instantané. Plus loin dans cet article, nous allons décrire les étapes permettant d’effectuer des sauvegardes de disques de machine virtuelle à l’aide d’instantanés.

### Restaurer des disques à l’aide d’instantanés

Pour restaurer votre disque vers une version stable précédente capturée dans l’un des instantanés de sauvegarde, vous pouvez copier un instantané sur l’objet blob de pages de base. Une fois que l’instantané est copié sur l’objet blob de pages de base, il reste, mais sa source est remplacée par une copie qui peut être lue et modifiée. Plus loin dans cet article, nous allons décrire les étapes permettant de restaurer une version précédente de votre disque à partir de son instantané.

### Implémentation de la copie d’un instantané complet

Vous pouvez implémenter la copie d’un instantané complet de la manière suivante :

-   Tout d’abord, prenez un instantané de l’objet blob de base à l’aide de l’opération [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).

-   Ensuite, copiez l’instantané vers un compte de stockage cible à l’aide de [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).

&lt; Extraits de code montrant Create Snapshot et Copy Blob &gt;

## Copie d’un instantané incrémentiel

L’API New Diff Page Ranges est une bien meilleure méthode pour sauvegarder les instantanés de vos objets blob de pages ou vos disques. Cette API renvoie la liste des modifications entre l’objet blob de base et les instantanés. Cela réduit la quantité d’espace de stockage utilisé sur le compte de sauvegarde. L’API prend en charge les objets blob de pages sur le Stockage Premium et le Stockage Standard. À l’aide de cette API, vous pouvez créer des solutions de sauvegarde plus rapides et efficaces pour les machines virtuelles Azure. Cette méthode est disponible à partir de la version REST xx.

La copie d’instantané incrémentiel vous permet de copier d’un compte de stockage à un autre la différence entre

-   l’objet blob de base et son instantané OU

-   les deux instantanés de l’objet blob de base

**Remarque** : Cette fonctionnalité est disponible pour les objets blob de pages Azure Standard et Premium.

Quand votre stratégie de sauvegarde personnalisée utilise des instantanés, la copie des instantanés d’un compte de stockage à un autre peut être très lente et consommer beaucoup d’espace de stockage. Au lieu de copier l’instantané en entier sur un compte de stockage de sauvegarde, vous pouvez écrire la différence dans un objet blob de pages de sauvegarde. De cette façon, le temps de copie et l’espace de stockage des sauvegardes sont sensiblement réduits.

### Implémentation de la copie d’un instantané incrémentiel

Vous pouvez implémenter la copie d’un instantané incrémentiel de la manière suivante :

-   Prenez un instantané de l’objet blob de base à l’aide de [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).

-   Copiez l’objet blob de base vers le compte de stockage cible à l’aide de [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).

-   Obtenez la différence entre l’objet blob de base et l’instantané à l’aide de [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) pour l’objet blob de base. Utilisez le nouveau paramètre **prevsnapshot** pour spécifier la valeur DateTime de l’instantané à comparer. Quand ce paramètre est présent, la réponse REST inclut uniquement les pages qui ont été modifiées entre l’objet blob cible et l’instantané précédent, y compris les pages effacées.

-   Enfin, utilisez [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) pour appliquer ces modifications à l’objet blob de pages de sauvegarde.

&lt; Extraits de code montrant Get Page Ranges et Put Page &gt;

Dans la section suivante, nous allons décrire plus en détail comment conserver des sauvegardes de disques à l’aide de la copie d’instantané incrémentiel

## Scénario

Dans cette section, nous allons décrire un scénario qui implique une stratégie de sauvegarde personnalisée pour les disques de machine virtuelle à l’aide d’instantanés.

Prenez une machine virtuelle Azure de série DS avec un disque P30 de stockage Premium attaché. Le disque P30 appelé mypremiumdisk est stocké dans un compte de stockage Premium appelé mypremiumaccount. Un compte de stockage Standard appelé mybackupstdaccount est utilisé pour stocker la sauvegarde de mypremiumdisk. Nous voulons conserver un instantané de mypremiumdisk toutes les 12 heures.

Pour en savoir plus sur la création d’un compte de stockage et de disques, consultez [À propos des comptes de stockage Azure](storage-create-storage-account.md).

Pour en savoir plus sur la sauvegarde de machines virtuelles Azure, reportez-vous à [Planifier les sauvegardes de machine virtuelle Azure](../backup/backup-azure-vms-introduction.md).

## Étapes pour gérer les sauvegardes d’un disque à l’aide d’instantanés incrémentiels

Les étapes décrites ci-dessous décrivent comment prendre des instantanés de mypremiumdisk et conserver les sauvegardes dans mybackupstdaccount. La sauvegarde est un objet blob de pages standard appelé mybackupstdpageblob, qui reflète à tout moment le même état que celui du dernier instantané de mypremiumdisk.

1.  Effectuez une copie du disque de stockage Premium dans le compte de stockage Standard de sauvegarde. Copiez mypremiumdisk à partir de mypremiumaccount vers mybackupstdaccount en tant que nouvel objet blob de pages appelé mybackupstdpageblob.

2.  Prenez un instantané de mybackupstdpageblob appelé mybackupstdpageblob\_ss1, à l’aide de Snapshot Blob et stockez-le dans mybackupstdaccount.

3.  Pendant la fenêtre de sauvegarde, créez un instantané de mypremiumdisk, mypremiumdisk\_ss1, et stockez-le dans mypremiumaccount.

4.  Obtenez les modifications incrémentielles entre mypremiumdisk et son premier instantané mypremiumdisk\_ss1 à l’aide de GetPageRanges. Écrivez ces modifications incrémentielles dans l’objet blob de pages de sauvegarde mybackupstdpageblob dans mybackupstdaccount. Si des modifications incrémentielles consistent en la suppression de plages, elles doivent être effacées de l’objet blob de pages de sauvegarde. Utilisez Put Page pour écrire des modifications incrémentielles dans l’objet blob de pages de sauvegarde.

5.  Prenez un instantané de l’objet blob de pages de sauvegarde mybackupstdpageblob, appelé mybackupstdpageblob\_ss2, et stockez-le dans mybackupstdaccount. Supprimez l’instantané mypremiumdisk\_ss1 du compte de stockage Premium.

6.  Répétez les étapes 3 à 5 pour chaque fenêtre de sauvegarde. De cette façon, vous pouvez gérer les sauvegardes de mypremiumdisk dans un compte de stockage Standard.

![Sauvegarder un disque à l’aide d’instantanés incrémentiels](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

&lt; Extrait de code montrant Copy Blob, Get Page Ranges, Put Page &gt;

## Procédure de restauration d’un disque à partir d’instantanés

La procédure décrite ci-dessous permet de restaurer le disque Premium mypremiumdisk vers un instantané antérieur à partir du compte de stockage de sauvegarde mybackupstdaccount.

1.  Identifiez le point dans le temps auquel restaurer le disque Premium. Supposons qu’il s’agit de l’instantané mybackupstdpageblob\_ss1, qui est stocké dans le compte de stockage de sauvegarde mybackupstdaccount.

2.  Dans mybackupstdaccount, promouvez l’instantané mybackupstdpageblob\_ss1 en tant que nouvel objet blob de pages de base de sauvegarde mybackupstdpageblobrestored.

3.  Copiez l’objet blob de pages de base de sauvegarde mybackupstdpageblobrestored de mybackupstdaccount vers mypremiumaccount en tant que nouveau disque Premium mypremiumdiskrestored.

4.  Pointez la machine virtuelle de série DS sur le disque restauré mypremiumdiskrestored et détachez l’ancien mypremiumdisk de la machine virtuelle.

5.  Lancez le processus de sauvegarde décrit dans la section précédente pour le disque restauré mypremiumdiskrestored, à l’aide de mybackupstdpageblobrestored en tant qu’objet blob de pages de sauvegarde.

![Restaurer un disque à partir d’instantanés](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

&lt; Extrait de code montrant Copy Blob, Attach disk et Detach disk &gt;

## Voir aussi

- [Création d’un instantané d’objet blob](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [Planification de votre infrastructure de sauvegarde de machines virtuelles](../backup/backup-azure-vms-introduction.md)

<!---HONumber=AcomDC_0330_2016-->