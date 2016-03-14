<properties 
	pageTitle="Gestion de votre service Search sur Microsoft Azure | Microsoft Azure | Service de recherche cloud hébergé" 
	description="Gestion d'Azure Search, un service de recherche cloud hébergé sur Microsoft Azure" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/04/2016" 
	ms.author="heidist"/>

# Gestion de votre service Search sur Microsoft Azure
> [AZURE.SELECTOR]
- [Portail](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [API REST](search-get-started-management-api.md)

Azure Search est un service cloud avec une API HTTP qu'il est possible d'utiliser dans des applications de recherche personnalisées. Notre service Search fournit le moteur d'analyse de texte avec recherche en texte intégral, des fonctionnalités de recherche avancée, un espace de stockage de données de recherche et une syntaxe de commande de requête.

Cet article vous explique comment administrer un service Search dans le [portail Azure](https://portal.azure.com). Vous pouvez également utiliser la nouvelle fonctionnalité d’analyse du trafic des recherches pour comprendre l’activité au niveau de l’index. Pour démarrer, consultez [Activation et utilisation de la fonctionnalité Azure Search du trafic](search-traffic-analytics.md).

Vous pouvez également utiliser l'API REST de gestion. Pour en savoir plus, consultez les articles [Prise en main de l'API REST de gestion Azure Search](search-get-started-management-api.md) et [Informations de référence sur l'API REST de gestion Azure Search](http://msdn.microsoft.com/library/azure/dn832684.aspx).

<a id="sub-1"></a>
## Ajout d'un service de recherche à votre abonnement

En tant qu'administrateur configurant un service Search, l'une de vos premières décisions consiste à choisir un niveau de tarification. Les niveaux de tarification disponibles sont Gratuit et Standard.

Les abonnés existants peuvent bénéficier gratuitement d'un service partagé. Celui-ci est recommandé à des fins de formation, pour les tests de validation technique et pour les projets de développement de faible envergure. Le service partagé est limité à 50 Mo de stockage, trois index et un certain nombre de documents (la limite stricte est de 10 000 documents, même si la capacité de 50 Mo n'a pas été atteinte). Les performances ne sont pas garanties avec le service partagé. Dès lors, si vous développez une application de recherche de production, pensez plutôt à utiliser la recherche standard.

Les recherches de base et standard sont facturables, car vous bénéficiez d'une infrastructure et de ressources dédiées, utilisées uniquement par votre abonnement. Les recherches de base et standard sont allouées dans des lots de partitions (stockage) et de réplicas (charges de travail de service) définis par l'utilisateur, et facturées par unité de recherche. Vous pouvez faire évoluer les partitions ou les réplicas séparément, en ajoutant des ressources suivant les besoins.

Pour planifier la capacité nécessaire et comprendre l'impact de la facturation, nous vous recommandons les liens suivants :

+	[Limites et contraintes](search-limits-quotas-capacity.md)
+	[Détails de la tarification](http://go.microsoft.com/fwlink/p/?LinkdID=509792)

Quand vous êtes prêt à vous inscrire, consultez [Créer un service Search dans le portail](search-create-service-portal.md).

##Analyse de recherche

Vous pouvez activer la collecte de données sur l'activité de recherche des utilisateurs pour comprendre le fonctionnement de votre service de recherche, les termes qui sont utilisés et si ces termes ont renvoyé des résultats. Un pack de contenu Power BI est le meilleur moyen pour analyser et visualiser ces données. La première étape consiste à activer l'analyse du trafic de recherche. Consultez [Analyse de votre trafic Azure Search (en anglais)](https://azure.microsoft.com/blog/analyzing-your-azure-search-traffic/) pour découvrir comment procéder.

<a id="sub-2"></a>
## Tâches d'administration

La présence de coadministrateurs est possible avec certains services. Cependant, dans le cas d'Azure Search, il ne peut y avoir qu'un seul administrateur par abonnement. Seul un administrateur est habilité à exécuter les tâches décrites dans cette section. Outre l'ajout du service Search à l'abonnement, l'administrateur est chargé d'effectuer les tâches suivantes :

+	distribution de l'URL de service (définie lors de l'approvisionnement du service) ;
+	gestion et distribution des clés API ;
+	surveiller l’utilisation des ressources ;
+	augmentation ou réduction d'échelle (s'applique uniquement à la recherche standard) ;
+	démarrage ou arrêt du service.
+	Définition de rôles pour contrôler l'accès des administrateurs

<a id="sub-3"></a>
## URL du service

L'URL de service est définie comme une propriété fixe lors de la création du service. Elle ne peut pas être modifiée par la suite.

Les développeurs qui conçoivent des applications de recherche doivent connaître l'URL de service des demandes HTTP. Vous pouvez rechercher rapidement cette URL au moyen du tableau de bord des services.

Pour obtenir l'URL de service à partir du tableau de bord des services :

1.	Connectez-vous au [portail Azure](https://portal.azure.com).
2.	Cliquez sur **Parcourir** | **Tout** | **Services de recherche**.
3.	Cliquez sur le nom de votre service de recherche pour ouvrir le tableau de bord.
4.	Cliquez sur **PROPRIÉTÉS** pour ouvrir une page de propriétés. L'URL de service figure en haut de la page. Vous pouvez épingler cette page en vue d'y accéder plus rapidement par la suite.

    ![][8]

Il se peut également que les développeurs vous demandent la version de l'API. Sur le plan du codage, l'une des exigences de l'API Azure Search consiste à toujours spécifier la version d'API dans la demande. La raison d'être de cette exigence est de permettre aux développeurs de continuer à utiliser une version précédente, puis de passer à une version ultérieure au moment opportun.

La version d'API n'est pas affichée dans les pages du portail. Il ne s'agit donc pas d'une valeur que vous pouvez fournir. Pour plus d'informations sur les versions actuelles et précédentes de l'API, consultez la page [API REST Azure Search](http://go.microsoft.com/fwlink/p/?LinkdID=509922).


<a id="sub-4"></a>
## Gestion des clés API

Les développeurs qui conçoivent des applications de recherche doivent posséder une clé API afin d'accéder au service Search. Chaque demande HTTP adressée à votre service de recherche a besoin d'une clé API générée spécialement pour votre service. La clé API constitue le seul mécanisme d'authentification auprès de l'URL de votre service de recherche.

Deux types de clés sont utilisés pour accéder à votre service de recherche :

+	Admin (valable pour toute opération)
+	Requête (valable uniquement pour les demandes de requête)

Une clé API Admin est créée avec le service. Il existe une clé primaire et une clé secondaire. Vous pouvez les utiliser de la même manière. Aucune d'elles n'est associée à un niveau d'accès plus élevé ou plus faible, ce qui se révèle particulièrement utile en cas de substitution de clés. Vous pouvez régénérer l'une des clés Admin, mais vous ne pouvez pas augmenter leur nombre total. Il peut y avoir, au maximum, deux clés Admin par service de recherche.

Les clés Requête sont conçues pour être utilisées dans des applications clientes qui appellent directement le service Search. Vous pouvez créer, au maximum, 50 clés de ce type.

Pour obtenir ou régénérer des clés API, ouvrez le tableau de bord des services. Cliquez sur **CLÉS** pour afficher la page de gestion des clés. Les commandes de régénération ou de création de clés figurent en haut de la page.

 ![][9]


<a id="sub-5"></a>
## surveiller l’utilisation des ressources ;

Dans cette version préliminaire publique, l'analyse des ressources se limite aux informations affichées dans le tableau de bord des services et à quelques mesures que vous pouvez obtenir en interrogeant le service.

Dans la section Utilisation du tableau de bord des services, vous pouvez déterminer rapidement si les niveaux des ressources de partition sont adaptés à votre application.

L'API du service Search vous permet d'obtenir le nombre de documents et d'index. Des limites strictes sont associées à ces valeurs sur la base du niveau de tarification. Pour plus d’informations, consultez [Limites de service Search](search-limits-quotas-capacity.md).

+	[Obtention de statistiques d'index](http://msdn.microsoft.com/library/dn798942.aspx)
+	[Nombre de documents](http://msdn.microsoft.com/library/dn798924.aspx)

> [AZURE.NOTE] Il arrive qu'une limite soit surévaluée en raison des options de mise en cache. Lors de l'utilisation du service partagé, par exemple, il se peut que le nombre de documents dépasse la limite stricte fixée à 10 000 documents. Cette surévaluation est temporaire et sera détectée lors de la prochaine vérification de l'application des limites.


<a id="sub-6"></a>
## Augmentation ou réduction d'échelle

Au départ, chaque service de recherche comporte, au minimum, un réplica et une partition. Si vous vous êtes inscrit pour bénéficier de ressources dédiées à l'aide des niveaux de tarification [De base et Standard](search-limits-quotas-capacity.md), vous pouvez cliquer sur la vignette **SCALE** (MISE À L'ÉCHELLE) sur le tableau de bord des services pour réajuster le nombre de partitions et de réplicas utilisés par votre service.

Lorsque vous ajoutez des capacités à travers l’une des ressources, le service les utilise automatiquement. Aucune autre action n'est requise de votre part. Il y aura cependant un léger décalage avant que l'effet de la nouvelle ressource ne soit perceptible. L'approvisionnement des ressources supplémentaires demande au moins 15 minutes.

 ![][10]

### Ajout de réplicas

Pour augmenter le nombre de requêtes par seconde (RPS) ou parvenir à une haute disponibilité, il convient d'ajouter des réplicas. Chaque réplica comporte une copie d'un index. L'ajout d'un réplica se traduit donc par un index supplémentaire pouvant être utilisé pour satisfaire les demandes de requête. En règle générale, on estime actuellement qu'il faut au moins 3 réplicas pour disposer d'une haute disponibilité (consultez [Planification de la capacité](search-capacity-planning.md) pour plus de détails).

Un service de recherche qui comporte davantage de réplicas peut équilibrer la charge des demandes de requête sur un plus grand nombre d'index. Pour un nombre de requêtes donné, le débit sera plus élevé si davantage de copies de l'index sont disponibles pour leur traitement. Si vous constatez une latence des requêtes, la mise en ligne des répliques supplémentaires aura, à n'en pas douter, un effet positif sur les performances.

Bien que l'ajout de réplicas à votre service entraîne une augmentation du débit des requêtes, celui-ci n'est pas exactement multiplié par deux ou par trois. Toutes les applications de recherche sont, en effet, soumises à des facteurs externes susceptibles d'affecter les performances des requêtes. La complexité des requêtes et la latence réseau, notamment, sont deux facteurs qui contribuent à la fluctuation des temps de réponse des requêtes.

### Ajout de partitions

Pour la plupart des applications de service, le nombre de réplicas est plus important que les partitions. La majorité des applications qui font usage de la recherche peuvent, en effet, tenir aisément dans une seule partition capable de prendre en charge jusqu'à 15 millions de documents.

Vous pouvez ajouter des partitions lorsqu'un plus grand nombre de documents est nécessaire si vous êtes inscrit au service Standard. Le niveau De base ne fournit pas d’autres partitions.

Au niveau Standard, les partitions sont ajoutées par multiples de 12 (notamment 1, 2, 3, 4, 6 ou 12). Il s'agit d'un artefact de partitionnement (ou sharding) ; un index est créé dans 12 fragments (ou shards) qui peuvent tous être stockés dans 1 partition ou répartis équitablement dans 2, 3, 4, 6 ou 12 partitions (un fragment par partition).

### Suppression de réplicas

Après une période de volume de requêtes intense, il est probable que vous supprimiez des réplicas lorsque la charge des requêtes de recherche est revenue à la normale (à la fin d'une période de vente, par exemple).

Pour ce faire, il vous suffit de faire coulisser le curseur des réplicas sur une valeur plus faible. Rien de plus ! La réduction du nombre de réplicas entraîne l'abandon des machines virtuelles dans le centre de données. Désormais, vos opérations de requête et d'ingestion de données s'exécuteront sur un nombre moins élevé de machines virtuelles. La limite minimale est de 1 réplica.

### Suppression de partitions

Contrairement à la suppression de réplicas, qui n'exige aucune opération supplémentaire de votre part, utiliser plus de volume de stockage que la capacité disponible après réduction peut entraîner une surcharge de travail. Supposons, par exemple, que votre solution utilise trois partitions. Dans ce cas, toute tentative de réduction à une ou deux partitions se traduira par une erreur si vous utilisez plus d'espace de stockage que ce qu'il est possible de stocker dans le nombre réduit de partitions. Deux solutions s'offrent alors à vous : soit supprimer des index ou documents au sein d'un index associé afin de libérer de l'espace, soit conserver la configuration actuelle.

Aucune méthode de détection ne vous permet d'identifier les fragments d'index qui sont stockés sur des partitions spécifiques. Chaque partition fournit environ 25 Go de stockage. Vous devrez donc réduire l'espace de stockage à une taille pouvant être prise en charge par le nombre de partitions dont vous disposez. Si vous souhaitez revenir à une seule partition, celle-ci devra contenir les 12 fragments.

Pour faciliter la planification, vous pouvez vérifier le stockage (voir la page [Obtenir des statistiques d'index](http://msdn.microsoft.com/library/dn798942.aspx)) afin de connaître l'espace réellement utilisé.

### Meilleures pratiques pour le déploiement de service et de mise à l'échelle dans plusieurs centres de données (vidéo)

> [AZURE.VIDEO azurecon-2015-azure-search-best-practices-for-web-and-mobile-applications]

<a id="sub-7"></a>
## Démarrage ou arrêt du service

Vous pouvez démarrer, arrêter, voire supprimer le service à l'aide des commandes disponibles dans le tableau de bord des services.

 ![][11]


L'arrêt ou le démarrage du service ne désactive pas la facturation. Pour ne plus être facturé, vous devez supprimer le service. Toute donnée associée à votre service est supprimée lors de la désactivation de ce dernier.

<a id="sub-8"></a>
## Définition de rôles lors de l'accès des administrateurs

Azure offre un modèle d’autorisation par rôle global pour tous les services gérés via le portail ou dans l’API du gestionnaire de ressources Azure si vous utilisez un outil d’administration personnalisé. Les rôles Propriétaire, Collaborateur et Lecteur définissent le niveau d'administration des services pour les utilisateurs, les groupes et les principaux de sécurité Active Directory que vous assignez à chaque rôle. Pour en savoir plus sur l’appartenance à un rôle, consultez la page [Contrôle d’accès en fonction du rôle dans le portail Azure Classic](../active-directory/role-based-access-control-configure.md).

Dans Azure Search, les contrôles d'accès en fonction du rôle déterminent les tâches d'administration suivantes :


Rôle|Task
---|---
Propriétaire|Démarrage, arrêt ou suppression du service.<p>Génération et affichage des clés d’administration et de requête.<p>Affichage de l’état du service, y compris le nombre d’index, les noms d’index, le nombre de documents et la taille de stockage.<p>Ajout ou suppression d’appartenance à un rôle (seul un Propriétaire peut gérer l’appartenance à un rôle).<p>Les administrateurs d’abonnement et de service appartiennent automatiquement au rôle Propriétaire.
Collaborateur|Même niveau d'accès que le Propriétaire, à l'exception de la gestion des rôles. Par exemple, un Collaborateur peut visualiser et régénérer `api-key`, mais il ne peut pas modifier les appartenances aux rôles.
Lecteur|Affichage de l'état du service et des clés Requête. Les membres de ce rôle ne peuvent ni démarrer ou arrêter un service, ni afficher des clés Admin.

Notez que les rôles n'accordent pas de droits d'accès au point de terminaison de service. Les opérations du service Search telles que la gestion ou le remplissage d'index, tout comme les requêtes de données de recherche, sont contrôlées via des clés api, et non par des rôles. Pour en savoir plus, consultez la section « Autorisation pour les opérations de gestion et les opérations de données » de la page [Contrôle d'accès en fonction du rôle dans le portail Azure](../active-directory/role-based-access-control-configure.md).

Les rôles offrent un contrôle d'accès après la création du service. Seuls les responsables d'abonnement peuvent ajouter un service Search à un abonnement.

<!--Anchors-->
[Add search service to your subscription]: #sub-1
[Administrative tasks]: #sub-2
[Service URL]: #sub-3
[Manage the api-keys]: #sub-4
[Monitor resource usage]: #sub-5
[Scale up or down]: #sub-6
[Start or Stop the Service]: #sub-7
[Set roles to control administrative access]: #sub-8

<!--Image references-->
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png
[11]: ./media/search-manage/Azure-Search-Manage-4-StartStop.png


 

<!---HONumber=AcomDC_0302_2016-->