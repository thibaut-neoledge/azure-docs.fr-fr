---
title: "Aide relative au réseau de distribution de contenu (CDN) | Microsoft Docs"
description: "Instructions relatives au réseau de distribution contenu (CDN) pour la distribution de contenu d’une bande passante élevée hébergé dans Azure."
services: cdn
documentationcenter: na
author: dragon119
manager: christb
editor: 
tags: 
ms.assetid: 57df0e00-d540-46e2-930e-f800c2301bf4
ms.service: best-practice
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: masashin
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ea811d7315298132a45a8d1873da8a2bf5600c1c


---
# <a name="content-delivery-network-cdn-guidance"></a>Aide relative au réseau de distribution de contenu (CDN)
[!INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

## <a name="overview"></a>Vue d'ensemble
Microsoft Azure Content Delivery Network (CDN) offre aux développeurs une solution globale pour fournir du contenu de large bande passante hébergé dans Azure ou ailleurs. Le réseau de diffusion de contenu (CDN) vous permet de mettre en cache des objets disponibles publiquement, chargés à partir d’un stockage d’objets blob Azure, d’une application web, d’une machine virtuelle, d’un dossier d’application ou d’un autre emplacement HTTP/HTTPS. Le cache du CDN peut être maintenu dans des emplacements stratégiques afin d’offrir une bande passante maximale pour la distribution de contenu aux utilisateurs. Le CDN est généralement utilisé pour distribuer du contenu statique tel que des images, des feuilles de style, des documents, des fichiers, des scripts côté client et des pages HTML.

Vous pouvez également utiliser le CDN en tant que cache pour servir du contenu dynamique, tel qu’un rapport au format PDF ou un graphique basé sur des entrées spécifiées. Si les mêmes valeurs d’entrée sont fournies par différents utilisateurs, le résultat doit être identique.

Les principaux avantages de l’utilisation du CDN sont une latence plus faible et une distribution plus rapide de contenu aux utilisateurs, quel que soit leur emplacement géographique par rapport au centre de données où l’application est hébergée.  

![Diagramme CDN](./media/best-practices-cdn/CDN.png)

L’utilisation du CDN doit également contribuer à réduire la charge sur l’application, car celle-ci est libérée du traitement requis pour accéder au contenu et le distribuer. Cette réduction de charge peut contribuer à augmenter les performances et l’extensibilité de l’application, tout en minimisant les coûts d’hébergement par une réduction des ressources de traitement requises pour atteindre un certain niveau de performances et de disponibilité.

## <a name="how-and-why-a-cdn-is-used"></a>Fonctionnement et raisons de l’utilisation d’un CDN
Un CDN est généralement utilisé dans les cas suivants :  

* La distribution de ressources statiques pour des applications clientes, souvent depuis un site web. Il peut s’agir d’images, de feuilles de style, de documents, de fichiers, de scripts côté client, de pages HTML, de fragments HTML ou de tout autre contenu que le serveur n’a pas besoin de modifier à chaque demande. L’application peut créer des éléments au cours de son exécution et les rendre disponibles au CDN (par exemple, en créant une liste de titres d’information actuels), mais elle ne le fait pas pour chaque demande.
* La distribution de contenu public statique et partagé à des appareils tels que des téléphones mobiles et des tablettes. L’application elle-même est un service web qui offre une API pour des clients s’exécutant sur divers appareils. Le CDN peut également fournir des jeux de données statiques (via le service web), que les clients peuvent utiliser, par exemple, pour générer l’interface utilisateur du client. Par exemple, le CDN peut permettre de distribuer des documents JSON ou XML.
* La mise à disposition de sites web entiers qui se composent uniquement de contenu statique public pour les clients, sans nécessiter de ressources de calcul dédiées.
* La diffusion en continu de fichiers vidéo à la demande pour le client. La vidéo bénéficie d’une latence faible et d’une connectivité fiable disponible depuis les centres de données répartis dans le monde qui offrent des connexions au CDN.
* L’amélioration générale de l’expérience des utilisateurs, en particulier de ceux qui se trouvent loin du centre de données hébergeant l’application. Autrement, ces utilisateurs peuvent pâtir d’une latence plus élevée. Une grande partie de la taille totale du contenu d’une application web est souvent statique et l’utilisation d’un CDN peut aider à maintenir la performance ainsi que l’expérience utilisateur générale tout en éliminant la contrainte du déploiement de l’application sur plusieurs centres de données.
* La gestion de la charge croissante pesant sur les applications qui prennent en charge les solutions IoT (Internet of Things, Internet des objets). Le nombre considérable de ces appareils et équipements pourrait facilement déborder une application si celle-ci devait traiter les messages de diffusion et gérer la distribution des mises à jour de microprogrammes directement à chaque service.
* La gestion des fluctuations de la demande sans que l’application doive être mise à l’échelle, évitant ainsi une augmentation des coûts d’exécution. Par exemple, quand une mise à jour de système d’exploitation est publiée pour un appareil tel qu’un modèle spécifique de routeur, ou pour un bien de consommation tel qu’un téléviseur connecté, la demande croît fortement, car des millions d’utilisateurs et d’appareils télécharge la mise à jour sur une brève période.

La liste suivante répertorie des exemples de temps médian jusqu’au premier octet dans différentes zones géographiques. Le rôle web cible est déployé sur Azure dans l’ouest des États-Unis. Il existe un lien étroit entre la forte stimulation due au CDN et la proximité d’un nœud du CDN. Pour obtenir la liste complète des emplacements de nœuds CDN Azure, consultez [Emplacements des nœuds du réseau de distribution de contenu (CDN) Azure](cdn/cdn-pop-locations.md).

|  | Temps (en ms) jusqu’au premier octet (origine) | Temps (en ms) jusqu’au premier octet (CDN) | % d’amélioration du temps CDN |
| --- | --- | --- | --- |
| \*San Jose, CA |47,5 |46,5 |2 % |
| \*\*Dulles, VA |109 |40,5 |169 % |
| Buenos Aires, AR |210 |151 |39 % |
| \*Londres, Royaume-Uni |195 |44 |343 % |
| Shanghai, CN |242 |206 |17 % |
| \*Singapour |214 |74 |189 % |
| \*Tokyo, JP |163 |48 |204 % |
| Séoul, KR |190 |190 |0 % |

\* A un nœud de CDN Azure dans la même ville.  
\*\* A un nœud de CDN Azure dans une ville voisine.  

## <a name="challenges"></a>Défis
Il existe plusieurs problèmes à prendre en compte lorsque vous envisagez d’utiliser le CDN :  

* **Déploiement**. Vous devez décider l’origine à partir de laquelle le CDN va extraire le contenu, et si vous avez besoin de déployer le contenu dans plusieurs systèmes de stockage (par exemple, dans le CDN et dans un autre emplacement).
  
  Le mécanisme de déploiement de votre application doit prendre en compte le processus de déploiement de contenu et de ressources statiques, ainsi que de déploiement des fichiers d’application tels que des pages ASPX. Par exemple, il se peut que vous deviez implémenter une étape distincte pour charger du contenu dans le stockage d’objets blob Azure.
* **Contrôle de version et de cache**. Vous devez envisager la façon dont vous allez mettre à jour du contenu statique et déployer de nouvelles versions. Le contenu CDN peut être [vidé](cdn/cdn-purge-endpoint.md) à l'aide du portail Azure lorsque de nouvelles versions de vos ressources sont disponibles. Il s’agit d’un problème similaire à la gestion de la mise en cache côté client, comme celle qui se produit dans un navigateur web.
* **Test**. Il peut être difficile de réaliser des tests locaux de vos paramètres de CDN lors du développement et du test d’une application localement ou dans un environnement intermédiaire.
* **Optimisation du moteur de recherche (SEO)**. Du contenu tel que des images et des documents est servi à partir d’un autre domaine lorsque vous utilisez le CDN. Cela peut avoir un effet sur le SEO pour ce contenu.
* **Sécurité du contenu**. De nombreux services de CDN, tel le CDN Azure, n’offrent actuellement aucun type de contrôle d’accès pour le contenu.
* **Sécurité du client**. Des clients peuvent se connecter à partir d’un environnement qui n’autorise pas l’accès aux ressources sur le CDN. Cela peut être un environnement soumis à des contraintes de sécurité qui limite l’accès uniquement à un ensemble de sources connues ou qui empêche le chargement de ressources à partir de tout autre emplacement que la page d’origine. Une implémentation de secours est requise pour gérer ces situations.
* **Résilience**. Le CDN peut être le point unique de défaillance d’une application. Il dispose d’une disponibilité SLA inférieure au stockage d’objets blob (qui peut être utilisé pour distribuer directement du contenu). Par conséquent, vous devrez peut-être envisager d’implémenter un mécanisme de secours pour le contenu important.
  
  Vous pouvez surveiller la disponibilité du contenu, la bande passante, les données transférées, les accès, le taux d’accès au cache et les métriques du cache de votre CDN sur le portail Azure en [temps réel](cdn/cdn-real-time-stats.md) et dans les [rapports agrégés](cdn/cdn-analyze-usage-patterns.md).

Les scénarios où le CDN peut s’avérer moins utile sont :  

* Si le contenu a un faible taux d’accès, il risque de n’être accessible que quelques fois quand il est valide (ce que détermine son paramètre de durée de vie). La première fois qu’un élément est téléchargé, vous encourez deux frais de transaction (du point d’origine au CDN, puis du CDN au client).
* Si les données sont privées, comme pour les grandes entreprises ou les écosystèmes de chaîne d’approvisionnement.

## <a name="general-guidelines-and-good-practices"></a>Instructions générales et meilleures pratiques
L’utilisation du CDN est une bonne façon de réduire la charge sur votre application et d’optimiser la disponibilité et les performances. Vous devez envisager d’adopter cette stratégie pour l’ensemble du contenu et des ressources appropriés que votre application utilise. Lors de l’élaboration de votre stratégie d’utilisation du CDN, prenez en considération les points des sections suivantes :  

### <a name="origin"></a>Origine
Un déploiement de contenu via le CDN nécessite simplement que vous spécifiiez un point de terminaison HTTP et/ou HTTPS que le service CDN utilisera pour accéder au contenu et le mettre en cache.

Le point de terminaison peut spécifier un conteneur de stockage d’objets blob Azure comprenant le contenu statique que vous voulez distribuer via le CDN. Le conteneur doit être marqué comme étant public. Seuls les objets blob dans un conteneur public disposant d’un accès en lecture public seront disponibles sur le CDN.

Le point de terminaison peut spécifier un dossier nommé **cdn** dans la racine de l’une des couches de calcul de l’application (par exemple, un rôle web ou une machine virtuelle). Les résultats des demandes de ressources, y compris des ressources dynamiques telles que des pages ASPX, sont mis en cache sur le CDN. La période minimale de mise en cache est de 300 secondes. Une période plus courte empêche le déploiement du contenu sur le CDN (pour plus d’informations, consultez *Contrôle de cache* ci-dessous).

Si vous utilisez Azure Web Apps, le point de terminaison est défini dans le dossier racine du site en sélectionnant le site lors de la création de l’instance du CDN. Tout le contenu du site est accessible via le CDN.

Dans la plupart des cas, le pointage vers le point de terminaison CDN dans un dossier dans une des couches de calcul de votre application offre plus de flexibilité et de contrôle. Par exemple, il est plus facile de gérer les exigences de routage actuelles et futures et de générer de façon dynamique le contenu statique tel que des images miniatures.

Vous pouvez utiliser des [chaînes de requête](cdn/cdn-query-string.md) pour différencier les objets dans le cache lorsque le contenu est distribué à partir de sources dynamiques telles que les pages ASPX. Toutefois, ce comportement peut être désactivé par un paramètre dans le portail Azure lorsque vous spécifiez le point de terminaison CDN. Lors de la distribution du contenu depuis le stockage d’objets blob, les chaînes de requête sont traitées comme des littéraux de chaîne de manière à ce que deux éléments qui ont le même nom mais des chaînes de requête différentes soient stockés en tant qu’éléments distincts sur le CDN.

Vous pouvez utiliser la réécriture d’URL pour les ressources telles que les scripts et autres contenus afin d’éviter de déplacer vos fichiers dans le dossier d’origine du CDN.

Lorsque vous utilisez des objets blob de stockage Azure pour conserver le contenu du CDN, l’URL des ressources dans les objets blob est sensible à la casse pour le nom du conteneur et des objets blob.

Lorsque vous utilisez des origines personnalisées ou Azure Web Apps, vous spécifiez le chemin d’accès à l’instance CDN dans les liens vers les ressources. Par exemple, le code suivant spécifie un fichier image dans le dossier **Images** dossier du site qui sera distribué via le CDN :

```XML
<img src="http://[your-cdn-endpoint].azureedge.net/Images/image.jpg" />
```

### <a name="deployment"></a>Déploiement
Il se peut que vous deviez configurer et déployer le contenu statique indépendamment de l’application si vous ne l’incluez pas dans le package ou le processus de déploiement d’application. Pensez à la façon dont cela affectera l’approche du contrôle de version que vous utilisez pour gérer les composants d’application et le contenu de ressources statique.

Pensez à la façon dont le regroupement (le rassemblement de plusieurs fichiers dans un seul fichier) et la minimisation (la suppression de caractères inutiles tels que des espaces blancs, les caractères de nouvelle ligne, les commentaires et autres caractères) des scripts et des fichiers CSS peuvent être gérés. Ces techniques couramment utilisées peuvent réduire les temps de chargement pour les clients, et sont compatibles avec la distribution de contenu via le CDN. Pour plus d’informations, consultez [Regroupement et minimisation](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification).

Si vous devez déployer le contenu à un emplacement supplémentaire, cela constitue une étape supplémentaire dans le processus de déploiement. Si l’application met à jour le contenu pour le CDN, peut-être à intervalles réguliers ou en réponse à un événement, elle doit stocker le contenu mis à jour dans tous les emplacements supplémentaires ainsi qu’au niveau du point de terminaison du CDN.

Vous ne pouvez pas configurer un point de terminaison CDN pour une application de l’émulateur Azure local dans Visual Studio. Cette restriction affecte les tests d’unités, les tests fonctionnels et les tests avant le déploiement final. Vous devez autoriser cela en implémentant un autre mécanisme. Par exemple, vous pouvez prédéployer le contenu du CDN à l’aide d’une application ou d’un utilitaire personnalisés, et effectuer des tests pendant la période où il est mis en cache. Autrement, utilisez des directives de compilation ou des constantes globales pour contrôler l’emplacement à partir duquel l’application charge les ressources. Par exemple, lors de l’exécution en mode débogage, elle peut charger des ressources telles que les regroupements de script côté client et d’autres contenus à partir d’un dossier local et utiliser le CDN lors de l’exécution en mode version finale.

Envisagez l’approche de compression que votre CDN doit prendre en charge :

* Vous pouvez [activer la compression](cdn/cdn-improve-performance.md) sur votre serveur d’origine, auquel cas le CDN prend en charge la compression par défaut, et distribue le contenu compressé aux clients dans un format tel que zip ou gzip. Lorsque vous utilisez un dossier d’application comme point de terminaison du CDN, le serveur peut compresser du contenu automatiquement de la même façon que lors de sa distribution directement à un navigateur web ou à un autre type de client. Le format dépend la valeur de l’en-tête **Accept-Encoding** dans la demande envoyée par le client. Dans Azure, le mécanisme par défaut consiste à compresser automatiquement le contenu lorsque l’utilisation du processeur est inférieure à 50 %. Si vous utilisez un service cloud pour héberger l’application, la modification des paramètres peut nécessiter l’utilisation d’une tâche de démarrage pour activer la compression du résultat dynamique dans IIS. Pour plus d’informations, voir [Activation de la compression gzip avec le CDN Microsoft Azure via un rôle web](http://blogs.msdn.com/b/avkashchauhan/archive/2012/03/05/enableing-gzip-compression-with-windows-azure-cdn-through-web-role.aspx) .
* Vous pouvez activer la compression directement sur les serveurs Edge CDN, auquel cas le CDN compresse les fichiers et les envoie aux utilisateurs. Pour plus d’informations, voir [Compression du CDN Azure](cdn/cdn-improve-performance.md).

### <a name="routing-and-versioning"></a>Routage et contrôle de version
Il se peut que vous deviez utiliser différentes instances de CDN à différents moments. Par exemple, lorsque vous déployez une nouvelle version de l’application, vous pouvez utiliser un nouveau CDN et conserver l’ancien (comprenant du contenu de format plus ancien) pour des versions antérieures. Si vous utilisez le stockage d’objets blob Azure comme origine du contenu, vous pouvez simplement créer un compte de stockage ou un conteneur distinct et pointer le point de terminaison CDN vers celui-ci. Si vous utilisez le dossier racine *cdn* dans l’application en tant que point de terminaison CDN, vous pouvez utiliser des techniques de réécriture d’URL pour diriger les demandes vers un autre dossier.

N’utilisez pas la chaîne de requête pour indiquer d’autres versions de l’application dans des liens vers des ressources sur le CDN car, lors de la récupération de contenu à partir du stockage d’objets blob Azure, la chaîne de requête fait partie du nom de la ressource (nom d’objet blob). Cette approche peut également avoir une incidence sur la façon dont le client met en cache les ressources.

Le déploiement de nouvelles versions de contenu statique lorsque vous mettez à jour une application peut être problématique si les ressources précédentes sont mises en cache sur le CDN. Pour plus d’informations, voir *Contrôle de cache*.

Envisagez de limiter l’accès au contenu du CDN par pays. Le CDN Azure vous permet de filtrer les demandes en fonction du pays d’origine, et de restreindre le contenu distribué. Pour plus d’informations, voir [Restriction de l’accès à votre contenu par pays](cdn/cdn-restrict-access-by-country.md).

### <a name="cache-control"></a>contrôle de cache
Considérez comment gérer la mise en cache dans le système. Par exemple, lors de l’utilisation d’un dossier en tant qu’origine du CDN, vous pouvez spécifier la capacité de mise en cache de pages qui génèrent le contenu, et le délai d’expiration de contenu pour toutes les ressources d’un dossier spécifique. Vous pouvez également spécifier des propriétés de cache pour le CDN et pour le client à l’aide d’en-têtes HTTP standard. Même si vous gérez probablement déjà la mise en cache sur le serveur et client, l’utilisation du CDN vous permettra de mieux voir la façon dont votre contenu est mis en cache à quel emplacement.

Pour empêcher des objets d’être disponibles sur le CDN, vous pouvez les supprimer du point l’origine (conteneur d’objets blob ou dossier racine *cdn* de l’application), supprimer le point de terminaison du CDN ou, dans le cas du stockage d’objets blob, rendre le conteneur ou l’objet blob privés. Toutefois, des éléments sont supprimés du CDN uniquement lorsque leur durée de vie (TTL) expire. Si aucune période d’expiration du cache n’est spécifiée (par exemple, quand le contenu est chargé à partir d’un stockage d’objets blob), le contenu est mis en cache sur le CDN pendant jusqu’à 7 jours.  Vous pouvez également [vider un point de terminaison CDN](cdn/cdn-purge-endpoint.md)manuellement.

Dans une application web, vous pouvez définir la mise en cache et l’expiration pour tout le contenu à l’aide de l’élément *clientCache* dans la section *system.webServer/staticContent* du fichier web.config. N’oubliez pas que, lorsque vous placez un fichier web.config dans un dossier, il a une incidence sur les fichiers de ce dossier et tous ses sous-dossiers.

Si vous créez le contenu du CDN de façon dynamique (par exemple, dans le code de votre application), veillez à spécifier la propriété *Cache.SetExpires* sur chaque page. Le CDN ne met pas en cache le résultat des pages qui utilisent le paramètre *public*de capacité de mise en cache par défaut.  Définissez la période d’expiration du cache à une valeur appropriée pour vous assurer que le contenu n’est pas ignoré et rechargé à partir de l’application à des intervalles très courts.  

### <a name="security"></a>Sécurité
Le CDN peut distribuer du contenu via HTTPS (SSL) à l’aide du certificat fourni par le CDN, mais il est également disponible sur HTTP. Vous ne pouvez pas bloquer l’accès HTTP aux éléments dans le CDN. Vous devrez peut-être utiliser HTTPS pour demander du contenu statique qui est affiché dans les pages chargées via HTTPS (par exemple, un Panier) afin d’éviter les avertissements de navigateur relatifs au contenu mixte.

Un CDN Azure ne fournit pas d’équipements de contrôle d’accès pour sécuriser l’accès au contenu. Vous ne pouvez pas utiliser des signatures d’accès partagé (SAS) avec le CDN.

Si vous distribuez des scripts côté client à l’aide du CDN, vous pouvez rencontrer des problèmes si ces scripts utilisent un appel *XMLHttpRequest* pour effectuer des demandes HTTP pour d’autres ressources telles que des données, des images ou des polices dans un autre domaine. De nombreux navigateurs web empêchent le partage des ressources provenant de différentes origines (CORS) sauf si le serveur web est configuré pour définir les en-têtes de réponse appropriés. Vous pouvez configurer le CDN pour prendre en charge le CORS :

* si le stockage d’objets blob Azure est l’emplacement d’origine à partir duquel vous distribuez du contenu, vous pouvez ajouter une *CorsRule* aux propriétés de service. La règle peut spécifier les origines autorisées pour les demandes CORS, les méthodes autorisées telles que GET et la durée maximale en secondes de la règle (la période pendant laquelle le client doit demander les ressources liées après le chargement du contenu d’origine). Pour plus d’informations, consultez [Support du partage des ressources provenant de différentes origines (CORS) pour les services de stockage d’Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).
* Si l’emplacement d’origine à partir duquel vous distribuez du contenu est un dossier dans l’application, tel que le dossier racine *cdn*, vous pouvez configurer des règles de trafic sortant dans le fichier de configuration de l’application pour définir un en-tête *Access-Control-Allow-Origin* sur toutes les réponses. Pour plus d’informations sur l’utilisation des règles de réécriture, consultez [Module de réécriture d’URL](http://www.iis.net/learn/extensions/url-rewrite-module).

### <a name="custom-domains"></a>Domaines personnalisés
Le CDN Azure vous permet de spécifier un [nom de domaine personnalisé](cdn/cdn-map-content-to-custom-domain.md) et de l’utiliser pour accéder à des ressources via le CDN. Vous pouvez également définir un nom de sous-domaine personnalisé à l'aide d’un enregistrement *CNAME* dans votre DNS. Cette approche peut fournir une couche supplémentaire d’abstraction et de contrôle.

Si vous utilisez un enregistrement *CNAME*, vous ne pouvez pas utiliser SSL, car le CDN utilise son propre certificat SSL unique qui ne correspond pas à vos noms de domaine/sous-domaine personnalisés.

### <a name="cdn-fallback"></a>Secours CDN
Vous devez envisager la façon dont votre application peut faire face à une défaillance ou à une indisponibilité temporaire du CDN. Les applications clientes peuvent être en mesure d’utiliser des copies des ressources qui ont été mises en cache localement (sur le client) lors de requêtes précédentes. Vous pouvez également inclure du code qui détecte des défaillances, et demande à la place des ressources à partir de l’emplacement d’origine (dossier de l’application ou conteneur d’objets blob Azure contenant les ressources) si le CDN n’est pas disponible.

### <a name="search-engine-optimisation"></a>Optimisation de moteur de recherche (SEO)
Si la SEO constitue un aspect important de votre application, effectuez les tâches suivantes :

* Incluez un en-tête canonique *Rel* dans chaque page ou ressource.
* Utilisez un enregistrement de sous-domaine *CNAME* et accédez aux ressources à l’aide de ce nom.
* Prenez en compte l’impact relatif au fait que l’adresse IP du CDN peut être un autre pays ou une autre région que celui/celle de l’application elle-même.
* Lorsque vous utilisez le stockage d’objets blob Azure comme origine, conservez la même structure du fichier pour les ressources sur le CDN que celle des dossiers de l’application.

### <a name="monitoring-and-logging"></a>Surveillance et journalisation
Incluez le CDN dans le cadre de la stratégie de surveillance de votre application pour détecter et mesurer des défaillances ou des occurrences de latence prolongée.  La surveillance est disponible à partir du gestionnaire de profil de CDN situé sur le site du portail Azure

Activez la journalisation pour le CDN et incluez-la dans vos opérations quotidiennes.

Envisagez d’analyser le trafic du CDN pour les modèles d’utilisation. Le portail Azure fournit des outils qui vous permettent de surveiller :

* la bande passante,
* les données transférées,
* les correspondances (codes d'état),
* l’état du cache,
* le taux d'accès au cache
* et le taux de requêtes IPV4/IPV6.

Pour plus d’informations, voir [Analyse des modèles d’utilisation CDN](cdn/cdn-analyze-usage-patterns.md).

### <a name="cost-implications"></a>Coûts résultants
Vous êtes facturé pour les transferts de données sortantes depuis le CDN.  En outre, si vous utilisez le stockage d'objets blob pour héberger vos ressources, vous êtes facturé pour les transactions de stockage lorsque le CDN charge des données à partir de votre application. Vous devez définir des délais d’expiration réalistes de cache du contenu pour garantir l’actualisation, sans que ces délais soient trop courts, ce qui entraînerait le rechargement répété du contenu à partir de l’application ou du stockage d’objets blob sur le CDN.

Les éléments rarement téléchargés impliquent les deux frais de transaction sans fournir une réduction notable de la charge du serveur.

### <a name="bundling-and-minification"></a>Regroupement et minimisation
Utilisez un regroupement et un minimisation pour réduire la taille de ressources telles que le code JavaScript et les pages HTML stockés dans le CDN. Cette stratégie peut vous aider à réduire le temps nécessaire pour télécharger ces éléments sur le client.

Le regroupement et la minimisation peuvent être gérés par ASP.NET. Dans un projet MVC, vous définissez vos regroupements dans *BundleConfig.cs*. Une référence au regroupement de script minimisé est créée en appelant la méthode *Script.Render* , généralement dans le code dans la classe d’affichage. Cette référence contient une chaîne de requête qui inclut un hachage basé sur le contenu du regroupement. Si le contenu du regroupement est modifié, le hachage généré est également modifié.  

Par défaut, les instances Azure CDN ont le paramètre *État de la chaîne de requête* désactivé. Afin que les regroupements de script mis à jour soient correctement gérés par le CDN, vous devez activer le paramètre *État de la chaîne de requête* pour l’instance CDN. Notez qu’une heure ou plus peut s’écouler avant que le paramètre prenne effet.

## <a name="example-code"></a>Exemple de code
Cette section contient des exemples de code et des techniques permettant de travailler avec le CDN.  

### <a name="url-rewriting"></a>Réécriture d’URL
L’extrait suivant d’un fichier Web.config à la racine d’une application hébergée sur Cloud Services montre comment effectuer une [réécriture d’URL](https://technet.microsoft.com/library/ee215194.aspx) lors de l’utilisation du CDN. Les demandes du CDN pour le contenu en cache sont redirigées vers des dossiers spécifiques dans la racine de l’application, en fonction du type de la ressource (par exemple, des scripts et des images).  

```XML
<system.webServer>
  ...
  <rewrite>
    <rules>
      <rule name="VersionedResource" stopProcessing="false">
        <match url="(.*)_v(.*)\.(.*)" ignoreCase="true" />
        <action type="Rewrite" url="{R:1}.{R:3}" appendQueryString="true" />
      </rule>
      <rule name="CdnImages" stopProcessing="true">
        <match url="cdn/Images/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Images/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnContent" stopProcessing="true">
        <match url="cdn/Content/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Content/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScript" stopProcessing="true">
        <match url="cdn/Scripts/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Scripts/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScriptBundles" stopProcessing="true">
        <match url="cdn/bundles/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/bundles/{R:1}" appendQueryString="true" />
      </rule>
    </rules>
  </rewrite>
  ...
</system.webServer>
```

Ces règles de réécriture effectuent les redirections suivantes :

* La première règle vous permet d’incorporer une version dans le nom de fichier d’une ressource, qui est alors ignorée. Par exemple, *Filename_v123.jpg *est réécrit sous la forme *Filename.jpg*.
* Les quatre règles suivantes indiquent comment rediriger les demandes si vous ne souhaitez pas stocker les ressources dans un dossier nommé *cdn** à la racine du rôle web. Les règles mappent les URL *cdn/Images*, *cdn/Content*, *cdn/Scripts* et *cdn/bundles* vers leurs dossiers racines respectifs dans le rôle web.

Notez que l’utilisation de la réécriture d’URL requiert que vous apportiez quelques modifications au regroupement des ressources.   

## <a name="more-information"></a>Plus d’informations
* [Azure CDN](https://azure.microsoft.com/services/cdn/)
* [Documentation sur le réseau de distribution de contenu (CDN) Azure](https://azure.microsoft.com/documentation/services/cdn/)
* [Utilisation d’Azure CDN](cdn/cdn-create-new-endpoint.md)
* [Intégration d’un service cloud à Azure CDN](cdn/cdn-cloud-service-with-cdn.md)
* [Meilleures pratiques pour le réseau de distribution de contenu Microsoft Azure](https://azure.microsoft.com/blog/2011/03/18/best-practices-for-the-windows-azure-content-delivery-network/)




<!--HONumber=Nov16_HO3-->


