<properties
	 pageTitle="Utilisation d’Azure CDN | Microsoft Azure"
	 description="Cette rubrique montre comment activer le réseau de distribution de contenu (CDN) pour Azure. Ce didacticiel décrit la création d'un profil CDN et d’un point de terminaison."
	 services="cdn"
	 documentationCenter=""
	 authors="camsoper"
	 manager="erikre"
	 editor=""/>
<tags
	 ms.service="cdn"
	 ms.workload="media"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="07/28/2016" 
	 ms.author="casoper"/>

# Utilisation d’Azure CDN  

Cette rubrique décrit l’activation d’Azure CDN en créant un nouveau profil CDN et un point de terminaison.

>[AZURE.IMPORTANT] Pour une présentation du fonctionnement de CDN, ainsi qu’une liste de fonctionnalités, consultez la [Vue d’ensemble de CDN](./cdn-overview.md).

## Créer un profil CDN

Un profil CDN est une collection de points de terminaison CDN. Chaque profil contient un ou plusieurs points de terminaison CDN. Vous pouvez utiliser plusieurs profils pour organiser vos points de terminaison CDN par domaine Internet, application web ou d'autres critères.

> [AZURE.NOTE] Par défaut, un abonnement Azure unique est limité à huit profils CDN. Chaque profil CDN est limité à dix points de terminaison CDN.
>
> La tarification CDN est appliquée au niveau du profil CDN. Si vous souhaitez utiliser une combinaison de niveaux de tarification Azure CDN, vous aurez besoin de plusieurs profils CDN.

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## Créer un point de terminaison CDN

**Pour créer un point de terminaison CDN**

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre profil CDN. Vous l'avez peut-être épinglé au tableau de bord à l'étape précédente. Dans le cas contraire, vous le trouverez en cliquant sur **Parcourir**, puis **Profils CDN** et en cliquant sur le profil auquel vous voulez ajouter le point de terminaison.

    Le panneau du profil CDN s'affiche.

    ![Profil CDN][cdn-profile-settings]

2. Cliquez sur le bouton **Ajouter un point de terminaison**.

    ![Bouton Ajouter un point de terminaison][cdn-new-endpoint-button]

    Le panneau **Ajouter un point de terminaison** s’affiche.

    ![Panneau Ajouter un point de terminaison][cdn-add-endpoint]

3. Entrez un **nom** pour ce point de terminaison CDN. Ce nom servira à accéder à vos ressources en cache au niveau du domaine `<endpointname>.azureedge.net`.

4. Dans la liste déroulante **Type d’origine**, sélectionnez votre type d’origine. Sélectionnez **Stockage** pour un compte Azure Storage, **Service cloud** pour un service Azure Cloud, **Application web** pour une application web Azure ou **Origine personnalisée** pour toute autre origine de serveur web accessible publiquement (hébergé dans Azure ou ailleurs).

	![Type d'origine CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)
		
5. Dans la liste déroulante **Nom d’hôte d’origine**, sélectionnez ou tapez votre domaine d’origine. La liste déroulante répertorie toutes les origines disponibles du type spécifié à l'étape 4. Si vous avez sélectionné *Origine personnalisée* comme **type d’origine**, tapez le domaine de votre origine personnalisée.

6. Dans la zone de texte **Chemin d’accès d’origine**, entrez le chemin d’accès aux ressources que vous souhaitez mettre en cache ou laissez cette zone vide pour autoriser la mise en cache de n’importe quelle ressource au niveau du domaine spécifié à l’étape 5.

7. Dans **En-tête de l’hôte d’origine**, entrez l’en-tête de l’hôte que le CDN doit envoyer avec chaque requête ou conservez la valeur par défaut.

	> [AZURE.WARNING] Certains types d’origine, tels que Azure Storage et Web Apps, nécessitent l’en-tête de l’hôte pour correspondre au domaine de l’origine. À moins d’avoir une origine nécessitant un en-tête d’hôte différent de son domaine, vous devriez laisser la valeur par défaut.

8. Pour **Protocole** et **Port d’origine**, spécifiez les protocoles et les ports utilisés pour accéder à vos ressources à l’origine. Vous devez sélectionner au moins un protocole (HTTP ou HTTPS).
	
	> [AZURE.NOTE] Le **port d’origine** ne concerne que le port utilisé par le point de terminaison pour récupérer des informations à partir de l’origine. Le point de terminaison ne sera disponible pour les clients que sur les ports HTTP et HTTPS par défaut (80 et 443), quel que soit le **port d’origine**.
	>
	> Les points de terminaison d’**Azure CDN fourni par Akamai** n’autorisent pas la plage de ports TCP complète pour les origines. Pour obtenir la liste des ports d’origine non autorisés, consultez l’article [Azure CDN from Akamai Allowed Origin Ports](https://msdn.microsoft.com/library/mt757337.aspx) (Ports d’origine autorisés du CDN Azure fourni par Akamai).
	>
	> L'accès à du contenu CDN à l'aide du protocole HTTPS présente les contraintes suivantes :
	> 
	> - Vous devez utiliser le certificat SSL fourni par le CDN. Les certificats tiers ne sont pas pris en charge.
	> - Vous devez utiliser le domaine fourni par le CDN (`<endpointname>.azureedge.net`) pour accéder au contenu HTTPS. La prise en charge du protocole HTTPS n'est pas disponible pour les noms de domaines personnalisés (enregistrements CNAME), car le CDN ne prend pas en charge les certificats personnalisés pour l'instant.

9. Cliquez sur le bouton **Ajouter** pour créer le point de terminaison.

10. Une fois le point de terminaison créé, il s'affiche dans la liste des points de terminaison pour le profil. L’affichage sous forme de liste montre l’URL à utiliser pour accéder au contenu mis en cache et le domaine d’origine.

    ![Point de terminaison CDN][cdn-endpoint-success]

    > [AZURE.IMPORTANT] Le point de terminaison ne sera pas disponible immédiatement, car la propagation de l’enregistrement dans le CDN peut prendre du temps. Pour les profils d’<b>Azure CDN fourni par Akamai</b>, la propagation s’effectue généralement dans un délai d’une minute. Pour les profils d’<b>Azure CDN fourni par Verizon</b>, la propagation s’effectue généralement dans un délai de 90 minutes, mais elle peut prendre plus de temps dans certains cas.
	>	 
	> Les utilisateurs qui tentent d’utiliser le nom de domaine CDN avant la propagation de la configuration du point de terminaison aux POP voient s’afficher des codes de réponse HTTP 404. Si plusieurs heures se sont écoulées depuis la création de votre point de terminaison et que vous recevez toujours des réponses 404, consultez [Dépannage des points de terminaison de CDN renvoyant des états 404](cdn-troubleshoot-endpoint.md).


##Voir aussi
- [Contrôle du comportement de mise en cache des demandes avec des chaînes de requête](cdn-query-string.md)
- [Mappage du contenu CDN à un domaine personnalisé](cdn-map-content-to-custom-domain.md)
- [Préchargement d’éléments multimédias sur un point de terminaison CDN Azure](cdn-preload-endpoint.md)
- [Purger un point de terminaison CDN Azure](cdn-purge-endpoint.md)
- [Dépannage des points de terminaison de CDN renvoyant des états 404](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png

<!---HONumber=AcomDC_0803_2016-->