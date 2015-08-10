<properties
   pageTitle="Téléchargement, installation et inscription de l’agent Azure Backup | Microsoft Azure"
   description="Découvrez comment télécharger et installer l’agent Azure Backup, et inscrire ce dernier à l’aide des informations d’identification de coffre"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="07/01/2015"
   ms.author="jimpark"; "aashishr"/>

# Téléchargement, installation et inscription de l’agent Azure Backup
Après avoir créé l’archivage Azure Backup, un agent doit être installé sur chaque serveur local (Windows Server, client Windows ou serveur System Center Data Protection Manager) pour vous permettre de sauvegarder des données et des applications dans Azure. Cet article décrit les étapes requises pour configurer l’agent Azure Backup sur Windows Server ou un ordinateur client Windows.

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com/).

2. Cliquez sur **Recovery Services**, puis sélectionnez le coffre de sauvegarde que vous souhaitez enregistrer avec un serveur. La page de démarrage rapide pour ce coffre de sauvegarde apparaît.

    ![Démarrage rapide](./media/backup-azure-backup-download-register/quickstart.png)

3. Dans la page de démarrage rapide, cliquez sur **Pour Windows Server ou System Center Data Protection Manager ou un client Windows** sous l’option **Télécharger l’agent**. Cliquez sur **Enregistrer** pour le copier sur l’ordinateur local.

    ![Enregistrer l’agent](./media/backup-azure-backup-download-register/agent.png)

4. Une fois l’agent Azure Backup installé, double-cliquez sur MARSAgentInstaller.exe pour lancer l’installation.

5. Choisissez le dossier d’installation et le dossier de travail requis pour l’agent. L’emplacement du cache spécifié doit avoir un espace libre équivalent à au moins 5 % du volume des données de sauvegarde.

6.	Si vous utilisez un serveur proxy pour vous connecter à Internet, dans l’écran **Configuration du proxy**, entrez les détails du serveur proxy. Si vous utilisez un proxy authentifié, entrez les informations de nom d’utilisateur et mot de passe dans cet écran.

7. L’agent Azure Backup installe .NET Framework 4.5 et Windows PowerShell (s’il n’est pas déjà disponible) pour terminer l’installation.

8.	Une fois l’agent installé, cliquez sur le bouton **Procéder à l’inscription** pour continuer le flux de travail.

    ![S’inscrire](./media/backup-azure-backup-download-register/register.png)

9. Dans l’écran d’informations d’identification de l’archivage, recherchez et sélectionnez le fichier d’informations d’identification d’archivage téléchargé précédemment.

    ![Informations d’identification du coffre](./media/backup-azure-backup-download-register/vc.png)

    > [AZURE.NOTE]Le fichier d’informations d’identification d’archivage est valide uniquement pendant 48 heures (à partir de son téléchargement depuis le portail). Si vous rencontrez une erreur dans cet écran (par exemple, « Le fichier d’informations d’identification de coffre fourni a expiré »), connectez-vous au portail Azure et retéléchargez le fichier d’informations d’identification d’archivage.

    Assurez-vous que le fichier d’informations d’identification d’archivage se trouve dans un emplacement accessible par l’application d’installation. Si vous rencontrez des erreurs liées à l’accès, copiez le fichier d’informations d’identification d’archivage dans un emplacement temporaire sur cet ordinateur et recommencez l’opération.

    Si vous rencontrez une erreur indiquant que les informations d’identification d’archivage ne sont pas valides (par exemple, « Les informations d’identification de coffre fournies ne sont pas valides. Soit le fichier est endommagé, soit il ne possède pas les dernières informations d’identification associées au service de récupération », recommencez l’opération après le téléchargement d’un nouveau fichier d’informations d’identification de coffre depuis le portail. Cette erreur se produit généralement si l’utilisateur clique sur l’option Télécharger les informations d’identification de coffre trop rapidement dans le portail Azure. Dans ce cas, seul le deuxième fichier d’informations d’identification de coffre est valide.

10. Dans l’écran **Paramètre de chiffrement**, vous pouvez générer ou fournir une phrase secrète (16 caractères minimum). N’oubliez pas d’enregistrer la phrase secrète dans un emplacement sécurisé.

    ![Chiffrement](./media/backup-azure-backup-download-register/encryption.png)

    > [AZURE.WARNING]Si la phrase secrète est perdue ou oubliée, Microsoft ne peut pas vous aider à récupérer les données de sauvegarde. L’utilisateur final détient la phrase secrète de chiffrement et Microsoft n’a aucune visibilité sur cette dernière. Enregistrez le fichier dans un emplacement sécurisé, car il vous sera demandé pour les opérations de récupération.

11. Quand vous cliquez sur le bouton **Terminer**, l’ordinateur est correctement inscrit dans l’archivage et vous êtes désormais prêt à démarrer la sauvegarde dans Microsoft Azure. Vous pouvez modifier les paramètres spécifiés pendant le flux de travail d’inscription en cliquant sur l’option **Modifier les propriétés** dans le composant logiciel enfichable MMC d’Azure Backup.

    ![Modifier les propriétés](./media/backup-azure-backup-download-register/change.png)

## Étapes suivantes
- Consultez la rubrique [Planification des sauvegardes](backup-azure-backup-and-recover.md) pour plus d'informations sur la sauvegarde des données et des applications vers Azure.

<!-----HONumber=July15_HO5-->