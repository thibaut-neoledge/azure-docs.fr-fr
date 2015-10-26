<properties 
   pageTitle="Présentation de Traffic Manager | Microsoft Azure"
   description="Cet article vous aidera à comprendre le principe et le fonctionnement de Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="joaoma" />

# Qu’est-ce que Traffic Manager ?

Microsoft Azure Traffic Manager vous permet de contrôler la distribution du trafic utilisateur vers les points de terminaison que vous spécifiez, par exemple des sites web ou des services cloud Azure. Traffic Manager utilise un moteur de stratégie intelligent pour les requêtes DNS des noms de domaine de vos ressources Internet. Vos sites web ou services cloud Azure peuvent être exécutés dans différents centres de données à travers le monde.

Traffic Manager peut vous aider à atteindre les objectifs suivants :

- **Améliorer la disponibilité des applications critiques** : Traffic Manager vous permet d’améliorer la disponibilité de vos applications critiques, en surveillant vos points de terminaison dans Microsoft Azure et en apportant des fonctionnalités de basculement automatique lorsqu’un service cloud ou un site web Azure, ou tout autre emplacement, n’est plus disponible.
- **Améliorer la réactivité des applications hautes performances** : Microsoft Azure vous permet d’exécuter des services cloud ou des sites web dans différents centres de données à travers le monde. Traffic Manager peut améliorer la réactivité de vos applications et vos délais de diffusion de contenu en dirigeant les utilisateurs finaux vers le point de terminaison affichant la latence réseau la plus faible par rapport au client.
- **Mettre à niveau vos services et en assurer la maintenance sans temps d’arrêt** : Traffic Manager prend en charge une grande diversité de scénarios de déploiement de cloud hybride et sur site, notamment le « burst-to-cloud », la migration vers le cloud et le basculement vers le cloud. Pour une maintenance planifiée, vous devez désactiver le point de terminaison dans Traffic Manager et attendre que celui-ci ait effectué la maintenance des connexions existantes. Lorsque plus aucun trafic n’aboutit au point de terminaison, vous devez mettre à jour le service sur ce point de terminaison et le tester, puis le réactiver dans Traffic Manager. Cela vous aide à mettre à niveau vos services et à en assurer la maintenance sans temps d’arrêt pour les clients.
- **Assurer la distribution du trafic pour les déploiements vastes et complexes** : grâce aux profils Traffic Manager imbriqués, dans lesquels un profil Traffic Manager peut en contenir un autre faisant office de point de terminaison, vous pouvez créer des configurations pour optimiser les performances et la distribution dans le cadre de déploiements plus vastes et plus complexes. Pour en savoir plus, voir [Profils imbriqués](#nested-profiles).

## Fonctionnement de Traffic Manager

Lorsque vous configurez un profil Traffic Manager, les paramètres que vous spécifiez fournissent à Traffic Manager les informations nécessaires pour déterminer le point de terminaison qui doit traiter la demande selon une requête DNS. Aucun trafic de point de terminaison ne transite réellement via Traffic Manager.

La *figure 1* indique comment Traffic Manager dirige les utilisateurs vers l’un des ensembles de points de terminaison. Les chiffres de la figure 1 correspondent aux descriptions ci-après :

![Fonctionnement de Traffic Manager](./media/traffic-manager-overview/IC740854.jpg)

**Figure 1**

1. **Trafic des données utilisateur vers le nom de domaine d’entreprise** : le client demande des informations à l’aide du nom de domaine d’entreprise. L’objectif est de résoudre un nom DNS en adresse IP. Les domaines d’entreprise doivent être réservés via des inscriptions de nom de domaine Internet standard qui sont gérées de façon externe à Traffic Manager. Dans la figure 1, le domaine d’entreprise indiqué en exemple est *www.contoso.com*.
2. **Depuis le nom de domaine d’entreprise vers le nom de domaine Traffic Manager** : l’enregistrement de ressource DNS associé au domaine d’entreprise pointe vers un nom de domaine Traffic Manager géré dans Azure Traffic Manager. Pour cela, un enregistrement de ressource CNAME mappe le nom de domaine d’entreprise au nom de domaine Traffic Manager. Dans cet exemple, le nom de domaine Traffic Manager est *contoso.trafficmanager.net*.
3. **Nom de domaine et profil Traffic Manager** : le nom de domaine Traffic Manager fait partie du profil Traffic Manager. Le serveur DNS de l’utilisateur envoie une nouvelle requête DNS pour le nom de domaine Traffic Manager (dans notre exemple, *contoso.trafficmanager.net*), laquelle est reçue par les serveurs de noms DNS de Traffic Manager.
4. **Traitement des règles de profil Traffic Manager** : Traffic Manager utilise la méthode de routage du trafic et l’état de la surveillance spécifiés pour déterminer quel point de terminaison (Azure ou autre) doit traiter la demande.
5. **Nom de domaine du point de terminaison envoyé à l’utilisateur** : Traffic Manager renvoie un enregistrement CNAME qui mappe le nom de domaine Traffic Manager au nom de domaine du point de terminaison. Le serveur DNS de l’utilisateur résout le nom de domaine du point de terminaison selon son adresse IP et l’envoie à l’utilisateur.
6. **Appel du point de terminaison par l’utilisateur** : l’utilisateur appelle directement le point de terminaison renvoyé à l’aide de son adresse IP.

Dans la mesure où le domaine d’entreprise et l’adresse IP résolue sont mis en cache sur l’ordinateur client, l’utilisateur continue à interagir avec le point de terminaison choisi jusqu’à ce que son entrée de cache DNS locale expire. Il est important de noter que le client DNS met en cache les entrées d’hôte DNS pendant leur durée de vie (TTL, Time-to-Live). Le profil Traffic Manager n’est pas pris en compte lors de la récupération des entrées d’hôte à partir du cache du client DNS. Aussi, des retards de connexion peuvent se produire si le point de terminaison devient indisponible avant l’expiration de la TTL. Si la TTL d’une entrée d’hôte DNS dans le cache expire et que l’ordinateur client doit résoudre une nouvelle fois le nom de domaine d’entreprise, il envoie une nouvelle requête DNS. L’ordinateur client peut recevoir l’adresse IP d’un autre point de terminaison selon la méthode de routage du trafic appliquée et l’intégrité des points de terminaison au moment de la requête.

## Mise en œuvre de Traffic Manager

La *figure 2* illustre les étapes nécessaires à l’implémentation de Traffic Manager, dans leur ordre logique. Dès lors que vous maîtrisez la configuration de Traffic Manager et les meilleures pratiques, vous pouvez modifier légèrement l’ordre de ces étapes. Les chiffres de la figure 2 correspondent aux descriptions ci-après :

![Configuration de Traffic Manager](./media/traffic-manager-overview/IC740855.jpg)

**Figure 2**

1. **Déployez vos services cloud et sites web Microsoft Azure ou d’autres points de terminaison dans votre environnement de production**. Lorsque vous créez un profil Traffic Manager, il doit être associé à un abonnement. Vous ajoutez ensuite les points de terminaison pour les services cloud et sites web de niveau Standard en production, qui font partie du même abonnement. Si un point de terminaison est dans un état intermédiaire et n’est pas dans un environnement de production Azure ou ne fait pas partie du même abonnement, il peut être ajouté comme point de terminaison externe. Pour en savoir plus sur les services cloud, voir [Services cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074). Pour en savoir plus sur les services cloud, voir [Applications web](http://go.microsoft.com/fwlink/p/?LinkId=393327).
2. **Choisissez un nom pour votre domaine Traffic Manager**. Utilisez de préférence un préfixe unique pour le nom de votre domaine. La dernière partie du domaine, trafficmanager.net, est fixe. Pour en savoir plus, voir [Vue d’ensemble de Traffic Manager](#best-practices).
3. **Choisissez la configuration de surveillance que vous souhaitez utiliser**. Traffic Manager surveille les points de terminaison pour vérifier qu’ils sont en ligne, quelle que soit la méthode de routage du trafic. Une fois que vous avez configuré les paramètres de surveillance, Traffic Manager ne dirige plus le trafic vers les points de terminaison considérés comme hors connexion par le système de surveillance, sauf s’il détecte que tous les points de terminaison sont hors connexion ou s’il ne peut pas détecter l’état des points de terminaison contenus dans le profil. Pour en savoir plus sur la surveillance, voir [Surveillance avec Traffic Manager](traffic-manager-monitoring.md).
4. **Choisissez la méthode de routage du trafic à utiliser**. Trois méthodes de routage de trafic sont disponibles. Prenez le temps de déterminer celle qui conviendra le mieux à vos besoins. Par la suite, vous pourrez changer de méthode à tout moment. Notez également que chaque méthode implique des étapes de configuration légèrement différentes. Pour plus d’informations sur les différentes méthodes de routage du trafic, consultez [À propos des méthodes de routage du trafic de Traffic Manager](traffic-manager-load-balancing-methods.md).
5. **Créez votre profil et configurez les paramètres**. Vous pouvez utiliser les API REST, Windows PowerShell ou le Portail de gestion pour créer votre profil Traffic Manager et configurer les paramètres. Pour en savoir plus, voir [Configuration des paramètres Traffic Manager](#how-to-configure-traffic-manager-settings). Les étapes suivantes supposent que vous utiliserez l’option **Création rapide** du Portail de gestion. 
   - **Créer votre profil Traffic Manager** : pour créer un profil à l’aide de l’option Création rapide du Portail de gestion, voir [Gérer des profils Traffic Manager](traffic-manager-manage-profiles.md).
   - **Configurer les paramètres de la méthode de routage du trafic** : avec l’option Création rapide, vous devez sélectionner la méthode de routage du trafic pour votre profil. Ce paramètre peut être modifié à tout moment, une fois réalisées les étapes de l’option Création rapide. Pour les étapes de configuration, consultez la rubrique qui correspond à votre méthode de routage du trafic : [Configuration des performances de la méthode de routage du trafic](traffic-manager-configure-performance-load-balancing.md), [Configuration du basculement de la méthode de routage du trafic](traffic-manager-configure-failover-load-balancing.md), [Configuration du tourniquet (round robin) de la méthode de routage du trafic](traffic-manager-configure-round-robin-load-balancing.md).
   
   >[AZURE.NOTE]La méthode de routage du trafic tourniquet (round robin) prend désormais en charge la distribution pondérée du trafic réseau. Toutefois, pour le moment, vous devez utiliser les API REST ou Windows PowerShell pour configurer la pondération. Pour en savoir plus et obtenir un exemple de configuration, consultez la page [Azure Traffic Manager External Endpoints and Weighted Round Robin via PowerShell](http://azure.microsoft.com/blog/2014/06/26/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/) sur le blog Azure.

   - **Configurer les points de terminaison** : les points de terminaison ne sont pas configurés à l’aide de l’option Création rapide. Après avoir créé votre profil et spécifié votre méthode de routage du trafic, vous devez indiquer à Traffic Manager les points de terminaison. Pour connaître la procédure de configuration des points de terminaison, voir [Gérer des points de terminaison dans Traffic Manager](traffic-manager-endpoints.md).

   - **Configurer les paramètres de surveillance** : les paramètres de surveillance ne sont pas configurés à l’aide de l’option Création rapide. Après avoir créé votre profil et spécifié votre méthode de routage du trafic, vous devez indiquer à Traffic Manager les éléments à surveiller. Pour connaître la procédure de configuration de la surveillance, voir [Surveillance avec Traffic Manager](traffic-manager-monitoring.md).
6. **Testez votre profil Traffic Manager**. Vérifiez que votre profil et votre domaine fonctionnent comme prévu. Pour savoir comment procéder, voir [Test des paramètres de Traffic Manager](traffic-manager-testing-settings.md).
7. **Redirigez l’enregistrement de ressource DNS du nom de domaine d’entreprise vers le profil pour le rendre actif**. Pour plus d’informations, consultez la page [Redirection d’un domaine Internet d’entreprise vers un domaine Traffic Manager](traffic-manager-point-internet-domain.md).

À l’aide de l’exemple de la figure 1, vous devez remplacer l’enregistrement de ressource DNS sur vos serveurs par la ligne suivante, afin de rediriger le nom de domaine d’entreprise vers le nom de domaine Traffic Manager : www.contoso.com IN CNAME contoso.trafficmanager.net.

## Configuration des paramètres de Traffic Manager

Vous pouvez configurer les paramètres de Traffic Manager à l’aide du Portail de gestion, avec les API REST et avec les applets de commande Windows PowerShell.

Même si tous les éléments d’API REST ne sont pas visibles dans le Portail de gestion, de nombreux paramètres sont disponibles avec chacune des méthodes. Pour en savoir plus sur l’utilisation des API REST, consultez la page [Opérations pour Traffic Manager (informations de référence sur l’API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584).

Pour en savoir plus sur les applets de commande Windows PowerShell pour Traffic Manager, consultez la page [Applets de commande Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769).

>[AZURE.NOTE]Il n’existe actuellement aucune prise en charge pour la configuration des points de terminaison externes (type = « Any »), des pondérations pour la méthode de routage du trafic tourniquet (round robin) et des profils imbriqués avec le Portail de gestion. Vous devez utiliser REST (consultez la page [Créer une définition](http://go.microsoft.com/fwlink/p/?LinkId=400772)) ou Windows PowerShell (consultez la page [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)).

### Configuration des paramètres dans le Portail de gestion

Dans le Portail de gestion, vous pouvez créer votre profil Traffic Manager à l’aide de l’option Création rapide. Cette option vous permet de créer un profil de base. Après avoir créé votre profil, vous pouvez configurer des paramètres supplémentaires ou modifier les paramètres que vous avez précédemment configurés. Pour en savoir plus sur la création de votre profil Traffic Manager à l’aide de l’option Création rapide, voir [Gérer des profils Traffic Manager](traffic-manager-manage-profiles.md).

Vous pouvez configurer les paramètres suivants dans le Portail de gestion :

- **Préfixe DNS** : un préfixe unique que vous créez. Les profils sont affichés par préfixe dans le Portail de gestion.
- **TTL du DNS** : la durée de vie (TTL, Time-to-Live) du DNS contrôle la fréquence à laquelle le serveur de noms cache local du client interroge le système DNS Azure Traffic Manager pour obtenir les entrées DNS mises à jour.
- **Abonnement** : sélectionnez l’abonnement auquel correspond votre profil. Notez que cette option apparaît uniquement si vous avez plusieurs abonnements.
- **Méthode de routage du trafic** : méthode que Traffic Manager doit utiliser pour gérer le routage du trafic.
- **Ordre de basculement** : ordre des points de terminaison appliqué lorsque vous utilisez la méthode de basculement du routage du trafic.
- **Surveillance** : les paramètres de surveillance incluent le protocole (HTTP ou HTTPS), le port, le chemin d’accès relatif et le nom de fichier.

### Configuration des paramètres à l’aide des API REST

Vous pouvez créer et configurer votre profil Traffic Manager à l’aide des API REST. Pour en savoir plus, voir [Opérations pour Traffic Manager (informations de référence sur l’API REST)](http://go.microsoft.com/fwlink/?LinkId=313584).

- **Profil** : un profil contient un préfixe de nom de domaine que vous créez. Chaque profil correspond à votre abonnement. Vous pouvez créer plusieurs profils par abonnement. Le nom du profil est visible dans le Portail de gestion. Le nom que vous créez, contenu dans le profil, est considéré comme votre domaine Traffic Manager.
- **Définition** : une définition inclut des paramètres de stratégie et d’analyse. Une définition correspond à un profil. Vous ne pouvez avoir qu’une définition par profil. La définition proprement dite n’est pas visible dans le Portail de gestion, même si de nombreux paramètres contenus dans la définition sont visibles et peuvent être configurés dans le Portail de gestion.
- **Options DNS** : chaque définition inclut des options DNS. C’est à ce niveau que la TTL du DNS est configurée.
- **Analyseur** : chaque définition inclut des paramètres d’analyse. C’est à ce niveau que le protocole et le port, ainsi que le chemin d’accès relatif et le nom du fichier sont configurés. Les paramètres d’analyse sont visibles et peuvent être configurés dans le Portail de gestion. Pour plus d’informations, consultez [Surveillance avec Traffic Manager](traffic-manager-monitoring.md).
- **Stratégie** : chaque définition inclut des paramètres de stratégie. C’est dans le cadre de la stratégie que sont spécifiés les méthodes de routage du trafic et les points de terminaison. La stratégie proprement dite n’est pas visible dans le Portail de gestion, même si certains paramètres connexes sont visibles et peuvent être configurés dans le Portail de gestion. Pour plus d'informations, consultez la page [À propos des méthodes de routage du trafic dans Traffic Manager](traffic-manager-load-balancing-methods.md).

## Configuration des paramètres à l’aide de Windows PowerShell

Vous pouvez créer et configurer votre profil Traffic Manager à l’aide de Windows PowerShell. Pour en savoir plus, voir [Applets de commande Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769).

## Meilleures pratiques

- **Assurez-vous que vos préfixes sont uniques et faciles à comprendre** : le nom DNS de votre profil Traffic Manager doit être unique. Vous pouvez contrôler la première partie du nom DNS uniquement. Le nom de domaine Traffic Manager est utilisé uniquement pour l’identification et l’acheminement des requêtes de client. Les ordinateurs client n’affichent jamais ces noms à l’attention de l’utilisateur final. Toutefois, les profils sont identifiés par ce nom de domaine. Il est donc important que vous puissiez l’identifier rapidement parmi les noms de domaine répertoriés dans le Portail de gestion.
- **Utilisez des points pour renforcer l’unicité ou la lisibilité des noms de domaine** : vous pouvez également utiliser des points pour séparer les parties du préfixe de votre nom de domaine. Si vous envisagez de créer plusieurs stratégies dans Traffic Manager, utilisez une hiérarchie cohérente pour différencier les services. Par exemple, Contoso dispose de services globaux pour le Web, la facturation et la gestion de l’utilitaire. Les trois stratégies seraient les suivantes : *web.contoso.trafficmanager.net*, *bill.contoso.trafficmanager.net* et *util.contoso.trafficmanager.net*. Lorsque vous configurez des services cloud ou des sites web, utilisez des noms qui incluent l’emplacement. Par exemple, *web-us-contoso.cloudapp.net* et *web-asia-contoso.cloudapp.net* Les limites sont celles imposées par le DNS. Un nom de domaine est une suite d’étiquettes séparées par des points(étiquette.étiquette.étiquette.étiquette.etc.). Au moment de la rédaction de cette documentation, les limites pour les noms de domaine dans Traffic Manager sont les suivantes :
   - Chaque étiquette peut comporter un maximum de 63 caractères.
   - Vous ne pouvez avoir plus de 40 étiquettes au total. Étant donné que deux étiquettes sont réservées par trafficmanager.net, il reste 38 étiquettes pour votre préfixe.
   - Le nom de domaine complet peut comporter un maximum de 253 caractères. Gardez à l’esprit que 19 de ces caractères sont réservés par trafficmanager.net.
- **TTL du DNS** : la durée de vie (TTL) du DNS contrôle la fréquence à laquelle le serveur de noms cache local du client interroge le système DNS Azure Traffic Manager pour obtenir les entrées DNS mises à jour. Toute modification dans Traffic Manager, notamment à l’égard du profil ou de la disponibilité du point de terminaison, utilisera cette période pour son actualisation à l’échelle du système global de serveurs DNS. Nous vous recommandons de conserver la valeur par défaut de ce paramètre, soit 300 secondes (5 minutes). Une valeur supérieure augmente la durée pendant laquelle les réponses DNS de Traffic Manager sont mises en cache par les clients et les résolutions DNS, ce qui réduit la latence globale des requêtes DNS. Toutefois, une valeur inférieure est préférable si un basculement très rapide est requis.
- **Les points de terminaison doivent correspondre à un même abonnement** : tous les points de terminaison doivent correspondre à l’abonnement dans lequel vous créez le profil. Vous pouvez ajouter des points de terminaison de différents abonnements à un profil en tant que points de terminaison externes, mais Azure ne les supprimera pas automatiquement si vous désactivez ou supprimez le service associé. Ainsi, les points de terminaison externes restent dans le profil Traffic Manager et vous continuerez à être facturé en conséquence, sauf si vous les supprimez manuellement.
- **Services de production uniquement** : seuls les points de terminaison d’un environnement de production sont disponibles. Vous ne pouvez pas diriger de trafic vers les points de terminaison exécutés dans un environnement intermédiaire. Notez que si vous effectuez un échange d’adresses IP virtuelles alors qu’un profil dirige le trafic, le trafic utilisera le point de terminaison qui vient d’être échangé dans l’environnement de production.
- **Nommez vos points de terminaison afin qu’ils soient facilement identifiables** : tenez compte du préfixe DNS que vous souhaitez utiliser. Le nom DNS est utilisé car il est forcément unique dans un abonnement, tandis que le nom du service cloud ou du site web ne l’est peut-être pas. Pour éviter les confusions, affectez à un service cloud ou un site web un nom et un préfixe DNS identiques ou similaires. Si vous avez plus de 20 services cloud et sites web, une affectation de noms incohérente peut compliquer la recherche de point de terminaison. En outre, les points de terminaison qui ne sont pas nommés correctement compliqueront la maintenance des profils.
- **Tous les points de terminaison dans un profil doivent traiter les mêmes opérations et ports** : si vous combinez vos points de terminaison, le risque qu’un client appelle un point de terminaison qui ne peut pas traiter sa demande sera accru.
- **Tous les services cloud d’un profil doivent utiliser les mêmes paramètres de surveillance** : vous pouvez choisir un seul chemin d’accès et un seul fichier pour surveiller tous les points de terminaison d’une définition spécifique. Vous pouvez entrer le caractère « / » dans la zone de texte **Chemin d’accès relatif et nom de fichier**, de sorte que le processus de surveillance tente d’accéder au chemin d’accès et au nom de fichier par défaut.
- **Désactivez les points de terminaison pour apporter des modifications temporaires au lieu de modifier votre configuration** : dans de nombreux cas, vous pouvez être amené à mettre un point de terminaison hors connexion. Plutôt que de supprimer le point de terminaison de votre profil, désactivez simplement le point de terminaison concerné dans votre profil. Ainsi, le point de terminaison reste dans le profil, mais le profil agit comme si le point de terminaison n’y était pas inclus. Cela s’avère particulièrement utile pour supprimer temporairement un point de terminaison en mode de maintenance ou redéployé. Une fois le point de terminaison à nouveau opérationnel, vous pouvez l’activer. Pour plus d’informations, consultez [Gérer des points de terminaison dans Traffic Manager](traffic-manager-endpoints.md).
- **En cas de modifications temporaires, désactivez un profil au lieu de le supprimer** : vous pouvez être amené à mettre hors connexion un profil complet, et non seulement des points de terminaison spécifiés dans ce profil. Pour ce faire, désactivez le profil. Lorsque vous désactivez un profil, tous les paramètres restent disponibles pour modification dans le Portail de gestion. Ainsi, vous pouvez remettre le profil en ligne rapidement et facilement lorsque vous souhaitez l’utiliser à nouveau. Pour plus d’informations, consultez [Gérer des points de terminaison dans Traffic Manager](traffic-manager-endpoints.md).
- **Stockage** : la conception de l’emplacement et de la distribution de votre stockage est un aspect majeur à prendre en compte lors de l’utilisation de Traffic Manager. Considérez la transaction de bout en bout et le flux de vos données lorsque vous concevez et déployez vos applications pour Traffic Manager.
- **SQL Azure** : tout comme pour la conception du stockage, analysez l’état de votre application et vos besoins en termes de données lorsque vous étendez vos points de terminaison sur plusieurs zones géographiques.

## Profils imbriqués

Vous pouvez spécifier le nom d’un autre profil Traffic Manager en tant que point de terminaison. Vous créez alors des profils imbriqués. Le nom du profil Traffic Manager est son nom DNS, par exemple contoso-europe.trafficmanager.net.

Cela vous permet de configurer Traffic Manager de manière à ce que les requêtes de nom DNS entrantes soient analysées sur plusieurs niveaux pour que le client à l’origine de la demande soit dirigé vers l’ensemble de points de terminaison approprié. La *figure 3* propose un exemple.

![Exemples de profils Traffic Manager imbriqués](./media/traffic-manager-overview/IC751072.png)

**Figure 3**

Vous pouvez imbriquer jusqu’à 10 niveaux, et chaque profil peut être configuré avec une méthode de routage du trafic différente.

Par exemple, vous pouvez créer une configuration selon les niveaux suivants :

- Au niveau supérieur (le profil Traffic Manager mappé à votre nom DNS externe), vous pouvez configurer le profil avec la méthode de routage du trafic des performances.
- Au niveau intermédiaire, un ensemble de profils Traffic Manager représente différents centres de données et utilise la méthode de routage du trafic tourniquet (round robin).
- Au niveau inférieur, un ensemble de points de terminaison de service cloud dans chaque service de centre de données demandé par le trafic de l’utilisateur.

En conséquence, les utilisateurs sont dirigés vers un centre de données approprié sur le plan régional selon la performance et vers un service cloud de ce centre de données selon une distribution de charge identique ou pondérée. Par exemple, la pondération peut être utilisée pour distribuer un faible pourcentage du trafic vers un déploiement nouveau ou d’évaluation à des fins de test ou pour les commentaires client.

La *figure 4* affiche la configuration.

![Exemples des profils Traffic Manager à plusieurs niveaux](./media/traffic-manager-overview/IC751073.png)

**Figure 4**

Dans la *figure 4*, le profil Traffic Manager situé au niveau supérieur est un profil parent, tandis que les profils Traffic Manager se trouvant au niveau intermédiaire sont des profils enfants.

Si Traffic Manager dirige les utilisateurs vers un profil enfant comprenant un petit nombre de points de terminaison intègres, ceux-ci peuvent être surchargés, ce qui entraîne des problèmes de performance. Pour éviter cette situation, vous pouvez configurer le profil Traffic Manager parent avec un seuil de points de terminaison intègres qui détermine si des points de terminaison inclus dans les profils enfants de ce parent peuvent recevoir du trafic. Par exemple, si vous souhaitez vous assurer que les profils enfants incluent au moins trois points de terminaison intègres, vous devez définir cette valeur de seuil à 3. Dans l’exemple de la figure 4, vous devez configurer le profil Traffic Manager de niveau supérieur pour ce seuil.

Pour ajouter un profil Traffic Manager en tant que point de terminaison et configurer le nombre minimal de points de terminaison intègres, vous devez utiliser REST (consultez la page [Créer une définition](http://go.microsoft.com/fwlink/p/?LinkId=400772)) ou Windows PowerShell (consultez la page [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)). Vous ne pouvez pas utiliser le Portail de gestion.

## Figures Traffic Manager

Si vous souhaitez récupérer les figures de cette rubrique sous forme de diapositives PowerPoint pour votre propre présentation de Traffic Manager ou pour les modifier, consultez la page [Traffic Manager figures in MSDN documentation](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99) (en anglais).

## Étapes suivantes

[Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Sites Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Opérations sur Traffic Manager (Référence sur l’API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584)

[Applets de commande Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769)

<!---HONumber=Oct15_HO3-->