<properties
	pageTitle="Télécharger les informations d'identification de coffre de sauvegarde Azure"
	description="Apprenez à utiliser les informations d'identification de coffre pour authentifier votre ordinateur avec le coffre de sauvegarde et le service de sauvegarde Azure"
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
	ms.date="03/27/2015"
	ms.author="prvijay"/>

# À l'aide des informations d'identification de coffre pour s'authentifier auprès du service de sauvegarde Azure

Le serveur local (Windows client ou serveur Windows Server ou SCDPM) doit être authentifié avec un coffre de sauvegarde avant de sauvegarder des données vers Azure. L'authentification s'effectue à l'aide de « archivage des informations d'identification ». Le concept d'informations d'identification de coffre est similaire au concept d'un fichier « paramètres de publication » qui est utilisé dans Azure PowerShell.

## Quel est le fichier d'informations d'identification de coffre ?

Le fichier d'informations d'identification de coffre est un certificat qui est généré par le portail pour chaque coffre de sauvegarde. Le portail télécharge ensuite la clé publique pour le Service de contrôle d'accès (ou ACS). La clé privée du certificat est accessible à l'utilisateur dans le cadre du flux de travail donné comme une entrée dans le workflow de machine d'inscription. Il authentifie l'ordinateur pour envoyer des données de sauvegarde dans un coffre identifié dans le service de sauvegarde Azure.

Il est important d'appeler que les informations d'identification de coffre sont utilisée uniquement pendant le flux de travail d'inscription. Il est responsable de l'utilisateur pour vous assurer que le fichier d'informations d'identification de coffre n'est pas compromis. S'il est compris entre les mains de tout utilisateur non autorisé, le fichier d'informations d'identification de coffre peut servir à inscrire d'autres ordinateurs sur le même coffre. Toutefois, comme les données de sauvegarde sont chiffrées à l'aide d'une phrase secrète qui appartient au client, les données de sauvegarde existantes ne peut pas être compromises. Pour atténuer ce problème, les informations d'identification de coffre a la valeur d'expiration dans 48hrs. Vous pouvez télécharger les informations d'identification de coffre d'un coffre de sauvegarde n'importe quel nombre de fois, mais uniquement le dernier fichier de d'informations d'identification de coffre est applicable pendant le flux de travail d'inscription.

## Téléchargez le fichier d'informations d'identification de coffre

Le fichier d'informations d'identification de coffre est téléchargé via un canal sécurisé à partir du portail Azure. Le service de sauvegarde Azure n'a pas connaissance de la clé privée du certificat et la clé privée n'est pas conservée dans le portail ou le service. Utilisez les étapes suivantes pour télécharger les informations d'identification de coffre sur un ordinateur local.

1.  Connectez-vous à la [portail de gestion](https://manage.windowsazure.com/)
2.  Cliquez sur **Services de récupération** dans le volet de navigation de gauche, sélectionnez le coffre de sauvegarde que vous avez créée. Cliquez sur l'icône du cloud pour accéder à la vue démarrage rapide du coffre de sauvegarde. <br/> ![Aperçu rapide][1]

3.  Dans la page de démarrage rapide, cliquez sur **informations d'identification du coffre de téléchargement**. Le portail génère le fichier d'informations d'identification de coffre qui est disponible au téléchargement. <br/> ![Télécharger][2]

4.  Le portail générera une information d'identification de coffre en combinant le nom du coffre et la date actuelle. Cliquez sur **Enregistrer** pour télécharger les informations d'identification de coffre pour le dossier des téléchargements du compte local, ou sélectionnez Enregistrer sous dans le menu Enregistrer pour spécifier un emplacement pour les informations d'identification de coffre.

## Remarque
+ Depuis mars 2015, les utilisateurs n'ont pas par programmation (par ex: PowerShell) la façon de télécharger des informations d'identification de coffre.

+ Vérifiez que les informations d'identification de coffre est enregistré dans un emplacement accessible à partir de votre ordinateur. S'il est stocké dans un partage de fichiers/SMB, vérifiez les autorisations d'accès.

+ Le fichier d'informations d'identification de coffre est utilisé uniquement pendant le flux de travail d'inscription.

+ Le fichier d'informations d'identification de coffre expire après 48hrs et peut être téléchargé à partir du portail.

+ Reportez-vous à la sauvegarde Azure [FAQ](backup-azure-backup-faq.md) pour toute question sur le flux de travail.


<!--Image references-->
[1]: ./media/backup-azure-backup-download-vc/quickview.png
[2]: ./media/backup-azure-backup-download-vc/downloadvc.png

<!---HONumber=GIT-SubDir--> 