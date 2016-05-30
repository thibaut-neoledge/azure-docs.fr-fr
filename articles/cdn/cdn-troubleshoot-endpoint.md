<properties
	pageTitle="CDN - Dépannage des points de terminaison de CDN renvoyant des états 404"
	description="Dépannez les codes de réponse 404 avec les points de terminaison de CDN."
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
    
# Dépannage des points de terminaison de CDN renvoyant des états 404

Cet article vous aide à résoudre les problèmes liés aux [points de terminaison de CDN](cdn-create-new-endpoint.md) renvoyant des erreurs 404.

Si vous avez besoin d'aide supplémentaire concernant n'importe quel point de cet article, contactez les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](https://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support**.

## Symptôme

Vous avez créé un profil et un point de terminaison de CDN, mais votre contenu ne semble pas être disponible sur le CDN. Les utilisateurs qui tentent d’accéder à votre contenu par le biais de l’URL du CDN reçoivent des codes d’état HTTP 404.

## Cause :

Il existe plusieurs causes possibles, y compris :

- L’origine du fichier n’est pas visible pour le CDN.
- Le point de terminaison est mal configuré et le CDN effectue donc la recherche au mauvais endroit.
- L’hôte rejette l’en-tête de l’hôte du CDN.
- Le point de terminaison n’a pas eu le temps de se propager dans le CDN.

## Étapes de dépannage

> [AZURE.IMPORTANT] Lorsqu’un point de terminaison de CDN est créé, il n’est pas disponible immédiatement, car la propagation de l’enregistrement dans le CDN peut prendre du temps. Pour les profils du <b>CDN Azure fourni par Akamai</b>, la propagation s’effectue généralement dans un délai d’une minute. Pour les profils du <b>CDN Azure fourni par Verizon</b>, la propagation s’effectue généralement dans un délai de 90 minutes, mais elle peut prendre plus de temps dans certains cas. Si vous suivez les étapes de ce document et que vous obtenez toujours des réponses 404, patientez quelques heures, puis vérifiez à nouveau avant d’ouvrir un ticket de support.

### Vérifier le fichier d’origine

Tout d’abord, nous devons vérifier que le fichier que nous voulons mettre en cache est disponible sur notre origine et est accessible publiquement. Pour cela, le moyen le plus rapide consiste à ouvrir une session de navigateur InPrivate ou Incognito et d’accéder directement au fichier. Il vous suffit de taper ou coller l’URL dans la zone d’adresse et de voir si vous accédez au fichier que vous attendez. Pour cet exemple, je vais utiliser un fichier disponible dans un compte Azure Storage, accessible à l’adresse `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`. Comme vous pouvez le voir, il réussit le test.

![Vous avez réussi !](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [AZURE.WARNING] Même s’il s’agit du moyen le plus rapide et le plus simple de vérifier que votre fichier est disponible publiquement, certaines configurations de réseau de votre organisation pourraient donner l’impression que ce fichier est accessible publiquement, alors qu’il n’est visible que pour les utilisateurs de votre réseau (même s’il est hébergé dans Azure). La meilleure option consiste à utiliser un navigateur externe à partir duquel vous pouvez effectuer le test, par exemple avec un appareil mobile qui n’est pas connecté au réseau de votre organisation ou une machine virtuelle dans Azure.

### Vérifier les paramètres d’origine

Maintenant que nous avons vérifié que le fichier est disponible publiquement sur Internet, nous devons vérifier les paramètres d’origine. Dans le [portail Azure](https://portal.azure.com), accédez à votre profil CDN et cliquez sur le point de terminaison que vous dépannez. Dans le panneau **Point de terminaison** qui s’affiche, cliquez sur l’origine.

![Panneau Point de terminaison avec origine mise en surbrillance](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Le panneau **Origine** s’affiche.

![Panneau Origine](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### Type et nom d’hôte de l’origine

Vérifiez que les champs **Type d’origine** et **Nom d’hôte d’origine** son correctement renseignés. Dans cet exemple, la partie nom d’hôte de l’URL `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt` est `cdndocdemo.blob.core.windows.net`. Comme vous pouvez le voir dans la capture d’écran, les données sont correctes. Pour les origines Azure Storage, d’application web et de service cloud, le champ **Nom d’hôte d’origine** est une liste déroulante. Par conséquent, nous n’avons pas à nous soucier de l’orthographe. Toutefois, si vous utilisez une origine personnalisée, il est *absolument essentiel* d’orthographier correctement votre nom d’hôte.

#### Ports HTTP et HTTPS

À ce stade, vous devez aussi vérifier vos **ports HTTP** et **HTTPS**. Dans la plupart des cas, les ports 80 et 443 sont corrects, et aucune modification n’est nécessaire. Toutefois, si le serveur d’origine est à l’écoute sur un port différent, cela doit être représenté ici. En cas de doute, vérifiez tout simplement l’URL de votre fichier d’origine. Les spécifications HTTP et HTTPS spécifient les ports 80 et 443 par défaut. Dans notre URL `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, aucun port n’est spécifié. Par conséquent, le port 443 est défini par défaut, et nos paramètres sont corrects.

Cependant, imaginons que l’URL du fichier d’origine que vous avez testé précédemment est `http://www.contoso.com:8080/file.txt`. Remarquez la valeur `:8080` à la fin du segment du nom d’hôte. Elle indique au navigateur d’utiliser le port `8080` pour se connecter au serveur web sur `www.contoso.com`. Vous devrez donc entrer 8080 dans le champ **Port HTTP**. Il est important de noter que ces paramètres de port affectent uniquement le port utilisé par le point de terminaison pour récupérer des informations à partir de l’origine.

> [AZURE.NOTE] Les points de terminaison du **CDN Azure fourni par Akamai** n’autorisent pas la plage de ports TCP complète pour les origines. Pour obtenir la liste des ports d’origine non autorisés, consultez [Détails concernant le comportement du CDN Azure fourni par Akamai](cdn-akamai-behavior-details.md).
  
### Vérifier les paramètres de point de terminaison

Dans le panneau **Point de terminaison**, cliquez sur le bouton **Configurer**.

![Panneau Point de terminaison avec bouton Configurer mis en surbrillance](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Le panneau **Configurer** du point de terminaison s’affiche.

![Panneau Configurer](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### Protocoles

Sous **Protocoles**, vérifiez que le protocole utilisé par les clients est sélectionné. Le protocole utilisé par le client sera celui utilisé pour accéder à l’origine. Il est donc important que les ports d’origine soient correctement configurés dans la section précédente. Le point de terminaison écoute uniquement les ports HTTP et HTTPS par défaut (80 et 443), quels que soient les ports d’origine.

Revenons à notre exemple avec `http://www.contoso.com:8080/file.txt`. Vous savez que Contoso a spécifié `8080` comme port HTTP. Cependant, supposons qu’il a aussi spécifié `44300` comme port HTTPS. S’il avait créé un point de terminaison nommé `contoso`, le nom d’hôte du point de terminaison de son CDN serait `contoso.azureedge.net`. Une demande pour `http://contoso.azureedge.net/file.txt` étant une requête HTTP, le point de terminaison utiliserait le protocole HTTP sur le port 8080 pour le récupérer à partir de l’origine. Avec une demande sécurisée sur HTTPS, `https://contoso.azureedge.net/file.txt`, le point de terminaison utiliserait le protocole HTTPS sur le port 44300 lors de la récupération du fichier à partir de l’origine.

#### En-tête de l’hôte d’origine

Le champ **En-tête de l’hôte d’origine** indique la valeur d’en-tête d’hôte envoyée à l’origine avec chaque demande. Dans la plupart des cas, elle doit être identique au **Nom d’hôte d’origine** que nous avons vérifié précédemment. Généralement, une valeur incorrecte dans ce champ ne provoque pas d’état 404. Cela peut cependant provoquer d’autres états 4xx, selon ce qu’attend l’origine.

#### Chemin d’accès d’origine

Enfin, nous devons vérifier le champ **Chemin d’accès d’origine**, qui est vide par défaut. Vous devez l’utiliser uniquement si vous souhaitez limiter les ressources hébergées par l’origine, que vous souhaitez rendre disponibles sur le CDN.

Par exemple, dans mon point de terminaison, je voulais que toutes les ressources de mon compte de stockage soient disponibles. J’ai donc laissé le champ **Chemin d’accès d’origine** vide. Cela signifie qu’une demande adressée à `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` entraîne une connexion de mon point de terminaison à `cdndocdemo.core.windows.net`, qui demande `/publicblob/lorem.txt`. De même, avec une demande pour `https://cdndocdemo.azureedge.net/donotcache/status.png`, le point de terminaison demande `/donotcache/status.png` à l’origine.

Que se passe-t-il si je ne veux pas utiliser le CDN pour chaque chemin d’accès de mon origine ? Disons que je veux uniquement exposer le chemin `publicblob`. Si je saisis */publicblob* dans le champ **Chemin d’accès d’origine**, le point de terminaison insère */publicblob* avant chaque demande adressée à l’origine. Cela signifie que la demande pour `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` prend en fait la partie demande de l’URL `/publicblob/lorem.txt` et ajoute `/publicblob` au début. Cela donne une demande pour `/publicblob/publicblob/lorem.txt` à partir de l’origine. Si ce chemin d’accès ne se résout pas en fichier réel, l’origine retournera un état 404. L’URL correcte pour récupérer lorem.txt dans cet exemple serait en fait `https://cdndocdemo.azureedge.net/lorem.txt`. Notez que nous n’incluons pas du tout le chemin */publicblob*, car la partie demande de l’URL est `/lorem.txt` et le point de terminaison ajoute `/publicblob`. Par conséquent, la demande transmise à l’origine est `/publicblob/lorem.txt`.

<!---HONumber=AcomDC_0518_2016-->