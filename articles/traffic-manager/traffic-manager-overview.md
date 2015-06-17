<properties 
   pageTitle="Vue d'ensemble de Traffic Manager"
   description="Cet article vous aidera à comprendre le principe et le fonctionnement de Traffic Manager"
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# Vue d'ensemble de Traffic Manager

Microsoft Azure Traffic Manager vous permet de contrôler la distribution du trafic utilisateur vers les points de terminaison que vous spécifiez, par exemple des sites web ou des services cloud Azure. Traffic Manager utilise un moteur de stratégie intelligent pour les requêtes DNS des noms de domaine de vos ressources Internet. Vos sites web ou services cloud Azure peuvent être exécutés dans différents centres de données à travers le monde.

Traffic Manager peut vous aider à atteindre les objectifs suivants :

- **Améliorer la disponibilité des applications critiques** - Traffic Manager vous permet d'améliorer la disponibilité de vos applications critiques en surveillant vos points de terminaison dans Azure et en apportant des fonctionnalités de basculement automatique lorsqu'un service cloud ou un site web Azure, ou un autre emplacement, n'est pas disponible.
- **Améliorer la réactivité des applications haute performance** - Azure vous permet d'exécuter des services cloud ou sites web dans différents centres de données à travers le monde. Traffic Manager peut améliorer la réactivité de vos applications et vos délais de diffusion de contenu en dirigeant les utilisateurs finaux vers le point de terminaison affichant la latence réseau la plus faible par rapport au client.
- **Mettre à niveau vos services et en assurer la maintenance sans temps d'arrêt** - Traffic Manager prend en charge une grande diversité de scénarios de déploiement de cloud hybride et sur site, notamment le " burst-to-cloud ", la migration vers le cloud et le basculement vers le cloud. Pour une maintenance planifiée, vous devez désactiver le point de terminaison dans Traffic Manager et attendre que celui-ci ait effectué la maintenance des connexions existantes. Lorsque plus aucun trafic n'aboutit au point de terminaison, vous devez mettre à jour le service sur ce point de terminaison et le tester, puis le réactiver dans Traffic Manager. Cela vous aide à mettre à niveau vos services et à en assurer la maintenance sans temps d'arrêt pour les clients.
- **Assurer la distribution du trafic pour les déploiements vastes et complexes** - Grâce aux profils Traffic Manager imbriqués, dans lesquels un profil Traffic Manager peut en contenir un autre faisant office de point de terminaison, vous pouvez créer des configurations pour optimiser les performances et la distribution dans le cadre de déploiements plus vastes et plus complexes. Pour plus d'informations, consultez la section [Profils imbriqués](#nested-profiles).

## Fonctionnement de Traffic Manager

Lorsque vous configurez un profil Traffic Manager, les paramètres que vous spécifiez fournissent à Traffic Manager les informations nécessaires pour déterminer le point de terminaison qui doit traiter la demande selon une requête DNS. Aucun trafic de point de terminaison ne transite réellement via Traffic Manager.

La * figure 1 * montre comment Traffic Manager dirige les utilisateurs vers l'un des ensembles de points de terminaison. Les chiffres de la figure 1 correspondent aux descriptions ci-après :

![Fonctionnement de Traffic Manager](./media/traffic-manager-overview/IC740854.jpg)

**Figure 1**

1. **Trafic utilisateur vers le nom de domaine d'entreprise** : Le client demande des informations à l'aide du nom de domaine d'entreprise. L'objectif est de résoudre un nom DNS en adresse IP. Les domaines d'entreprise doivent être réservés via des inscriptions de nom de domaine Internet standard qui sont gérées de façon externe à Traffic Manager. Dans la figure 1, le domaine d'entreprise indiqué en exemple est *www.contoso.com*.
2. **Nom de domaine d'entreprise pointant vers le nom de domaine Traffic Manager** : L'enregistrement de ressource DNS pour le domaine d'entreprise pointe vers un nom de domaine Traffic Manager géré dans Azure Traffic Manager. Pour cela, un enregistrement de ressource CNAME mappe le nom de domaine d'entreprise au nom de domaine Traffic Manager. Dans cet exemple, le nom de domaine Traffic Manager est *contoso.trafficmanager.net*.
3. **Profil et nom de domaine Traffic Manager** : Le nom de domaine Traffic Manager fait partie du profil Traffic Manager. Le serveur DNS de l'utilisateur envoie une nouvelle requête DNS pour le nom de domaine Traffic Manager (dans notre exemple, *contoso.trafficmanager.net*), reçue par les serveurs de noms DNS de Traffic Manager.
4. **Traitement des règles de profil Traffic Manager** : Traffic Manager utilise la méthode d'équilibrage de charge et l'état de la surveillance spécifiés pour déterminer le point de terminaison, Azure ou non, qui doit traiter la demande.
5. **Nom de domaine du point de terminaison envoyé à l'utilisateur** : Traffic Manager renvoie un enregistrement CNAME qui mappe le nom de domaine Traffic Manager au nom de domaine du point de terminaison. Le serveur DNS de l'utilisateur résout le nom de domaine du point de terminaison selon son adresse IP et l'envoie à l'utilisateur.
6. **L'utilisateur appelle le point de terminaison** : L'utilisateur appelle le point de terminaison retourné directement à l'aide de son adresse IP.

Dans la mesure où le domaine d'entreprise et l'adresse IP résolue sont mis en cache sur l'ordinateur client, l'utilisateur continue à interagir avec le point de terminaison choisi jusqu'à ce que son entrée de cache DNS locale expire. Il est important de noter que le client DNS met en cache les entrées d'hôte DNS pendant leur durée de vie (TTL, Time-to-Live). Le profil Traffic Manager n'est pas pris en compte lors de la récupération des entrées d'hôte à partir du cache du client DNS. Aussi, des retards de connexion peuvent se produire si le point de terminaison devient indisponible avant l'expiration de la TTL. Si la TTL d'une entrée d'hôte DNS dans le cache expire et que l'ordinateur client doit résoudre une nouvelle fois le nom de domaine d'entreprise, il envoie une nouvelle requête DNS. L'ordinateur client peut recevoir l'adresse IP d'un autre point de terminaison selon la méthode d'équilibrage de charge appliquée et l'intégrité des points de terminaison au moment de la requête.

## Mise en œuvre de Traffic Manager

La * figure 2 * illustre les étapes nécessaires à la mise en œuvre de Traffic Manager, dans leur ordre logique. Dès lors que vous maîtrisez la configuration de Traffic Manager et les meilleures pratiques, vous pouvez modifier légèrement l'ordre de ces étapes. Les chiffres de la figure 2 correspondent aux descriptions ci-après :

![Configuration de Traffic Manager](./media/traffic-manager-overview/IC740855.jpg)

**Figure 2**

1. **Déployez vos services cloud et sites web Azure ou d'autres points de terminaison dans votre environnement de production**. Lorsque vous créez un profil Traffic Manager, il doit être associé à un abonnement. Vous ajoutez ensuite les points de terminaison pour les services cloud et sites web de niveau Standard en production, qui font partie du même abonnement. Si un point de terminaison est dans un état intermédiaire et n'est pas dans un environnement de production Azure ou ne fait pas partie du même abonnement, il peut être ajouté comme point de terminaison externe. Pour plus d'informations sur les services cloud, consultez la page [Services cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074). Pour plus d'informations sur les sites web, consultez la page [Sites web](http://go.microsoft.com/fwlink/p/?LinkId=393327) (en anglais).
2. **Choisissez un nom pour votre domaine Traffic Manager**. Utilisez de préférence un préfixe unique pour le nom de votre domaine. La dernière partie du domaine, trafficmanager.net, est fixe. Pour plus d'informations, consultez la rubrique [Meilleures pratiques recommandées](#best-practices).
3. **Décidez de la configuration de surveillance que vous souhaitez utiliser**. Traffic Manager surveille les points de terminaison pour vérifier qu'ils sont en ligne, quelle que soit la méthode d'équilibrage de charge. Une fois que vous avez configuré les paramètres de surveillance, Traffic Manager ne dirige plus le trafic vers les points de terminaison considérés comme hors connexion par le système de surveillance, sauf s'il détecte que tous les points de terminaison sont hors connexion ou s'il ne peut pas détecter l'état des points de terminaison contenus dans le profil. Pour plus d'informations sur la surveillance, consultez la rubrique [Surveillance avec Traffic Manager](traffic-manager-monitoring.md).
4. **Décidez de la méthode d'équilibrage de charge à utiliser**. Trois méthodes d'équilibrage de charge sont disponibles. Prenez le temps de déterminer celle qui conviendra le mieux à vos besoins. Par la suite, vous pourrez changer de méthode à tout moment. Notez également que chaque méthode implique des étapes de configuration légèrement différentes. Pour plus d'informations sur les méthodes d'équilibrage de charge, consultez la rubrique [À propos des méthodes d'équilibrage de charge dans Traffic Manager](traffic-manager-load-balancing-methods.md).
5. **Créez votre profil et configurez les paramètres**. Vous pouvez utiliser les API REST, Windows PowerShell ou le Portail de gestion pour créer votre profil Traffic Manager et configurer les paramètres. Pour plus d'informations, consultez la section [Configuration des paramètres Traffic Manager](#how-to-configure-traffic-manager-settings). Les étapes suivantes supposent que vous utiliserez l'option **Création rapide** du Portail de gestion. 
   - **Créer votre profil Traffic Manager** - Pour créer un profil à l'aide de l'option Création rapide du Portail de gestion, consultez la rubrique [Gérer des profils Traffic Manager](traffic-manager-manage-profiles.md).
   - **Configurer les paramètres de la méthode d'équilibrage de charge** - Toujours avec l'option Création rapide, vous devez sélectionner la méthode d'équilibrage de charge pour votre profil. Ce paramètre peut être modifié à tout moment, une fois réalisées les étapes de l'option Création rapide. Pour ce qui concerne les étapes de configuration, consultez la rubrique correspondant à votre méthode d'équilibrage de charge : [Configurer l'équilibrage de charge des performances](traffic-manager-configure-performance-load-balancing.md), [Configurer l'équilibrage de charge de basculement](traffic-manager-configure-failover-load-balancing.md), [Configurer l'équilibrage de charge tourniquet (round robin)](traffic-manager-configure-round-robin-load-balancing.md).
   >[AZURE.NOTE] La méthode d'équilibrage de charge tourniquet (round robin) prend désormais en charge la distribution pondérée du trafic réseau. Toutefois, pour le moment, vous devez utiliser les API REST ou Windows PowerShell pour configurer la pondération. Pour plus d'informations et pour obtenir un exemple de configuration, consultez la page [Points de terminaison externes Azure Traffic Manager et méthode tourniquet (round robin) pondérée via PowerShell](http://azure.microsoft.com/blog/2014/06/26/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/) (en anglais) sur le blog Azure.

   - **Configurer les points de terminaison** - Les points de terminaison ne sont pas configurés à l'aide de l'option Création rapide. Après avoir créé votre profil et spécifié votre méthode d'équilibrage de charge, vous devez indiquer à Traffic Manager les points de terminaison. Pour connaître la procédure de configuration des points de terminaison, consultez la rubrique [Gérer des points de terminaison dans Traffic Manager](traffic-manager-endpoints.md)

   - **Configurer les paramètres de surveillance** - Les paramètres de surveillance ne sont pas configurés à l'aide de l'option Création rapide. Après avoir créé votre profil et spécifié votre méthode d'équilibrage de charge, vous devez indiquer à Traffic Manager les éléments à surveiller. Pour connaître la procédure de configuration de la surveillance, consultez la rubrique [Surveillance avec Traffic Manager](traffic-manager-monitoring.md).
6. **Testez votre profil Traffic Manager**. Vérifiez que votre profil et votre domaine fonctionnent comme prévu. Pour plus d'informations, consultez la rubrique [Test des paramètres de Traffic Manager](traffic-manager-testing-settings.md).
7. **Redirigez l'enregistrement de ressource DNS du nom de domaine d'entreprise vers le profil pour le rendre actif**. Pour plus d'informations, consultez la rubrique [Rediriger un domaine Internet d'entreprise vers un domaine Traffic Manager](traffic-manager-point-internet-domain.md).

À l'aide de l'exemple de la figure 1, vous devez remplacer l'enregistrement de ressource DNS sur vos serveurs par la ligne suivante, afin de rediriger le nom de domaine d'entreprise vers le nom de domaine Traffic Manager :
    www.contoso.com IN CNAME contoso.trafficmanager.net

## Configuration des paramètres de Traffic Manager

Vous pouvez configurer les paramètres de Traffic Manager à l'aide du Portail de gestion, avec les API REST et avec les applets de commande Windows PowerShell.

Même si tous les éléments d'API REST ne sont pas visibles dans le Portail de gestion, de nombreux paramètres sont disponibles avec chacune des méthodes. Pour plus d'informations sur l'utilisation des API REST, consultez la page [Opérations pour Traffic Manager (informations de référence sur l'API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584).

Pour plus d'informations sur les applets de commande Windows PowerShell pour Traffic Manager, consultez la page [Applets de commande Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769) (en anglais).

>[AZURE.NOTE] Il n'existe actuellement aucune prise en charge pour la configuration des points de terminaison externes (type = " Any "), des pondérations pour la méthode d'équilibrage de charge tourniquet (round robin) et des profils imbriqués avec le Portail de gestion. Vous devez utiliser REST (consultez la page [Créer une définition](http://go.microsoft.com/fwlink/p/?LinkId=400772)) ou Windows PowerShell (consultez la page [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)).

### Configuration des paramètres dans le Portail de gestion

Dans le Portail de gestion, vous pouvez créer votre profil Traffic Manager à l'aide de l'option Création rapide. Cette option vous permet de créer un profil de base. Après avoir créé votre profil, vous pouvez configurer des paramètres supplémentaires ou modifier les paramètres que vous avez précédemment configurés. Pour plus d'informations sur la création de votre profil Traffic Manager à l'aide de l'option Création rapide, consultez la rubrique [Gérer des profils Traffic Manager](traffic-manager-manage-profiles.md).

Vous pouvez configurer les paramètres suivants dans le Portail de gestion :

- **Préfixe DNS** - Un préfixe unique que vous créez. Les profils sont affichés par préfixe dans le Portail de gestion.
- **TTL du DNS** - La durée de vie (TTL, Time-to-Live) du DNS contrôle la fréquence à laquelle le serveur de noms cache local du client interroge le système DNS Azure Traffic Manager pour une mise à jour des entrées DNS.
- **Abonnement** - Sélectionnez l'abonnement auquel correspond votre profil. Notez que cette option apparaît uniquement si vous avez plusieurs abonnements.
- **Méthode d'équilibrage de charge** - Méthode avec laquelle vous souhaitez que Traffic Manager gère l'équilibrage de charge.
- **Ordre de basculement** - Ordre des points de terminaison lorsque vous utilisez la méthode d'équilibrage de charge de basculement.
- **Surveillance** - Les paramètres de surveillance incluent le protocole (HTTP ou HTTPS), le port, le chemin d'accès relatif et le nom de fichier.

### Configuration des paramètres à l'aide des API REST

Vous pouvez créer et configurer votre profil Traffic Manager à l'aide des API REST. Pour plus d'informations, consultez la page [Opérations pour Traffic Manager (informations de référence sur l'API REST)](http://go.microsoft.com/fwlink/?LinkId=313584).

- **Profil** - Un profil contient un préfixe de nom de domaine que vous créez. Chaque profil correspond à votre abonnement. Vous pouvez créer plusieurs profils par abonnement. Le nom du profil est visible dans le Portail de gestion. Le nom que vous créez, contenu dans le profil, est considéré comme votre domaine Traffic Manager.
- **Définition** - Une définition inclut des paramètres de stratégie et d'analyse. Une définition correspond à un profil. Vous ne pouvez avoir qu'une définition par profil. La définition proprement dite n'est pas visible dans le Portail de gestion, même si de nombreux paramètres contenus dans la définition sont visibles et peuvent être configurés dans le Portail de gestion.
- **Options DNS** - Chaque définition inclut des options DNS. C'est à ce niveau que la TTL du DNS est configurée.
- **Analyseurs** - Chaque définition inclut des paramètres d'analyse. C'est à ce niveau que le protocole et le port, ainsi que le chemin d'accès relatif et le nom du fichier sont configurés. Les paramètres d'analyse sont visibles et peuvent être configurés dans le Portail de gestion. Pour plus d'informations, consultez la rubrique [Surveillance avec Traffic Manager](traffic-manager-monitoring.md)..
- **Stratégie** - Chaque définition inclut des paramètres de stratégie. C'est dans le cadre de la stratégie que sont spécifiés les méthodes d'équilibrage de charge et les points de terminaison. La stratégie proprement dite n'est pas visible dans le Portail de gestion, même si certains paramètres connexes sont visibles et peuvent être configurés dans le Portail de gestion. Pour plus d'informations, consultez la page [À propos des méthodes d'équilibrage de charge dans Traffic Manager](traffic-manager-load-balancing-methods.md).

## Configuration des paramètres à l'aide de Windows PowerShell

Vous pouvez créer et configurer votre profil Traffic Manager à l'aide de Windows PowerShell. Pour plus d'informations, consultez la page [Applets de commande Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769) (en anglais).

## Meilleures pratiques

- **Assurez-vous que vos préfixes sont uniques et faciles à comprendre** - Le nom DNS de votre profil Traffic Manager doit être unique. Vous pouvez contrôler la première partie du nom DNS uniquement. Le nom de domaine Traffic Manager est utilisé uniquement pour l'identification et l'acheminement des requêtes de client. Les ordinateurs client n'affichent jamais ces noms à l'attention de l'utilisateur final. Toutefois, les profils sont identifiés par ce nom de domaine. Il est donc important que vous puissiez l'identifier rapidement parmi les noms de domaine répertoriés dans le Portail de gestion.
- **Utilisez des points pour renforcer le caractère unique des noms de domaine ou les rendre lisibles** - Vous pouvez également utiliser des points pour séparer les différentes parties du préfixe de votre nom de domaine.  Si vous envisagez de créer plusieurs stratégies dans Traffic Manager, utilisez une hiérarchie cohérente pour différencier les services. Par exemple, Contoso dispose de services globaux pour le Web, la facturation et la gestion de l'utilitaire. Les trois stratégies seront donc *web.contoso.trafficmanager.net*, *bill.contoso.trafficmanager.net*, et *util.contoso.trafficmanager.net*. Lorsque vous configurez des services cloud ou des sites web, utilisez des noms qui incluent l'emplacement. Par exemple, *web-us-contoso.cloudapp.net* et *web-asia-contoso.cloudapp.net*. Les limites sont celles imposées par le DNS. Imaginons qu'un nom de domaine consiste en une suite d'étiquettes séparées par des points (label.label.label.label.etc.).  Au moment de cette documentation, les limites imposées pour les noms de domaine dans Traffic Manager sont les suivantes :
   - Chaque étiquette peut comporter un maximum de 63 caractères.
   - Vous ne pouvez avoir plus de 40 étiquettes au total. Étant donné que deux étiquettes sont réservées par trafficmanager.net, il reste 38 étiquettes pour votre préfixe.
   - Le nom de domaine complet peut comporter un maximum de 253 caractères. Gardez à l'esprit que 19 de ces caractères sont réservés par trafficmanager.net.
- **TTL du DNS** - La TTL du DNS contrôle la fréquence à laquelle façon le serveur de noms cache local du client interroge le système DNS Azure Traffic Manager pour une mise à jour des entrées DNS. Toute modification dans Traffic Manager, notamment à l'égard du profil ou de la disponibilité du point de terminaison, utilisera cette période pour son actualisation à l'échelle du système global de serveurs DNS. Nous vous recommandons de conserver la valeur par défaut de ce paramètre, soit 300 secondes (5 minutes). Une valeur supérieure augmente la durée pendant laquelle les réponses DNS de Traffic Manager sont mises en cache par les clients et les résolutions DNS, ce qui réduit la latence globale des requêtes DNS. Toutefois, une valeur inférieure est préférable si un basculement très rapide est requis.
- **Les points de terminaison doivent correspondre à un même abonnement** - Tous les points de terminaison doivent correspondre à l'abonnement dans lequel vous créez le profil. Vous pouvez ajouter des points de terminaison de différents abonnements à un profil en tant que points de terminaison externes, mais Azure ne les supprimera pas automatiquement si vous désactivez ou supprimez le service associé. Ainsi, les points de terminaison externes restent dans le profil Traffic Manager et vous continuerez à être facturé en conséquence, sauf si vous les supprimez manuellement.
- **Services de production uniquement** - Seuls les points de terminaison d'un environnement de production sont disponibles. Vous ne pouvez pas diriger de trafic vers les points de terminaison exécutés dans un environnement intermédiaire. Notez que si vous effectuez un échange d'adresses IP virtuelles alors qu'un profil dirige le trafic, le trafic utilisera le point de terminaison qui vient d'être échangé dans l'environnement de production.
- **Nommez vos points de terminaison afin qu'ils soient facilement identifiables** - Tenez compte du préfixe DNS que vous souhaitez utiliser. Le nom DNS est utilisé car il est forcément unique dans un abonnement, tandis que le nom du service cloud ou du site web ne l'est peut-être pas. Pour éviter les confusions, affectez à un service cloud ou un site web un nom et un préfixe DNS identiques ou similaires. Si vous avez plus de 20 services cloud et sites web, une affectation de noms incohérente peut compliquer la recherche de point de terminaison. En outre, les points de terminaison qui ne sont pas nommés correctement compliqueront la maintenance des profils.
- **Tous les points de terminaison d'un profil doivent traiter les mêmes opérations et les mêmes ports** - Si vous combinez vos points de terminaison, les risques de voir un client appeler un point de terminaison qui ne peut pas traiter sa demande augmentent.
- **Tous les services cloud d'un profil doivent utiliser les mêmes paramètres de surveillance** - Vous pouvez choisir un seul chemin d'accès et un seul fichier pour surveiller tous les points de terminaison d'une définition spécifique. Vous pouvez entrer le caractère " / " dans la zone de texte **Chemin d'accès relatif et nom de fichier** de sorte que le procédé de surveillance tente d'accéder au chemin d'accès et au nom de fichier par défaut.
- **Désactivez les points de terminaison pour les modifications temporaires au lieu de modifier votre configuration** - Dans de nombreux cas, vous pouvez être amené à mettre un point de terminaison hors connexion. Plutôt que de supprimer le point de terminaison de votre profil, désactivez simplement le point de terminaison concerné dans votre profil. Ainsi, le point de terminaison reste dans le profil, mais le profil agit comme si le point de terminaison n'y était pas inclus. Cela s'avère particulièrement utile pour supprimer temporairement un point de terminaison en mode de maintenance ou redéployé. Une fois le point de terminaison à nouveau opérationnel, vous pouvez l'activer. Pour plus d'informations, consultez la rubrique [Gérer des points de terminaison dans Traffic Manager](traffic-manager-endpoints.md).
- **En cas de modifications temporaires, désactivez un profil au lieu de le supprimer** - Vous pouvez être amené à mettre hors connexion un profil complet et non seulement des points de terminaison spécifiés dans ce profil. Pour ce faire, désactivez le profil. Lorsque vous désactivez un profil, tous les paramètres restent disponibles pour modification dans le Portail de gestion. Ainsi, vous pouvez remettre le profil en ligne rapidement et facilement lorsque vous souhaitez l'utiliser à nouveau. Pour plus d'informations, consultez la rubrique [Gérer des points de terminaison dans Traffic Manager](traffic-manager-endpoints.md).
- **Stockage** - La conception de l'emplacement et de la distribution de votre stockage est un aspect majeur à prendre en compte lors de l'utilisation de Traffic Manager. Considérez la transaction de bout en bout et le flux de vos données lorsque vous concevez et déployez vos applications pour Traffic Manager.
- **SQL Azure** - Tout comme pour la conception du stockage, analysez l'état de votre application et vos besoins en termes de données lorsque vous étendez vos points de terminaison sur plusieurs zones géographiques.

## Profils imbriqués

Vous pouvez spécifier le nom d'un autre profil Traffic Manager en tant que point de terminaison. Vous créez alors des profils imbriqués. Le nom du profil Traffic Manager est son nom DNS, par exemple contoso-europe.trafficmanager.net.

Cela vous permet de configurer Traffic Manager de sorte que les requêtes de nom DNS entrantes soient analysées dans un ensemble de niveaux. Vous pouvez ainsi vous assurer que le client demandeur est dirigé vers l'ensemble de points de terminaison approprié. La *Figure 3* illustre ce concept.

![Exemples de profils Traffic Manager imbriqués](./media/traffic-manager-overview/IC751072.png)

**Figure 3**

Vous pouvez imbriquer jusqu'à 10 niveaux, et chaque profil peut être configuré avec une méthode d'équilibrage de charge différente.

Par exemple, vous pouvez créer une configuration selon les niveaux suivants :

- Au niveau supérieur (le profil Traffic Manager mappé à votre nom DNS externe), vous pouvez configurer le profil avec la méthode d'équilibrage de charge des performances.
- Au niveau intermédiaire, un ensemble de profils Traffic Manager représente différents centres de données et utilise la méthode d'équilibrage de charge tourniquet (round robin).
- Au niveau inférieur, un ensemble de points de terminaison de service cloud dans chaque service de centre de données demandé par le trafic de l'utilisateur.

En conséquence, les utilisateurs sont dirigés vers un centre de données approprié sur le plan régional selon la performance et vers un service cloud de ce centre de données selon une distribution de charge identique ou pondérée. Par exemple, la pondération peut être utilisée pour distribuer un faible pourcentage du trafic vers un déploiement nouveau ou d'évaluation à des fins de test ou pour les commentaires client.

La *figure 4* illustre la configuration.

![Exemples des profils Traffic Manager à plusieurs niveaux](./media/traffic-manager-overview/IC751073.png)

**Figure 4**

Dans la *Figure 4*, le profil Traffic Manager du niveau supérieur est un profil parent et les profils Traffic Manager du niveau intermédiaire sont des profils enfants.

Si Traffic Manager dirige les utilisateurs vers un profil enfant comprenant un petit nombre de points de terminaison intègres, ceux-ci peuvent être surchargés, ce qui entraîne des problèmes de performance. Pour éviter cette situation, vous pouvez configurer le profil Traffic Manager parent avec un seuil de points de terminaison intègres qui détermine si des points de terminaison inclus dans les profils enfants de ce parent peuvent recevoir du trafic. Par exemple, si vous souhaitez vous assurer que les profils enfants incluent au moins trois points de terminaison intègres, vous devez définir cette valeur de seuil à 3. Dans l'exemple de la figure 4, vous devez configurer le profil Traffic Manager de niveau supérieur pour ce seuil.

Pour ajouter un profil Traffic Manager en tant que point de terminaison et configurer le nombre minimal de points de terminaison intègres, vous devez utiliser REST (consultez la page [Créer une définition](http://go.microsoft.com/fwlink/p/?LinkId=400772)) ou Windows PowerShell (consultez la page [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)). Vous ne pouvez pas utiliser le Portail de gestion.

## Figures Traffic Manager

Si vous souhaitez récupérer les figures de cette rubrique sous forme de diapositives PowerPoint pour votre propre présentation de Traffic Manager ou pour les modifier, consultez la page [Figures Traffic Manager dans la documentation MSDN](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99) (en anglais).

## Voir aussi

[Services cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Sites web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Opérations pour Traffic Manager (informations de référence sur l'API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584)

[Applets de commande Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769)

<!--HONumber=49--> 