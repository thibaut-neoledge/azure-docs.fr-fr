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
	ms.date="05/11/2016"
	ms.author="casoper"/>

# Compression des fichiers pour améliorer les performances

La compression est une méthode simple et efficace qui vise à améliorer la vitesse de transfert des fichiers et à accroître les performances de chargement des pages en réduisant la taille des fichiers avant leur envoi à partir du serveur. Elle permet de réduire les coûts de bande passante et offre à vos utilisateurs davantage de réactivité.

Il existe deux façons d’activer la compression :

- Vous pouvez activer la compression sur votre serveur d’origine. Dans ce cas, le CDN transmet les fichiers compressés et les remet aux clients qui les demandent.
- Vous pouvez activer la compression directement sur les serveurs de périmètre CDN. Dans ce cas, le CDN compresse les fichiers et les envoie aux utilisateurs finals, même s’ils ne sont pas compressés par le serveur d’origine.

> [AZURE.IMPORTANT] Les changements de configuration CDN prennent un certain temps avant de se propager sur le réseau. Pour les profils du <b>CDN Azure fourni par Akamai</b>, la propagation s’effectue généralement en moins d’une minute. Pour les profils du <b>CDN Azure fourni par Verizon</b>, vous verrez généralement vos changements s’appliquer dans un délai de 90 minutes. Si c’est la première fois que vous configurez la compression de votre point de terminaison CDN, patientez 1 à 2 heures pour être certain que les paramètres de compression ont été propagés aux POP avant de chercher à résoudre un problème.

## Activation de la compression

> [AZURE.NOTE] Les niveaux Standard et Premium de CDN fournissent les mêmes fonctionnalités, mais l’interface utilisateur est différente. Pour plus d’informations sur les différences entre les niveaux Standard et Premium de CDN, consultez [Vue d’ensemble du réseau de distribution de contenu (CDN) Azure](cdn-overview.md).

### Niveau standard

> [AZURE.NOTE] Cette section s’applique aux profils du **CDN Azure Standard fourni par Verizon** et du **CDN Azure Standard fourni par Akamai**.

1. Dans le panneau du profil CDN, cliquez sur le point de terminaison CDN que vous souhaitez gérer.

	![Points de terminaison du panneau de profil CDN](./media/cdn-file-compression/cdn-endpoints.png)

	Le panneau du point de terminaison CDN s’ouvre.

2. Cliquez sur le bouton **Configurer**.

	![Bouton de gestion du panneau de profil CDN](./media/cdn-file-compression/cdn-config-btn.png)

	Le panneau de configuration CDN s’ouvre.

3. Activez **Compression**.

	![Options de compression CDN](./media/cdn-file-compression/cdn-compress-standard.png)

4. Utilisez les types par défaut ou modifiez la liste en supprimant ou en ajoutant des types de fichier.
	
	> [AZURE.TIP] Bien que cela soit possible, il n’est pas recommandé d’appliquer la compression aux formats compressés, tels que ZIP, MP3, MP4, JPG, etc.
	
5. Une fois vos modifications effectuées, cliquez sur le bouton **Enregistrer**.

### Niveau Premium

> [AZURE.NOTE] Cette section s’applique aux profils du **CDN Azure CDN Premium fourni par Verizon**.

1. Dans le panneau de profil CDN, cliquez sur le bouton **Gérer**.

	![Bouton de gestion du panneau de profil CDN](./media/cdn-file-compression/cdn-manage-btn.png)

	Le portail de gestion CDN s’ouvre.

2. Pointez sur l’onglet **HTTP volumineux**, puis pointez sur le menu volant **Paramètres de cache**. Cliquez sur **Compression**.

	Les options de compression sont affichées.

	![Compression de fichiers](./media/cdn-file-compression/cdn-compress-files.png)

3. Activez la compression en cliquant sur la case d’option **Compression activée**. Entrez les types MIME que vous souhaitez compresser sous forme de liste délimitée par des virgules (sans espace) dans la zone de texte **Types de fichiers**.
		
	> [AZURE.TIP] Bien que cela soit possible, il n’est pas recommandé d’appliquer la compression aux formats compressés, tels que ZIP, MP3, MP4, JPG, etc.

4. Après avoir apporté vos modifications, cliquez sur le bouton **Mettre à jour**.


## Règles de compression

Ces tableaux décrivent le comportement de compression du CDN Azure pour chaque scénario.

> [AZURE.IMPORTANT] Pour le **CDN Azure fourni par Verizon** (Standard et Premium), seuls les fichiers éligibles sont compressés. Pour être éligible pour la compression, un fichier doit :
>
> - Être supérieur à 128 octets.
> - Être inférieur à 1 Mo.
> 
> Pour le **CDN Azure fourni par Akamai**, tous les fichiers sont éligibles à la compression.
>
> Pour tous les produits Azure CDN, un fichier doit être de type MIME qui a été [configuré pour la compression](#enabling-compression).
>
> Les profils du **CDN Azure fourni par Verizon** (Standard et Premium) prennent en charge l’encodage **gzip**, **deflate** ou **bzip2**. Les profils du **CDN Azure fourni par Akamai** prennent uniquement en charge l’encodage **gzip**.
>
> Les points de terminaison du **CDN Azure fourni par Akamai** demandent toujours des fichiers encodés **gzip** en provenance de l’origine, quelle que soit la demande du client.

### Compression désactivée ou le fichier n’est pas éligible pour la compression

|Format demandé par le client (via l’en-tête Accept-Encoding)|Format de fichier mis en cache|Réponse du CDN au client|Remarques|
|----------------|-----------|------------|-----|
|Compressé|Compressé|Compressé| |
|Compressé|Non compressé|Non compressé| |	
|Compressé|Non mis en cache|Compressés ou non compressé|Dépend de la réponse d’origine|
|Non compressé|Compressé|Non compressé| |
|Non compressé|Non compressé|Non compressé| |	
|Non compressé|Non mis en cache|Non compressé| |

### La compression est activée et le fichier est éligible pour la compression

|Format demandé par le client (via l’en-tête Accept-Encoding)|Format de fichier mis en cache|Réponse du CDN au client|Remarques|
|----------------|-----------|------------|-----|
|Compressé|Compressé|Compressé|Transcode CDN entre les formats pris en charge|
|Compressé|Non compressé|Compressé|CDN effectue la compression|
|Compressé|Non mis en cache|Compressé|CDN effectue la compression si l’origine renvoie le format non compressé|
|Non compressé|Compressé|Non compressé|CDN effectue la décompression|
|Non compressé|Non compressé|Non compressé| |	
|Non compressé|Non mis en cache|Non compressé| |	

## Remarques
1. Pour les points de terminaison de diffusion en continu CDN de Media Services, la compression est activée par défaut pour les types de contenu suivants : application/vnd.ms-sstr+xml, application/dash+xml,application/vnd.apple.mpegurl, application/f4m+xml. Vous ne pouvez pas activer ou désactiver la compression pour les types mentionnés à l'aide du portail Azure.  

## Voir aussi
- [Résolution des problèmes de compression des fichiers CDN](cdn-troubleshoot-compression.md)    

<!---HONumber=AcomDC_0518_2016-->