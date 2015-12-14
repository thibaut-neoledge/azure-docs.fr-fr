<properties 
	pageTitle="CDN : compression des fichiers pour améliorer les performances" 
	description="Vous pouvez améliorer la vitesse de transfert de fichiers et les performances de chargement de page en compressant vos fichiers." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>

# Compression des fichiers pour améliorer les performances

Cette rubrique explique comment améliorer la vitesse de transfert de fichiers et les performances de chargement de page en compressant vos fichiers.

CDN peut prendre en charge la compression de deux façons :

- Vous pouvez activer la compression sur votre serveur d'origine, auquel cas CDN prend en charge la compression par défaut et remet des fichiers compressés aux clients. 
- Vous pouvez activer la compression directement sur les serveurs Edge CDN, auquel cas le CDN compresse les fichiers et les envoie aux utilisateurs.

## Activation de la compression

> [AZURE.NOTE]Les niveaux Standard et Premium de CDN fournissent les mêmes fonctionnalités, mais l’interface utilisateur est différente. Pour plus d’informations sur les différences entre les niveaux Standard et Premium de CDN, consultez [Vue d’ensemble du réseau de distribution de contenu (CDN) Azure](cdn-overview.md).

### Niveau standard

1. Dans le panneau du profil CDN, cliquez sur le point de terminaison CDN que vous souhaitez gérer.
	
	![Points de terminaison du panneau de profil CDN](./media/cdn-file-compression/cdn-endpoints.png)

	Le panneau du point de terminaison CDN s’ouvre.

2. Cliquez sur le bouton **Configurer**.

	![Bouton de gestion du panneau de profil CDN](./media/cdn-file-compression/cdn-config-btn.png)
	
	Le panneau de configuration CDN s’ouvre.
	
3. Activez **Compression**.
	
	![Options de compression CDN](./media/cdn-file-compression/cdn-compress-standard.png)
	
4. Utilisez les types par défaut ou modifiez la liste en supprimant ou en ajoutant des types de fichier.

5. Une fois vos modifications effectuées, cliquez sur le bouton **Enregistrer**.

### Niveau Premium

1. Dans le panneau Profil CDN, cliquez sur le bouton **Gérer**.

	![Bouton de gestion du panneau de profil CDN](./media/cdn-file-compression/cdn-manage-btn.png)
	
	Le portail de gestion CDN s’ouvre.
	
2. Pointez sur l’onglet **HTTP volumineux**, puis pointez sur le menu volant **Paramètres de cache**. Cliquez sur **Compression**.
	
	Les options de compression sont affichées.
	
	![Compression de fichiers](./media/cdn-file-compression/cdn-compress-files.png)
	
3. Après avoir modifié la liste des types de fichier, cliquez sur le bouton **Mettre à jour**.


## Règles et processus de compression

1. Le demandeur envoie une demande de contenu.
2. Un serveur Edge vérifie s’il y a un en-tête **Accept-Encoding**.
	1. S’il y a un en-tête, ce dernier identifie la méthode de compression demandée.
	1. S'il n’y en a pas, ce type de requête est servi dans un format non compressé.
3.	Le POP Edge le plus proche vérifie l’état du cache, la méthode de compression et si sa durée de vie est valide.
	1.	Absence du cache : si la version demandée n'est pas mise en cache, la demande est transmise à l'origine.
	2.	Correspondance du cache avec la même méthode de compression : le serveur Edge fournit immédiatement le contenu compressé au client.
	3.	Correspondance du cache avec une méthode de compression différente : le serveur Edge transcode l'élément multimédia selon la méthode de compression demandée. 
	4.	Correspondance du cache sans compression : si la demande initiale a mis la ressource en cache dans un format non compressé, une vérification est effectuée afin de vérifier si la demande est éligible pour la compression du serveur Edge (selon les critères de la section de définition/exigences ci-dessus).
		1.	Si elle est éligible, le serveur Edge compresse le fichier et le sert au client.
		2.	Si elle n’est pas éligible : le serveur Edge fournit immédiatement le contenu non compressé au client. 



## Considérations 

1. Pour les points de terminaison de diffusion en continu CDN de Media Services, la compression est activée par défaut pour les types de contenu suivants : application/vnd.ms-sstr+xml, application/dash+xml,application/vnd.apple.mpegurl, application/f4m+xml. Vous ne pouvez pas activer ou désactiver la compression pour les types mentionnés à l'aide du portail Azure.  
2. Une seule version d'un fichier (compressée ou non compressée) est mise en cache sur le serveur Edge. La demande d’une autre version entraîne un transcodage du contenu par le serveur Edge.  

<!---HONumber=AcomDC_1203_2015-->