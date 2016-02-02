<properties
	pageTitle="Préparer votre environnement à la sauvegarde d’un serveur Windows ou d’un ordinateur client Windows | Microsoft Azure"
	description="Préparez votre environnement à des fenêtres de sauvegarde en créant un coffre de sauvegarde, en téléchargeant les informations d’identification et en installant l’agent de sauvegarde."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="coffre de sauvegarde ; agent de sauvegarde ; fenêtres de sauvegarde ;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/22/2016"
	ms.author="trinadhk; jimpark; markgal"/>


# Préparer votre environnement pour la sauvegarde de machines Windows
Cet article vous aidera à préparer la sauvegarde d’un serveur Windows ou un Client Windows sur Azure. Pour ce faire, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

Si vous l’avez déjà fait, vous pouvez démarrer la [sauvegarde de vos machines Windows](backup-azure-backup-windows-server.md). Sinon, exécutez les étapes ci-dessous pour vérifier que votre environnement est prêt.

>[AZURE.NOTE] Auparavant, vous aviez besoin de créer ou de vous procurer un certificat X.509 v3 afin d'enregistrer votre serveur de sauvegarde. Les certificats sont toujours pris en charge, mais pour faciliter l’inscription d’un coffre Azure auprès d’un serveur, vous pouvez désormais générer un droit d’informations d’identification de coffre à partir de la page Démarrage rapide.

## Avant de commencer
Pour sauvegarder les fichiers et les données de votre serveur Windows Server vers Azure, commencez par effectuer les opérations suivantes :

- **Créer un coffre de sauvegarde** : créez un coffre dans le [portail de gestion Azure Backup](http://manage.windowsazure.com).
- **Télécharger les informations d’identification de coffre** : depuis la page Tableau de bord du coffre de sauvegarde Azure, téléchargez les informations d’identification de coffre qui seront utilisées pour enregistrer l’ordinateur Windows dans le coffre de sauvegarde.
- **Installer l’Agent Azure Backup et inscrire le serveur** : à partir de la page Tableau de bord, cliquez sur le lien pour télécharger l’[agent Azure Backup](http://aka.ms/azurebackup_agent). Installez l’agent et inscrivez le serveur dans le coffre de sauvegarde à l’aide des informations d’identification du coffre.

[AZURE.INCLUDE [backup-create-vault-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## Étapes suivantes
- [Sauvegarder un serveur Windows Server ou un client Windows](backup-azure-backup-windows-server.md)
- [Gérer un serveur Windows Server ou un client Windows](backup-azure-manage-windows-server.md)
- [Restaurer un serveur Windows Server ou un client Windows à partir d’Azure](backup-azure-restore-windows-server.md)
- [Azure Backup - Forum Aux Questions](backup-azure-backup-faq.md)
- [Forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=AcomDC_0128_2016-->