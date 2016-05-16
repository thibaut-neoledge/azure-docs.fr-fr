<properties
	pageTitle="CDN - Résolution des problèmes de compression des fichiers"
	description="Résolvez les problèmes de compression des fichiers CDN."
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
	ms.date="04/28/2016" 
	ms.author="casoper"/>
    
# Résolution des problèmes de compression des fichiers CDN

Cet article vous aide à résoudre les problèmes de [compression des fichiers CDN](cdn-improve-performance.md).

Si vous avez besoin d'aide supplémentaire concernant n'importe quel point de cet article, contactez les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](https://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support**.

## Symptôme

La compression pour votre point de terminaison est activée, mais les fichiers sont renvoyés non compressés.

## Cause :

Il existe plusieurs causes possibles, y compris :

- Le contenu demandé n’est pas éligible pour la compression.
- La compression n’est pas activée pour le type de fichier demandé.
- La requête HTTP ne comprenait pas un en-tête demandant un type de compression valide.

## Étapes de dépannage

> [AZURE.TIP] Comme lors du déploiement de nouveaux points de terminaison, les modifications de configuration CDN prennent un certain temps à se propager sur le réseau. Dans la plupart des cas, vous verrez que vos modifications s’appliquent dans les 90 minutes. S’il s’agit de la première fois que vous avez configuré la compression pour votre point de terminaison CDN, vous devez envisager d’attendre 1 à 2 heures pour vous assurer que les paramètres de compression ont été transmis aux POP.

### Vérifier la requête

Tout d’abord, effectuez une vérification rapide de l’intégrité de la requête. Vous pouvez utiliser les [outils de développement](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) de votre navigateur pour afficher les requêtes en cours.

- Vérifiez que la requête est envoyée à l’URL de point de terminaison, `<endpointname>.azureedge.net`, et non à l’origine.
- Vérifiez que la requête contient un en-tête **Accept-Encoding** et que la valeur de cet en-tête contient **gzip**, **deflate** ou **bzip2**.

![En-têtes de requête CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### Vérifier les paramètres de compression (profil CDN Standard)

> [AZURE.NOTE] Cette étape s’applique uniquement si votre profil CDN se trouve dans le niveau de tarification **Standard**.

Accédez à votre point de terminaison dans le [portail Azure](https://portal.azure.com) et cliquez sur le bouton **Configurer**.

- Vérifiez que la compression est activée.
- Vérifiez que le type MIME pour le contenu à compresser est inclus dans la liste des formats compressés.

![Paramètres de compression CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### Vérifier les paramètres de compression (profil CDN Premium)

> [AZURE.NOTE] Cette étape s’applique uniquement si votre profil CDN se trouve dans le niveau de tarification **Premium**.

Accédez à votre point de terminaison dans le [portail Azure](https://portal.azure.com) et cliquez sur le bouton **Gérer**. Le portail supplémentaire s’ouvre. Pointez sur l'onglet **HTTP volumineux**, puis pointez sur le menu volant **Paramètres de cache**. Cliquez sur **Compression**.

- Vérifiez que la compression est activée.
- Vérifiez que la liste **Types de fichiers** contient une liste séparée par des virgules (sans espace) des types MIME.
- Vérifiez que le type MIME pour le contenu à compresser est inclus dans la liste des formats compressés.

![Paramètres de compression Premium CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### Vérifier que le contenu est mis en cache

À l’aide des outils de développement de votre navigateur, vérifiez les en-têtes de réponse pour vous assurer que le fichier est mis en cache dans la région où il est demandé.

- Vérifiez l’en-tête de réponse **Server**. L’en-tête doit être au format **Plateforme (ID serveur/POP)**, comme indiqué dans l’exemple ci-dessous.
- Vérifiez l’en-tête de réponse **X-Cache**. L’en-tête doit indiquer **HIT**.  

![En-têtes de réponse CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### Vérifier que le fichier respecte les exigences de taille

Pour être éligible pour la compression, un fichier doit respecter les exigences de taille suivantes :

- Plus de 128 octets.
- Moins de 1 Mo.

<!---HONumber=AcomDC_0504_2016-->