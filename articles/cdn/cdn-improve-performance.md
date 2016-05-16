<properties
	pageTitle="CDN : compression des fichiers pour améliorer les performances"
	description="Vous pouvez améliorer la vitesse de transfert de fichiers et les performances de chargement de page en compressant vos fichiers."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016" 
	ms.author="casoper"/>

# Compression des fichiers pour améliorer les performances

Cette rubrique explique comment améliorer la vitesse de transfert de fichiers et les performances de chargement de page en compressant vos fichiers.

CDN peut prendre en charge la compression de deux façons :

- Vous pouvez activer la compression sur votre serveur d'origine, auquel cas CDN prend en charge la compression par défaut et remet des fichiers compressés aux clients.
- Vous pouvez activer la compression directement sur les serveurs Edge CDN, auquel cas le CDN compresse les fichiers et les envoie aux utilisateurs.

## Activation de la compression

> [AZURE.NOTE] Les niveaux Standard et Premium de CDN fournissent les mêmes fonctionnalités, mais l’interface utilisateur est différente. Pour plus d’informations sur les différences entre les niveaux Standard et Premium de CDN, consultez [Vue d’ensemble du réseau de distribution de contenu (CDN) Azure](cdn-overview.md).

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

3. Activez la compression en cliquant sur la case d’option **Compression activée**. Entrez les types MIME que vous souhaitez compresser sous forme de liste délimitée par des virgules (sans espace) dans la zone de texte **Types de fichiers**.

4. Une fois vos modifications effectuées, cliquez sur le bouton **Mettre à jour**.


## Processus de compression

### Vue d'ensemble

1. Le demandeur envoie une demande de contenu.

2. Un serveur Edge vérifie s’il y a un en-tête **Accept-Encoding**.
	1. S’il y a un en-tête, ce dernier identifie la méthode de compression demandée.
		> [AZURE.NOTE] Les méthodes de compression prises en charge sont **gzip**, **deflate** et **bzip2**.
	2. S'il n’y en a pas, ce type de requête est servi dans un format non compressé.
	
3.	Le POP Edge le plus proche vérifie l’état du cache, la méthode de compression et si sa durée de vie est valide.
	1.	**Absence** du cache : si la version demandée n’est pas mise en cache, la demande est transmise à l’origine.
	2.	**Correspondance** : si la version requise est mise en cache avec la méthode de compression demandée, le serveur Edge fournit immédiatement le contenu compressé au client.
	3.	**Correspondance** du cache : si le fichier est mis en cache avec une méthode de compression différente, le serveur Edge transcode l’élément multimédia selon la méthode de compression demandée.
	4.	**Correspondance** du cache : si le fichier est mis en cache dans un format non compressé, une vérification sera effectuée pour déterminer si la demande est éligible pour la compression du serveur Edge (voir la remarque ci-dessous). Si elle est éligible, le serveur Edge compresse le fichier et le sert au client. Sinon, il retourne le contenu non compressé.
		
> [AZURE.IMPORTANT] Pour être éligible pour la compression, un fichier doit :
>
> - Être supérieur à 128 octets.
> - Être inférieur à 1 Mo.
> - Être un type MIME qui a été [configuré pour la compression](#enabling-compression).

### Tables

Les tables ci-dessous décrivent le comportement de compression du CDN pour chaque scénario.

#### Compression désactivée ou le fichier n’est pas éligible pour la compression

|Format demandé|Fichier mis en cache|Réponse CDN|Remarques|
|----------------|-----------|------------|-----|
|Compressé|Compressé|Compressé|Transcode CDN entre les formats pris en charge|
|Compressé|Non compressé|Non compressé| |	
|Compressé|Non mis en cache|Compressés ou non compressé|Dépend de la réponse d’origine|
|Non compressé|Compressé|Non compressé|CDN revient à l’origine pour la version non compressée|
|Non compressé|Non compressé|Non compressé| |	
|Non compressé|Non mis en cache|Non compressé| |

#### La compression est activée et le fichier est éligible pour la compression

|Format demandé|Fichier mis en cache|Réponse CDN|Remarques|
|----------------|-----------|------------|-----|
|Compressé|Compressé|Compressé|Transcode CDN entre les formats pris en charge|
|Compressé|Non compressé|Compressé|CDN effectue la compression|
|Compressé|Non mis en cache|Compressé|CDN effectue la compression si l’origine renvoie le format non compressé|
|Non compressé|Compressé|Non compressé|CDN effectue la décompression|
|Non compressé|Non compressé|Non compressé| |	
|Non compressé|Non mis en cache|Non compressé| |	


## Remarques

1. Comme lors du déploiement de nouveaux points de terminaison, les modifications de configuration CDN prennent un certain temps à se propager sur le réseau. Dans la plupart des cas, vous verrez que vos modifications s’appliquent dans les 90 minutes. S’il s’agit de la première fois que vous avez configuré la compression pour votre point de terminaison CDN, vous devez envisager d’attendre 1 à 2 heures pour vous assurer que les paramètres de compression ont été transmis aux POP avant de procéder à la résolution des problèmes.
2. Une seule version d'un fichier (compressée ou non compressée) est mise en cache sur le serveur Edge. La demande d’une autre version entraîne un transcodage du contenu par le serveur Edge.
3. Pour les points de terminaison de diffusion en continu CDN de Media Services, la compression est activée par défaut pour les types de contenu suivants : application/vnd.ms-sstr+xml, application/dash+xml,application/vnd.apple.mpegurl, application/f4m+xml. Vous ne pouvez pas activer ou désactiver la compression pour les types mentionnés à l'aide du portail Azure.  
4. Bien que cela soit possible, il n’est pas recommandé d’appliquer la compression aux formats compressés, tels que ZIP, MP3, MP4, JPG, etc.

## Voir aussi
- [Résolution des problèmes de compression des fichiers CDN](cdn-troubleshoot-compression.md)    

<!---HONumber=AcomDC_0504_2016-->