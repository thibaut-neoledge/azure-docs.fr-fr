<properties
	pageTitle="Résoudre les problèmes de suppression de comptes de stockage Azure, de conteneurs ou de disques durs virtuels | Microsoft Azure"
	description="Résoudre les problèmes de suppression de comptes de stockage Azure, de conteneurs ou de disques durs virtuels"
	services="storage"
	documentationCenter=""
	authors="genlin"
	manager="felixwu"
	editor=""
	tags="storage"/>

<tags
	ms.service="storage"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/20/2016"
	ms.author="genli"/>

# Résoudre les problèmes de suppression de comptes de stockage Azure, de conteneurs ou de disques durs virtuels

## Résumé
Vous pouvez recevoir des erreurs quand vous essayez de supprimer le compte de stockage Azure, le conteneur ou le disque dur virtuel dans le [portail Azure](https://portal.azure.com/) ou le [portail Azure Classic](https://manage.windowsazure.com/). Les problèmes peuvent être causés par les situations suivantes :

-	Quand vous supprimez une machine virtuelle, le disque et le disque dur virtuel ne sont pas supprimés automatiquement, ce qui peut être la cause de l’échec de suppression du compte de stockage. Nous ne supprimons pas le disque pour que vous puissiez l’utiliser pour monter une autre machine virtuelle.

-	Il existe toujours un bail sur un disque ou sur l’objet blob sur le disque.

Si le problème lié à Azure n’est pas traité dans cet article, parcourez les forums Azure sur [MSDN et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez publier votre problème sur ces forums ou sur Twitter (@AzureSupport). Vous pouvez également créer une demande de support Azure en sélectionnant **Obtenir une assistance** sur le site du [support Azure](https://azure.microsoft.com/support/options/).

## Résolution :
Pour résoudre les problèmes les plus courants, essayez la méthode suivante :

1. Connectez-vous au [Portail Azure Classic](https://manage.windowsazure.com/).
2. Sélectionnez **MACHINE VIRTUELLE**>**DISQUES**.

	![disk.png](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. Recherchez les disques associés au compte de stockage, au conteneur ou au disque dur virtuel à supprimer. Vérifiez l’emplacement du disque pour y trouver le compte de stockage, le conteneur et le disque dur virtuel associés.

	![location](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. Vérifiez qu’aucune machine virtuelle n’est indiquée dans le champ **Attaché à** des disques, puis supprimez les disques.

 	> [AZURE.NOTE] Si un disque est attaché à une machine virtuelle, vous ne pouvez pas le supprimer. Les disques sont détachés de manière asynchrone d’une machine virtuelle supprimée, l’effacement du champ peut donc se produire quelques minutes après la suppression de la machine virtuelle.

5. Sélectionnez **MACHINE VIRTUELLE**>**IMAGES**, puis supprimez les images associées au compte de stockage, au conteneur ou au disque dur virtuel.

Ensuite, réessayez de supprimer le compte de stockage, le conteneur ou le disque dur virtuel.

> [AZURE.WARNING] Veillez à sauvegarder tout ce que vous souhaitez conserver avant de supprimer le compte. Il n’est pas possible de restaurer un compte de stockage supprimé ou de récupérer son contenu avant la suppression. Ceci vaut également pour toutes les ressources du compte : dès que vous supprimez un disque dur virtuel, un objet blob, une table, une file d’attente ou un fichier, la suppression est irréversible. Vérifiez que la ressource n’est pas en cours d’utilisation.

## Symptôme

La section suivante répertorie les erreurs courantes que vous pouvez recevoir quand vous tentez de supprimer les comptes de stockage Azure, les conteneurs ou les disques durs virtuels, consultez :

### Scénario 1 Impossible de supprimer un compte de stockage

Quand vous essayez de supprimer un compte de stockage que vous n’utilisez plus en accédant au compte de stockage dans le [portail Azure](https://portal.azure.com/) ou le [portail Azure Classic](https://manage.windowsazure.com/), et que vous sélectionnez Supprimer, le message d’erreur suivant peut s’afficher :

**Dans le portail Azure **:

*Échec de la suppression du compte de stockage <vm-storage-account-name>. Impossible de supprimer le compte de stockage <vm-storage-account-name> : « Le compte de stockage <vm-storage-account-name> contient des images et/ou des disques actifs. Supprimez ces images et/ou disques avant de supprimer ce compte de stockage. ».*

**Dans le portail Azure Classic** :

*Le compte de stockage <vm-storage-account-name> contient des images et/ou des disques actifs, par exemple, xxxxxxxxx-xxxxxxxxx-O-209490240936090599. Supprimez ces images et/ou disques avant de supprimer ce compte de stockage.*

Vous pouvez également obtenir cette erreur :

**Dans le portail Azure **:

*Le compte de stockage <vm-storage-account-name> contient 1 ou plusieurs conteneurs avec une image active et/ou des artefacts de disque. Supprimez ces artefacts du dépôt d’images avant de supprimer ce compte de stockage*.

**Dans le portail Azure Classic** :

*Échec de l’envoi Le compte de stockage <vm-storage-account-name> contient 1 ou plusieurs conteneurs avec une image active et/ou des artefacts de disque. Supprimez ces artefacts du dépôt d’images avant de supprimer ce compte de stockage. Quand vous essayez de supprimer un compte de stockage auquel sont associés des disques toujours actifs, un message vous informe que des disques actifs doivent être supprimés*.

### Scénario 2 Impossible de supprimer un conteneur

Quand vous essayez de supprimer le conteneur de stockage, vous pouvez rencontrer l’erreur suivante :

*Échec de la suppression du conteneur de stockage <container name>. Erreur : « Il existe actuellement un bail sur le conteneur et aucun ID de bail n’a été spécifié dans la demande »*.

### Scénario 3 Impossible de supprimer un disque dur virtuel

Après avoir supprimé une machine virtuelle, vous tentez de supprimer les objets blob des disques durs virtuels associés et vous recevez le message ci-dessous :

*Échec de la suppression de l’objet blob « path/XXXXXX-XXXXXX-os-1447379084699.vhd ». Erreur : « Il existe actuellement un bail sur l’objet blob et aucun ID de bail n’a été spécifié dans la demande »*.

## Plus d’informations

Les machines virtuelles V1 qui ont été conservées s’affichent avec l’état arrêté (désalloué) dans le [portail Azure](https://portal.azure.com/) ou le [portail Azure Classic](https://manage.windowsazure.com/).

**Portail Azure Classic** :

![screenshot1](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

**Portail Azure** :

![screenshot2](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)

Un état « Arrêté (désalloué) » libère les ressources de calcul comme le processeur, la mémoire et le réseau, mais les disques sont toujours conservés pour que l’utilisateur puisse recréer rapidement la machine virtuelle si nécessaire. Ces disques sont créés sur les disques durs virtuels sauvegardés par Azure Storage. Le compte de stockage contient ces disques durs virtuels et les disques ont des baux sur ces disques durs virtuels.

## Références

- [Suppression d’un compte de stockage](storage-create-storage-account.md#delete-a-storage-account)
- [How to break the locked lease of blob storage in Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492) (Interruption du bail verrouillé du stockage d’objets blob dans Microsoft Azure (PowerShell))

<!---HONumber=AcomDC_0323_2016-->