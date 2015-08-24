
<properties 
    pageTitle="Données de profil utilisateur dans Azure RemoteApp"
	description="En savoir plus sur le stockage et l’accès des données utilisateur dans Azure RemoteApp"
	services="remoteapp"
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/12/2015" 
    ms.author="elizapo" />



# Comment Azure RemoteApp enregistre-t-il les paramètres et les données utilisateur ?

Azure RemoteApp enregistre l'identité de l'utilisateur et les personnalisations entre les périphériques et les sessions. Ces données utilisateur sont stockées dans un disque par utilisateur et par collection, appelé disque de profil utilisateur (UPD). Le disque suit l'utilisateur et s’assure que l'utilisateur bénéficie d’une expérience cohérente, quel que soit l’endroit où il se connecte.

Les disques de profil utilisateur sont totalement transparents pour l'utilisateur : les utilisateurs enregistrent des documents dans leur dossier Documents (sans doute sur un lecteur local) et modifient leurs paramètres d'application comme d'habitude. En même temps, tous les paramètres personnels sont conservés lors de la connexion à Azure RemoteApp à partir de n'importe quel périphérique. L'utilisateur voit seulement ses données dans un même endroit.

Chaque UPD offre 50 Go de stockage persistant et contient à la fois les paramètres d'application et les données utilisateur.

Continuez votre lecture pour obtenir des détails spécifiques sur les données de profil utilisateur.

## Comment un administrateur peut-il accéder aux données ?

Si vous avez besoin d’accéder aux données pour l'un de vos utilisateurs (pour la récupération d'urgence ou si l'utilisateur quitte l'entreprise), contactez [Azure RemoteApp](mailto:remoteappforum@microsoft.com) et fournissez les informations d'abonnement de la collection et l'identité de l'utilisateur. L'équipe Azure RemoteApp vous communiquera une URL pour accéder aux données ; à partir de là, vous pouvez parcourir l'emplacement et récupérer les documents ou fichiers dont vous avez besoin.


## Les données sont-elles sauvegardées ?

Oui, nous enregistrons une sauvegarde des données utilisateur par emplacement géographique. Les données sont en lecture seule et accessibles de la même façon que des données standard (contactez Azure RemoteApp si besoin), si le centre de données principal est arrêté.

## Comment les utilisateurs voient-ils l’UPD côté serveur ?

Chaque utilisateur disposera de son propre répertoire sur le serveur, pointant vers leur UPD : c:\\Users\\username.

## Quelle est la meilleure façon d'utiliser Outlook et UPD ?

Azure RemoteApp enregistre l'état d'Outlook (boîtes aux lettres, PST) entre les sessions. Pour cela, le fichier PST doit être stocké dans les données du profil utilisateur (c:\\users<username>). Il s’agit de l'emplacement par défaut des données, par conséquent, tant que vous ne modifiez pas cet emplacement, les données seront conservées entre les sessions.

Nous vous recommandons également d'utiliser le mode « mise en cache » dans Outlook et d'utiliser le mode « serveur/en ligne » pour la recherche.

## Pouvons-nous utiliser des solutions de données partagées ?
Oui, Azure RemoteApp prend en charge l'utilisation de solutions de données partagées, en particulier OneDrive Entreprise et Dropbox. Notez que OneDrive Consumer (la version personnelle) et Box ne sont pas prises en charge.

## Qu'en est-il de la redirection ?
Vous pouvez configurer Azure RemoteApp pour permettre aux utilisateurs d'accéder aux périphériques locaux en configurant une [redirection](remoteapp-redirection.md). Les périphériques locaux pourront ensuite accéder aux données sur l’UPD.

## Puis-je utiliser mon UPD comme partage réseau ?
Non, car l’UPD n'est pas persistant. Un UPD est disponible uniquement lorsque l'utilisateur est connecté activement à Azure RemoteApp.

## Si je supprime un utilisateur d'une collection, son UPD est-il supprimé ?

Non, lorsque vous supprimez un utilisateur, nous ne supprimons pas automatiquement l’UPD : nous stockons les données jusqu'à ce que vous supprimiez la collection. 90 jours après la suppression de la collection, nous supprimons tous les UPD.

Si vous devez supprimer un UPD d'une collection, contactez Azure RemoteApp - nous pouvons supprimer l’UPD de notre côté.

## Puis-je accéder aux UPD de mes utilisateurs (utilisateurs actuels ou supprimés) ?

Oui, si vous contactez [Azure RemoteApp](mailto:remoteappforum@microsoft.com), nous pouvons vous fournir une URL pour accéder aux données. Vous disposez environ de 10 heures pour télécharger les données ou les fichiers de l’UPD avant expiration de l'accès.

## Les UPD sont-ils disponibles hors connexion ?

Nous ne proposons actuellement pas d'accès hors connexion aux UPD au-delà de la fenêtre d'accès de 10 heures décrite ci-dessus. Cela signifie que nous ne sommes actuellement pas en mesure de vous fournir un accès suffisamment long pour effectuer des tâches plus complexes, par exemple l’exécution de logiciels antivirus sur les UPD ou l'accès aux données en vue d’un audit.

## Puis-je désactiver les UPD d’une collection ?

Oui, vous pouvez demander à Azure RemoteApp de désactiver les UPD d’un abonnement, mais vous ne pouvez pas effectuer cette opération vous-même. Cela signifie que les UPD seront désactivés pour toutes les collections de l'abonnement.

## Puis-je empêcher les utilisateurs d’enregistrer des données sur le lecteur système ?

Oui, mais vous devrez configurer cette opération dans l'image du modèle avant de créer la collection. Utilisez les étapes suivantes pour bloquer l’accès au lecteur système :

1. Exécutez **gpedit.msc** sur l'image du modèle.
2. Accédez à **Configuration utilisateur > Modèles d'administration > Composants Windows > Explorer Windows**.
3. Sélectionnez les options suivantes :
	- **Dans Poste de travail, masquer ces lecteurs spécifiés**
	- **Empêcher l'accès aux lecteurs à partir du Poste de travail**

## Puis-je amorcer des UPD ? Je souhaite insérer des données UPD disponibles la première fois que l'utilisateur ouvre une session.

Oui, lorsque vous créez l'image de modèle, vous pouvez ajouter des informations au profil par défaut. Ces informations sont ensuite ajoutées à l’UPD.

## Puis-je modifier la taille de l’UPD selon la quantité de données à stocker ?

Non, tous les UPD ont une capacité de stockage de 50 Go. Si vous souhaitez stocker d’autres quantités de données, essayez ce qui suit :

1. Désactivez les UPD de la collection.
2. Configurez un partage de fichiers accessible aux utilisateurs.
3. Chargez le partage de fichiers à l'aide d'un script de démarrage. Voir ci-dessous pour plus d'informations sur les scripts de démarrage dans Azure RemoteApp.
4. Demandez aux utilisateurs d'enregistrer toutes les données sur le partage de fichiers.

Vous pouvez également utiliser des applications de synchronisation de données comme OneDrive Entreprise.

## Comment puis-je exécuter un script de démarrage dans Azure RemoteApp ?

Si vous souhaitez exécuter un script de démarrage, commencez par créer une tâche planifiée dans l'image de modèle que vous souhaitez utiliser pour la collection. (Procédez ainsi *avant* d’exécuter sysprep.)

![Création d’une tâche système](./media/remoteapp-upd/upd1.png)

![Création d’une tâche système qui s'exécute lorsqu'un utilisateur ouvre une session](./media/remoteapp-upd/upd2.png)

La tâche planifiée lancera le script de démarrage à l'aide des informations d'identification de l'utilisateur. Planifiez l’exécution de la tâche chaque fois qu'un utilisateur ouvre une session.

![Définition du déclencheur pour la tâche sur « à l’ouverture de la session »](./media/remoteapp-upd/upd3.png)

Vous pouvez également utiliser [des scripts de démarrage basés sur une stratégie de groupe](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx).

## Qu’en est-il du placement d’un script de démarrage dans le menu Démarrer ? Cette méthode fonctionne-t-elle ?

En d'autres termes, puis-je créer un fichier .bat qui exécute un script de fenêtre de configuration et l’enregistre dans le dossier c:\\ProgramData\\Microsoft\\Windows\\Start Menu\\Programs\\StartUp, puis exécuter ce script chaque fois qu'un utilisateur démarre une session RemoteApp ?

Non, cette opération n'est pas prise en charge dans Azure RemoteApp, qui utilise RDSH, qui ne prend pas en charge non plus les scripts de démarrage dans le menu Démarrer.

## Puis-je utiliser mstsc.exe (le programme Bureau à distance) pour configurer des scripts d'ouverture de session ?

Non, cette fonctionnalité n’est pas prise en charge dans Azure RemoteApp.

<!---HONumber=August15_HO7-->