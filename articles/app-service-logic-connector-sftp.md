<properties 
	pageTitle="Connecteur SFTP"
	description="Prise en main du connecteur SFTP"
	authors="anuragdalmia" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/31/2015"
	ms.author="adgoda"/>

# Utilisation du connecteur SFTP dans votre application logique #

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. 

Le connecteur SFTP vous permet de déplacer des données vers ou depuis un serveur SFTP. Grâce à ce connecteur, vous pouvez télécharger des fichiers dans les deux sens et obtenir la liste des fichiers sur un serveur SFTP.

## Création d'un connecteur SFTP pour votre application logique ##
Pour utiliser le connecteur SFTP, vous devez d'abord créer une instance de l'application API de ce connecteur. Pour cela, procédez comme suit :

1.	Ouvrez Azure Marketplace à l'aide de l'option + NOUVEAU en bas à gauche du portail Azure.
2.	Accédez à " Web et mobilité > API Apps " et recherchez " Connecteur SFTP ".
3.	Configurez le connecteur SFTP comme suit :
 
	![][1] 
	- **Emplacement** : choisissez l'emplacement géographique dans lequel vous voulez déployer le connecteur.
	- **Abonnement** : choisissez un abonnement dans lequel vous voulez que ce connecteur soit créé.
	- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources dans lequel doit résider le connecteur.
	- **Plan d'hébergement web** : sélectionnez ou créez un plan d'hébergement web.
	- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
	- **Nom** : indiquez le nom de votre connecteur SFTP.
	- **Paramètres du package** 
		- **Adresse du serveur** : indiquez le nom ou l'adresse IP du serveur SFTP.
		- **Accepter toute clé d'hôte de serveur SSH** : détermine si n'importe quelle clé d'hôte publique SSH émanant du serveur doit être acceptée. Si ce paramètre est désactivé, la clé d'hôte est comparée à la clé spécifiée dans la propriété " Empreinte de la clé d'hôte du serveur SSH ".
		- **Clé d'hôte du serveur SSH** : spécifiez l'empreinte de la clé d'hôte publique du serveur SSH.
		- **Dossier racine** : indiquez le chemin du dossier racine.
		- **Chiffrement** : spécifiez le chiffrement.
		- **Port du serveur** : spécifiez le numéro de port du serveur SFTP.
4. Cliquez sur Créer. Un connecteur SFTP est créé.

5. Accédez à l'application API tout juste créée en sélectionnant Parcourir -> API Apps -> <Nom de l'application API créée>. Le composant " Sécurité " apparaît comme non configuré. 

	![][2]
6. Cliquez sur le composant " Sécurité " pour configurer la sécurité (Nom d'utilisateur, Mot de passe, Clé privée, Mot de passe du fichier PPK) du connecteur SFTP. 
Sélectionnez l'onglet d'autorisation " Mot de passe ", " Clé privée " ou " Multifacteur " dans Sécurité et spécifiez les propriétés requises.

	![][3]
	![][4]
	![][5]
6. Une fois la configuration de sécurité enregistrée, vous pouvez créer une application logique dans le même groupe de ressources pour utiliser le connecteur SFTP. 

## Utilisation du connecteur SFTP dans votre application logique ##
Une fois votre application API créée, vous pouvez utiliser le connecteur SFTP comme déclencheur ou action pour votre application logique. Pour cela, vous devez procéder comme suit :

1.	Créez une application logique et choisissez le groupe de ressources qui contient le connecteur SFTP.
 	
	![][6]
2.	Ouvrez " Déclencheurs et actions " pour ouvrir le concepteur d'applications logiques et configurer votre flux. 
 	
	![][7]
3.	Le connecteur SFTP s'affiche dans la section " Applications API dans ce groupe de ressources " de la galerie située à droite.
 
	![][8]
4.	Vous pouvez déposer l'application API du connecteur SFTP dans l'éditeur en cliquant sur " Connecteur SFTP ".
 
	
6.	Vous pouvez maintenant utiliser le connecteur SFTP dans le flux. Vous pouvez utiliser le fichier récupéré du déclencheur SFTP (" TriggerOnFileAvailable ") dans d'autres actions du flux. 

	**Remarque :** le déclencheur SFTP " TriggerOnFileAvailable " supprime le fichier récupéré après le traitement de celui-ci.

8.	Configurez les propriétés d'entrée du déclencheur SFTP comme suit :

	- **Chemin du dossier** : spécifiez le chemin du dossier dans lequel récupérer les fichiers.
	- **type du fichier : texte ou binaire** : sélectionnez le type du fichier.
	- **Masque de fichiers** : spécifiez le masque à appliquer pour récupérer les fichiers. '*' récupère tous les fichiers du dossier spécifié.
	- **Masque de fichiers à exclure** : spécifiez le masque à appliquer pour exclure des fichiers. Si la propriété " Masque de fichiers " est également définie, la propriété " Masque de fichiers à exclure " est appliquée en premier.

 
	![][9] 
	![][10]

7.	De même, vous pouvez utiliser les actions SFTP dans le flux. Vous pouvez utiliser l'action " Charger le fichier " pour charger un fichier sur le serveur SFTP. Configurez les propriétés d'entrée de l'action " Charger le fichier " comme suit :

	- **Contenu** : spécifie le contenu du fichier à télécharger.
	- **Encodage de transfert de contenu** : spécifiez none ou base64.
	- **Chemin du fichier** : spécifiez le chemin du fichier à charger.
	- **Remplacer** : spécifiez " true " pour remplacer le fichier s'il existe déjà.
	- **Ajouter à la fin si existant** : spécifiez " true " ou " false ". Lorsque vous spécifiez " true ", les données sont ajoutées à la fin du fichier (s'il existe). Lorsque vous spécifiez " false ", le fichier est remplacé (s'il existe).
	- **Dossier temporaire** : si cette propriété est définie, l'adaptateur charge le fichier dans le 'Chemin du dossier temporaire'. Une fois le chargement terminé, le fichier est déplacé vers le dossier spécifié dans 'Chemin du dossier'. Le 'Chemin du dossier temporaire' doit résider sur le même disque physique que le 'Chemin du dossier' pour que l'opération de déplacement soit rapide. Le dossier temporaire n'est utilisable que lorsque la propriété 'Ajouter à la fin si existant' est désactivée.

	![][11]
	![][12]





<!-- Image reference -->
[1]: ./media/app-service-logic-connector-sftp/img1.PNG
[2]: ./media/app-service-logic-connector-sftp/img2.PNG
[3]: ./media/app-service-logic-connector-sftp/img3.PNG
[4]: ./media/app-service-logic-connector-sftp/img4.PNG
[5]: ./media/app-service-logic-connector-sftp/img5.PNG
[6]: ./media/app-service-logic-connector-sftp/img6.PNG
[7]: ./media/app-service-logic-connector-sftp/img7.png
[8]: ./media/app-service-logic-connector-sftp/img8.png
[9]: ./media/app-service-logic-connector-sftp/img9.PNG
[10]: ./media/app-service-logic-connector-sftp/img10.PNG
[11]: ./media/app-service-logic-connector-sftp/img11.PNG
[12]: ./media/app-service-logic-connector-sftp/img12.PNG

<!--HONumber=52-->