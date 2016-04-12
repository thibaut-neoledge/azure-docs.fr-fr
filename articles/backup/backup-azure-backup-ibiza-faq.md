<properties
   pageTitle="FAQ sur la version préliminaire publique du service Azure Backup| Microsoft Azure"
   description="Cette version de la FAQ prend en charge la version préliminaire publique du service Azure Backup. Réponses aux questions fréquemment posées sur l’agent de sauvegarde, la sauvegarde et la rétention, la récupération, la sécurité et d’autres questions courantes sur la solution Azure Backup."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="solution de sauvegarde ; service de sauvegarde"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="03/30/2016"
	 ms.author="trinadhk; markgal; jimpark;"/>

# Version préliminaire publique du service Azure Backup - FAQ

> [AZURE.SELECTOR]
- [FAQ Azure Backup pour le mode classique](backup-azure-backup-faq.md)
- [FAQ Azure Backup pour le mode ARM](backup-azure-backup-ibiza-faq.md)

Cet article fournit des informations spécifiques à la version préliminaire publique du service Azure Backup. Cet article est mis à jour dès que de nouvelles questions fréquemment posées sont ajoutées et vient compléter la [FAQ Azure Backup](backup-azure-backup-faq). La FAQ Azure Backup fournit l’ensemble des questions et réponses sur le service Azure Backup.

Vous pouvez poser des questions sur Azure Backup dans la section Disques de cet article ou d’un article associé. Vous pouvez également publier des questions sur le service Azure Backup dans le [forum de discussion](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## Nouveautés dans la version préliminaire publique
La version préliminaire publique introduit le coffre Recovery Services et la prise en charge ARM lors de la protection de machines virtuelles Azure. Le coffre Recovery Services constitue la dernière génération de coffres. Il s’agit du coffre utilisé par les services Azure Backup et Azure Site Recovery (ASR). Considérez-le comme le coffre v.2.

## Coffres Azure Backup et Azure Recovery Services

**Q1. Étant donné que les coffres Azure Recovery Services sont de type v.2, les coffres Azure Backup (v.1) sont-ils toujours pris en charge ?** <br/> R1. Oui, les coffres Azure Backup sont toujours pris en charge. Créez des coffres Azure Backup dans le portail classique. Créez des coffres Azure Recovery Services dans le portail Azure.

**Q2. Puis-je migrer un coffre Azure Backup dans un coffre Azure Recovery Services ?** <br/> R2. Malheureusement non, à ce stade vous ne pouvez pas migrer le contenu d’un coffre Azure Backup dans un coffre Azure Recovery Services. Nous travaillons sur l’ajout de cette fonctionnalité, mais elle n’est pas disponible dans la version préliminaire publique.

**Q3. Les coffres Azure Recovery Services prennent-ils en charge les machines virtuelles v.1 ou les machines virtuelles v.2 ?** <br/> R3. Les coffres Azure Recovery Services prennent en charge les machines virtuelles v.1 et v.2. Vous pouvez sauvegarder une machine virtuelle créée dans le portail classique (v.1), ou une machine virtuelle créée dans le portail Azure (v.2) dans un coffre Azure Recovery Services.


## Prise en charge ARM pour les machines virtuelles Azure

**Q1. Existe-t-il des limites à la prise en charge ARM pour les machines virtuelles Azure ?** <br/> R1. Les applets de commande PowerShell pour ARM ne sont pas disponibles actuellement. Vous devez utiliser l’interface utilisateur du portail Azure pour ajouter des ressources à un groupe de ressources.

<!---HONumber=AcomDC_0406_2016-->