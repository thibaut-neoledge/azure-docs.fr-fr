<properties
	pageTitle="Configurer Azure Backup pour préparer la sauvegarde de Windows Server | Microsoft Azure"
	description="Utilisez ce didacticiel pour découvrir comment utiliser le service Backup de l'offre cloud de Microsoft Azure pour sauvegarder Windows Server dans le cloud."
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
	ms.date="08/12/2015"
	ms.author="jimpark"/>

# Configurer Azure Backup pour préparer la sauvegarde de Windows Server

Cet article vous guidera tout au long de l’activation de la fonctionnalité Azure Backup. Pour sauvegarder un serveur Windows Server ou un client Windows sur Azure, vous devez disposer d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
>[AZURE.NOTE]Auparavant, vous aviez besoin de créer ou de vous procurer un certificat X.509 v3 afin d'enregistrer votre serveur de sauvegarde. Les certificats sont toujours pris en charge, mais pour faciliter l’inscription d’un coffre Azure auprès d’un serveur, vous pouvez désormais générer un droit d’informations d’identification de coffre à partir de la page Démarrage rapide.

## Avant de commencer
Pour sauvegarder les fichiers et les données de votre serveur Windows Server vers Azure, commencez par effectuer les opérations suivantes :

- **Créer un coffre de sauvegarde** : créez un coffre dans la console Azure Backup.
- **Télécharger les informations d'identification de l'archivage** : dans Azure Backup, téléchargez dans l'archivage le certificat de gestion que vous avez créé.
- **Installer l’agent Azure Backup et inscrire le serveur** : à partir d’Azure Backup, installez l’agent et inscrivez le serveur dans le coffre de sauvegarde.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## Étapes suivantes
- [Sauvegarder un serveur Windows Server ou un client Windows](backup-azure-backup-windows-server.md)
- [Gérer un serveur Windows Server ou un client Windows](backup-azure-manage-windows-server.md)
- [Restaurer un serveur Windows Server ou un client Windows à partir d’Azure](backup-azure-restore-windows-server.md)
- [Azure Backup - Forum Aux Questions](backup-azure-backup-faq.md)
- [Forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=August15_HO7-->