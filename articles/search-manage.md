<properties title="Manage your Search service on Microsoft Azure" pageTitle="Manage your Search service on Microsoft Azure" description="Manage your Search service on Microsoft Azure" metaKeywords="" services="" solutions="" documentationCenter="" authors="heidist" videoId="" scriptId="" />

# Gestion de votre service Search sur Microsoft Azure

[WACOM.INCLUDE [Cet article utilise la version préliminaire du portail Azure](../includes/preview-portal-note.md)]

Azure Search est à la fois un service cloud et une API HTTP qu'il est possible d'utiliser dans des applications de recherche personnalisées. Notre service Search fournit le moteur d'analyse de texte avec recherche en texte intégral, des fonctionnalités de recherche avancée, un espace de stockage et une syntaxe de commande d'interrogation.

Cet article vous explique comment administrer un service Search dans Microsoft Azure.

Comme indiqué précédemment, la nouvelle version préliminaire du portail est requise pour les tâches d'administration. Azure Search n'est pas disponible sur les anciennes versions du portail. Une API de gestion sera bientôt disponible pour permettre l'exécution de scripts de tâches de gestion courantes.

<!--TOC-->

-   [Ajout d'un service de recherche à votre abonnement](#sub-1)
-   [Tâches d'administration](#sub-2)
-   [URL du service](#sub-3)
-   [Gestion des clés API](#sub-4)
-   [surveiller l’utilisation des ressources ;](#sub-5)
-   [Augmentation ou réduction d'échelle](#sub-6)
-   [Démarrage ou arrêt du service](#sub-7)

<h2 id="sub-1">Ajout d'un service de recherche à votre abonnement</h2>

En tant qu'administrateur, vous pouvez ajouter le service Search à votre abonnement Azure existant à l'aide de la nouvelle [version préliminaire du portail Azure](https://portal.azure.com). Seuls les administrateurs peuvent ajouter des fonctionnalités à un abonnement. Lors de la configuration de votre service, vous avez le choix entre deux niveaux de tarification.

Les abonnés existants peuvent bénéficier gratuitement d'un service partagé. Celui-ci est recommandé à des fins de formation, pour les tests de validation technique et pour les projets de développement de faible envergure. Le service partagé est limité à 50 Mo de stockage, trois index et un certain nombre de documents (la limite stricte est de 10 000 documents, même si la capacité de 50 Mo n'a pas été atteinte). Les performances ne sont pas garanties avec le service partagé. Dès lors, si vous développez une application de recherche de production, pensez plutôt à utiliser la recherche standard.

La recherche standard est facturable, car vous bénéficiez d'une infrastructure et de ressources dédiées, utilisées uniquement par votre abonnement. La recherche standard est allouée dans des lots de partitions (stockage) et de réplicas (charges de travail de service) définis par l'utilisateur, et facturée par unité de recherche. Vous pouvez faire évoluer les partitions ou les réplicas séparément, en ajoutant des ressources suivant les besoins.

Pour planifier la capacité nécessaire et comprendre l'impact de la facturation, nous vous recommandons les liens suivants :

-   [Limites et contraintes](http://msdn.microsoft.com/en-us/library/dn798934.aspx)
-   [Tarification](http://go.microsoft.com/fwlink/p/?LinkdID=509792)

Lorsque vous êtes prêt à vous inscrire, consultez la rubrique [Configuration de Search dans la version préliminaire du portail Azure](../search-configure/).

<h2 id="sub-2">Tâches d'administration</h2>

La présence de coadministrateurs est possible avec certains services. Cependant, dans le cas d'Azure Search, il ne peut y avoir qu'un seul administrateur par abonnement. Seul un administrateur est habilité à exécuter les tâches décrites dans cette section.
Outre l'ajout du service Search à l'abonnement, l'administrateur est chargé d'effectuer les tâches suivantes :

-   distribution de l'URL de service (définie lors de l'approvisionnement du service) ;
-   gestion et distribution des clés API ;
-   surveiller l’utilisation des ressources ;
-   augmentation ou réduction d'échelle (s'applique uniquement à la recherche standard) ;
-   démarrage ou arrêt du service.

<h2 id="sub-3">URL du service</h2>

L'URL de service est définie comme une propriété fixe lors de la création du service. Elle ne peut pas être modifiée par la suite.

Les développeurs qui conçoivent des applications de recherche doivent connaître l'URL de service des demandes HTTP. Vous pouvez rechercher rapidement cette URL au moyen du tableau de bord des services.

Pour obtenir l'URL de service à partir du tableau de bord des services :

1.  Connectez-vous à la [version préliminaire du portail Azure](https://portal.azure.com).
2.  Cliquez sur **Parcourir** | **Tout** | **Services de recherche**.
3.  Cliquez sur le nom de votre service de recherche pour ouvrir le tableau de bord.
4.  Cliquez sur **PROPRIÉTÉS** pour ouvrir une page de propriétés. L'URL de service figure en haut de la page. Vous pouvez épingler cette page en vue d'y accéder plus rapidement par la suite.

    ![][8]

Il se peut également que les développeurs vous demandent la version de l'API. Sur le plan du codage, l'une des exigences de l'API Azure Search consiste à toujours spécifier la version d'API dans la demande. La raison d'être de cette exigence est de permettre aux développeurs de continuer à utiliser une version précédente, puis de passer à une version ultérieure au moment opportun.

La version d'API n'est pas affichée dans les pages du portail. Il ne s'agit donc pas d'une valeur que vous pouvez fournir. Pour plus d'informations sur les versions actuelles et précédentes de l'API, consultez la page [API REST Azure Search](http://go.microsoft.com/fwlink/p/?LinkdID=509922).

<!---->

<h2 id="sub-4">Gestion des clés API</h2>

Les développeurs qui conçoivent des applications de recherche doivent posséder une clé API afin d'accéder au service Search. Chaque demande HTTP adressée à votre service de recherche a besoin d'une clé API générée spécialement pour votre service. La clé API constitue le seul mécanisme d'authentification auprès de l'URL de votre service de recherche.

Deux types de clés sont utilisés pour accéder à votre service de recherche :

-   Admin (valable pour toute opération)
-   Requête (valable uniquement pour les demandes de requête)

Une clé API Admin est créée avec le service. Il existe une clé primaire et une clé secondaire. Vous pouvez les utiliser de la même manière. Aucune d'elles n'est associée à un niveau d'accès plus élevé ou plus faible, ce qui se révèle particulièrement utile en cas de substitution de clés. Vous pouvez régénérer l'une des clés Admin, mais vous ne pouvez pas augmenter leur nombre total. Il peut y avoir, au maximum, deux clés Admin par service de recherche.

Les clés Requête sont conçues pour être utilisées dans des applications clientes qui appellent directement le service Search. Vous pouvez créer, au maximum, 50 clés de ce type.

Pour obtenir ou régénérer des clés API, ouvrez le tableau de bord des services. Cliquez sur **CLÉS** pour afficher la page de gestion des clés. Les commandes de régénération ou de création de clés figurent en haut de la page.

 ![][9]

<!---->

<h2 id="sub-5">surveiller l’utilisation des ressources</h2>

Dans cette version préliminaire publique, l'analyse des ressources se limite aux informations affichées dans le tableau de bord des services et à quelques mesures que vous pouvez obtenir en interrogeant le service.

Dans la section Utilisation du tableau de bord des services, vous pouvez déterminer rapidement si les niveaux des ressources de partition sont adaptés à votre application.

L'API du service Search vous permet d'obtenir le nombre de documents et d'index. Des limites strictes sont associées à ces valeurs sur la base du niveau de tarification. Pour plus d'informations, consultez [Limites et contraintes](http://msdn.microsoft.com/en-us/library/dn798934.aspx).

-   [Obtenir des statistiques d'index](http://msdn.microsoft.com/en-us/library/dn798942.aspx)
-   [Nombre de documents](http://msdn.microsoft.com/en-us/library/dn798924.aspx)

> [WACOM.NOTE] Il arrive qu'une limite soit surévaluée en raison des options de mise en cache. Lors de l'utilisation du service partagé, par exemple, il se peut que le nombre de documents dépasse la limite stricte fixée à 10 000 documents. Cette surévaluation est temporaire et sera détectée lors de la prochaine vérification de l'application des limites.

<!---->

<h2 id="sub-6">Augmentation ou réduction d'échelle</h2>

Au départ, chaque service de recherche comporte, au minimum, un réplica et une partition. Si vous vous êtes inscrit pour bénéficier de ressources dédiées à l'aide du niveau de tarification Standard, vous pouvez cliquer sur la vignette **METTRE À L'ÉCHELLE** dans le tableau de bord des services pour réajuster le nombre de partitions et de réplicas utilisés par votre service.

Lorsque vous ajoutez une ressource, le service l'utilise automatiquement. Aucune autre action n'est requise de votre part. Il y aura cependant un léger décalage avant que l'effet de la nouvelle ressource ne soit perceptible. L'approvisionnement des ressources supplémentaires demande au moins 15 minutes.

 ![][10]

### Ajout de réplicas

Pour augmenter le nombre de requêtes par seconde (RPS) ou parvenir à une haute disponibilité, il convient d'ajouter des réplicas. Chaque réplica comporte une copie d'un index. L'ajout d'un réplica se traduit donc par un index supplémentaire pouvant être utilisé pour satisfaire les demandes de requête. En règle générale, on estime actuellement qu'il faut au moins 3 réplicas pour disposer d'une haute disponibilité.

Un service de recherche qui comporte davantage de réplicas peut équilibrer la charge des demandes de requête sur un plus grand nombre d'index. Pour un nombre de requêtes donné, le débit sera plus élevé si davantage de copies de l'index sont disponibles pour leur traitement. Si vous constatez une latence des requêtes, la mise en ligne des répliques supplémentaires aura, à n'en pas douter, un effet positif sur les performances.

Bien que l'ajout de réplicas à votre service entraîne une augmentation du débit des requêtes, celui-ci n'est pas exactement multiplié par deux ou par trois. Toutes les applications de recherche sont, en effet, soumises à des facteurs externes susceptibles d'affecter les performances des requêtes. La complexité des requêtes et la latence réseau, notamment, sont deux facteurs qui contribuent à la fluctuation des temps de réponse des requêtes.

### Ajout de partitions

Pour la plupart des applications de service, le nombre de réplicas est plus important que les partitions. La majorité des applications qui font usage de la recherche peuvent, en effet, tenir aisément dans une seule partition capable de prendre en charge jusqu'à 15 millions de documents.

Vous pouvez ajouter des partitions lorsqu'un plus grand nombre de documents est nécessaire. Notez que les partitions sont ajoutées par multiples de 12 (1, 2, 3, 4, 6 ou 12). Il s'agit d'un artefact de partitionnement (ou sharding) ; un index est créé dans 12 fragments (ou shards) qui peuvent tous être stockés dans 1 partition ou répartis équitablement dans 2, 3, 4, 6 ou 12 partitions (un fragment par partition).

### Suppression de réplicas

Après une période de volume de requêtes intense, il est probable que vous supprimiez des réplicas lorsque la charge des requêtes de recherche est revenue à la normale (à la fin d'une période de vente, par exemple).

Pour ce faire, il vous suffit de faire coulisser le curseur des réplicas sur une valeur plus faible. Rien de plus ! La réduction du nombre de réplicas entraîne l'abandon des machines virtuelles dans le centre de données. Désormais, vos opérations de requête et d'ingestion de données s'exécuteront sur un nombre moins élevé de machines virtuelles. La limite minimale est de 1 réplica.

### Suppression de partitions

Contrairement à la suppression de réplicas, qui n'exige aucune opération supplémentaire de votre part, utiliser plus de volume de stockage que la capacité disponible après réduction peut entraîner une surcharge de travail. Supposons, par exemple, que votre solution utilise trois partitions. Dans ce cas, toute tentative de réduction à une ou deux partitions se traduira par une erreur si vous utilisez plus d'espace de stockage que ce qu'il est possible de stocker dans le nombre réduit de partitions. Deux solutions s'offrent alors à vous : soit supprimer des index ou documents au sein d'un index associé afin de libérer de l'espace, soit conserver la configuration actuelle.

Aucune méthode de détection ne vous permet d'identifier les fragments d'index qui sont stockés sur des partitions spécifiques. Chaque partition fournit environ 25 Go de stockage. Vous devrez donc réduire l'espace de stockage à une taille pouvant être prise en charge par le nombre de partitions dont vous disposez. Si vous souhaitez revenir à une seule partition, celle-ci devra contenir les 12 fragments.

Pour faciliter la planification, vous pouvez vérifier le stockage (voir la page [Obtenir des statistiques d'index](http://msdn.microsoft.com/en-us/library/dn798942.aspx)) afin de connaître l'espace réellement utilisé.

<!---->

<h2 id="sub-7">Démarrage ou arrêt du service</h2>

Vous pouvez démarrer, arrêter, voire supprimer le service à l'aide des commandes disponibles dans le tableau de bord des services.

 ![][11]

L'arrêt ou le démarrage du service ne désactive pas la facturation. Pour ne plus être facturé, vous devez supprimer le service. Toute donnée associée à votre service est supprimée lors de la désactivation de ce dernier.

<!--Anchors-->
[Add search service to your subscription]: #sub-1
[Administrative tasks]: #sub-2
[Service URL]: #sub-3
[Manage the api-keys]: #sub-4
[Monitor resource usage]: #sub-5
[Scale up or down]: #sub-6
[Start or Stop the Service]: #sub-7


<!--Image references-->
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png
[11]: ./media/search-manage/Azure-Search-Manage-4-StartStop.png


<!--Link references-->
[Configure search in the Azure Preview Portal]: ../search-configure/
[Azure Search development workflow]: ../search-workflow/
[Create your first azure search solution]: ../search-create-first-solution/

