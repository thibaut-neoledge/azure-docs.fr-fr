<properties
	pageTitle="Télécharger, installer et inscrire Azure Backup agent"
	description="Découvrez comment et où pour télécharger l'agent de sauvegarde Azure, des étapes d'installation et l'inscription de l'agent de sauvegarde Azure en utilisant les informations d'identification de coffre"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="04/08/2015"
	ms.author="prvijay"/>

# Télécharger, installer et inscrire l'agent de sauvegarde Azure

Après avoir créé le coffre de sauvegarde Azure, un agent doit être installé sur chacun de votre serveur local (Windows Server, Windows client ou serveur de System Center Data Protection Manager) qui vous permet de données de sauvegarde et d'application sur Windows Azure. Cet article décrit les étapes requises pour le programme d'installation de l'agent de sauvegarde Azure sur un serveur Windows ou l'ordinateur du client Windows.

1. Connectez-vous à la [portail de gestion](https://manage.windowsazure.com/)

2. Cliquez sur **Recovery Services**, puis sélectionnez le coffre de sauvegarde que vous souhaitez enregistrer avec un serveur. La page de démarrage rapide de ce coffre de sauvegarde s'affiche. <br/> ![Démarrage rapide][1]

3. Dans la page de démarrage rapide, cliquez sur **de Windows Server ou client Windows ou de System Center Data Protection Manager** sous **Télécharger l'Agent** option. Cliquez sur Enregistrer pour le copier sur l'ordinateur local. <br/> ![Enregistrer l'agent][2]

4. Une fois que l'agent est installé, double-cliquez sur MARSAgentInstaller.exe pour lancer l'installation de l'agent de sauvegarde Azure. Choisissez le dossier d'installation et le dossier de travail requise pour l'agent. L'emplacement du cache spécifié doit avoir un espace libre qui est au moins 5 % des données de sauvegarde.

5.	Si vous utilisez un serveur proxy pour se connecter à internet, dans le **configuration Proxy** écran, entrez les détails du serveur proxy. Si vous utilisez un proxy authentifié, entrez les détails de nom et mot de passe utilisateur dans cet écran.

6.	L'agent de sauvegarde Azure installer .NET Framework 4.5 et Windows PowerShell (s'il est disponible) pour terminer l'installation.

7.	Une fois que l'agent est installé, cliquez sur le **passer à l'enregistrement** pour continuer avec le flux de travail. <br/> ![S’inscrire][3]

8. Dans l'écran d'informations d'identification de coffre, recherchez et sélectionnez le fichier d'informations d'identification de coffre qui a été téléchargé précédemment. <br/> ![Informations d'identification de coffre][4] <br/> <br/>
> [AZURE.NOTE]Le fichier d'informations d'identification de coffre est valide uniquement pour les 48 heures (après l'avoir téléchargée à partir du portail). Si vous rencontrez une erreur dans cet écran (par exemple « fichier d'informations d'identification de coffre fourni a expiré »), connexion au portail Azure et téléchargez le fichier d'informations d'identification de coffre à nouveau.
>
> Assurez-vous que le fichier d'informations d'identification de coffre est disponible dans un emplacement accessible par l'application d'installation. Si vous rencontrez des erreurs connexes d'accès, copiez le fichier d'informations d'identification de coffre vers un emplacement temporaire sur cet ordinateur et recommencez l'opération.
>
> Si vous rencontrez une erreur d'informations d'identification de coffre non valide (par exemple, « credentials coffre non valide fournis ». Le fichier est endommagé ou n'ont pas les dernières informations d'identification d'associées avec le service de récupération », recommencez l'opération après avoir téléchargé un nouveau fichier d'informations d'identification de coffre à partir du portail. Cette erreur se produite généralement si l'utilisateur clique sur l'option d'informations d'identification de coffre téléchargement dans le portail Azure, rapidement. Dans ce cas, seul le fichier d'informations d'identification coffre deuxième est valide.

9. Dans le **paramètre de chiffrement** écran, vous pouvez soit générer un mot de passe ou fournissez une phrase secrète (au minimum 16 caractères) et n'oubliez pas d'enregistrer le mot de passe dans un emplacement sécurisé. <br/> ![Chiffrement][5] <br/> <br/>
> [AZURE.WARNING]Si le mot de passe est perdu ou oublié ; Microsoft ne peut pas vous aider à récupérer les données de sauvegarde. L'utilisateur final possède la phrase secrète de chiffrement et Microsoft n'a aucune visibilité dans la phrase secrète utilisée par l'utilisateur final. Enregistrez le fichier dans un emplacement sécurisé qui serait nécessaire pendant une opération de récupération.

10. Une fois que vous cliquez sur le **Terminer** bouton, l'ordinateur a été correctement inscrit dans le coffre et vous êtes maintenant prêt à démarrer la sauvegarde dans Microsoft Azure. Vous pouvez modifier les paramètres spécifiés pendant le flux de travail d'inscription en cliquant sur le **Modifier les propriétés** option dans le composant logiciel enfichable de mmc sauvegarde Azure. <br/> ![Modifier les propriétés][6]

## Étapes suivantes
+ Reportez-vous à l'installation et la configuration de la sauvegarde Azure [FAQ](backup-azure-backup-faq.md) pour toute question concernant le flux de travail.


<!--Image references-->
[1]: ./media/backup-azure-backup-download-register/quickstart.png
[2]: ./media/backup-azure-backup-download-register/agent.png
[3]: ./media/backup-azure-backup-download-register/register.png
[4]: ./media/backup-azure-backup-download-register/vc.png
[5]: ./media/backup-azure-backup-download-register/encryption.png
[6]: ./media/backup-azure-backup-download-register/change.png

<!---HONumber=GIT-SubDir--> 