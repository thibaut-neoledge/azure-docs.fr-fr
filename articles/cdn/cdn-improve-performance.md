<properties 
	pageTitle="CDN : compression des fichiers pour améliorer les performances"
	description="Vous pouvez améliorer la vitesse de transfert de fichiers et les performances de chargement de page en compressant vos fichiers."
	services="cdn"
	documentationCenter=".NET"
	authors="juliako"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="juliako"/>

#Compression des fichiers pour améliorer les performances

Cette rubrique explique comment améliorer la vitesse de transfert de fichiers et les performances de chargement de page en compressant vos fichiers.

CDN peut prendre en charge la compression de deux façons :

- Vous pouvez activer la compression sur votre serveur d'origine, auquel cas CDN prend en charge la compression par défaut et remet des fichiers compressés aux clients. 
- Vous pouvez activer la compression directement sur les serveurs Edge CDN, auquel cas le CDN compresse les fichiers et les envoie aux utilisateurs.

##Définitions

- En-tête de demande **Accept-Encoding** : cet en-tête indique les méthodes de compression prises en charge par un agent utilisateur. Cela doit être inclus dans l'en-tête de demande. Pour plus d'informations, consultez [Définitions de champ d'en-tête](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- **Type de contenu** : liste des types de contenu devant être ajoutés pour la compression. Seuls les fichiers texte sont éligibles pour la compression. Par exemple, texte/brut, texte/html.
- **Méthode de compression** : les méthodes de compression prises en charge sont gzip/deflate/bzip2. Une méthode prise en charge doit être définie dans l'en-tête de demande Accept-Encoding. 
- **État du cache** : l’état du cache identifie si le contenu demandé est déjà mis en cache sur le serveur POP le plus proche du demandeur.  
- **Taille du fichier** : par défaut, la compression prend uniquement en charge les fichiers dont la taille est supérieure à 1 octet et inférieure à 1 Mo.  

##Workflow

1. Le demandeur envoie une demande de contenu.
2. Un serveur Edge vérifie s'il y a un en-tête **Accept-Encoding**.
	1. S’il y a un en-tête, ce dernier identifie la méthode de compression demandée.
	1. S'il n’y en a pas, ce type de requête est servi dans un format non compressé.
3.	Le POP Edge le plus proche vérifie l'état du cache, la méthode de compression et si sa durée de vie est valide.
	1.	Absence du cache : si la version demandée n'est pas mise en cache, la demande est transmise à l'origine.
	2.	Correspondance du cache avec la même méthode de compression : le serveur Edge fournit immédiatement le contenu compressé au client.
	3.	Correspondance du cache avec une méthode de compression différente : le serveur Edge transcode l'élément multimédia selon la méthode de compression demandée. 
	4.	Correspondance du cache sans compression : si la demande initiale a mis la ressource en cache dans un format non compressé, une vérification est effectuée afin de vérifier si la demande est éligible pour la compression du serveur Edge (selon les critères de la section de définition/exigences ci-dessus).
		1.	Si elle est éligible, le serveur Edge compresse le fichier et le sert au client.
		2.	Si elle n’est pas éligible : le serveur Edge fournit immédiatement le contenu non compressé au client. 

![Compression de fichiers](./media/cdn-file-compression/cdn-compress-files.png)

##Considérations 

1. Pour les points de terminaison de diffusion en continu avec Media Services CDN, la compression est activée par défaut pour les types de contenu suivants : application/vnd.ms-sstr+xml, application/dash+xml, application/vnd.apple.mpegurl, application/f4m+xml. Vous ne pouvez pas activer ou désactiver la compression pour les types mentionnés à l'aide du portail Azure.  
2. Une seule version d'un fichier (compressée ou non compressée) est mise en cache sur le serveur Edge. La demande d’une autre version entraîne un transcodage du contenu par le serveur Edge.  

<!---HONumber=September15_HO1-->