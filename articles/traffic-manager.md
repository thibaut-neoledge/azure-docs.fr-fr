<properties
   pageTitle="Vue d'ensemble de Traffic Manager"
   description="Cet article contient la présentation technique de Traffic Manager"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/23/2015"
   ms.author="joaoma" />

# Vue d'ensemble de Traffic Manager

Microsoft Azure Traffic Manager vous permet de contrôler la distribution du trafic utilisateur vers vos points de terminaison spécifiés, qui peuvent inclure des sites web, des services cloud Azure et d'autres points de terminaison. Traffic Manager utilise un moteur de stratégie intelligent pour les requêtes DNS des noms de domaine de vos ressources Internet. Vos sites web ou services cloud Azure peuvent être exécutés dans différents centres de données à travers le monde.

Traffic Manager vous aide à effectuer les opérations suivantes :

-**Améliorer la disponibilité des applications critiques :** Traffic Manager vous permet d'améliorer la disponibilité de vos applications critiques en surveillant vos points de terminaison dans Azure et en fournissant des fonctionnalités de basculement automatique quand un service cloud Azure, un site web Azure ou un autre emplacement tombe en panne.

-**Améliorer la réactivité des applications haute performance  :** Azure vous permet d'exécuter des sites web ou des services cloud dans des centres de données situés dans le monde entier. Traffic Manager peut améliorer la réactivité de vos applications et les délais de distribution de contenu en dirigeant les utilisateurs finaux vers le point de terminaison présentant la latence réseau la plus faible par rapport au client.

-**Mettre à niveau et gérer les services sans les interrompre :** Traffic Manager prend en charge les scénarios étendus pour les déploiements locaux et cloud hybrides, notamment les scénarios qui impliquent le recours au cloud suite à un accroissement important du volume de données ou dans le cadre d'une migration ou d'un basculement. Pour une maintenance planifiée, vous désactivez le point de terminaison dans Traffic Manager et attendez qu'il effectue la maintenance des connexions existantes. Quand le point de terminaison ne reçoit plus de trafic, vous mettez à jour le service sur ce point de terminaison, vous testez celui-ci, puis vous le réactivez dans Traffic Manager. Cela vous aide à gérer et à mettre à niveau vos services sans temps mort pour les clients.

-**Assurer la distribution de trafic pour des déploiements vastes et complexes :** grâce à l'imbrication de profils Traffic Manager, qui permet d'intégrer à un profil Traffic Manager un autre profil Traffic Manager en tant que point de terminaison, vous pouvez créer des configurations pour optimiser les performances et la distribution pour les déploiements plus volumineux et plus complexes. Pour plus d'informations, consultez [Profils imbriqués](#Nested).

## Fonctionnement de Traffic Manager

Quand vous configurez un profil Traffic Manager, les paramètres que vous spécifiez fournissent à Traffic Manager les informations nécessaires pour déterminer le point de terminaison qui doit traiter la demande en fonction d'une requête DNS. Aucun trafic de point de terminaison ne transite réellement par Traffic Manager.

La figure 1 montre comment Traffic Manager dirige les utilisateurs vers un des points de terminaison. Les nombres dans la figure 1 correspondent aux descriptions numérotées plus bas :

![Fonctionnement de Traffic Manager](./media/traffic-manager/IC740854.jpg)

Figure 1

1. **Trafic utilisateur vers le nom de domaine d'entreprise :** le client demande des informations à l'aide du nom de domaine d'entreprise. L'objectif est de résoudre un nom DNS en adresse IP. Les domaines d'entreprise doivent être réservés via des inscriptions de nom de domaine Internet normales qui sont gérées en dehors de Traffic Manager. Dans la figure 1, l'exemple de domaine d'entreprise est www.contoso.com.
1. **Du nom de domaine d'entreprise vers le nom de domaine Traffic Manager :** l'enregistrement de ressource DNS pour le domaine d'entreprise pointe vers un nom de domaine Traffic Manager géré dans Azure Traffic Manager. Pour cela, un enregistrement de ressource CNAME mappe le nom de domaine d'entreprise au nom de domaine Traffic Manager. Dans l'exemple, le nom de domaine Traffic Manager est contoso.trafficmanager.net.
1. **Profil et nom de domaine Traffic Manager :** le nom de domaine Traffic Manager fait partie du profil Traffic Manager. Le serveur DNS de l'utilisateur envoie une nouvelle requête DNS pour le nom de domaine Traffic Manager (dans notre exemple, contoso.trafficmanager.net), qui est reçue par les serveurs de noms DNS Traffic Manager.
1. **Traitement des règles de profil Traffic Manager :** Traffic Manager utilise la méthode d'équilibrage de charge spécifiée et l'état de la surveillance pour déterminer quel point de terminaison (Azure ou autre) doit traiter la demande.
1. **Envoi du nom de domaine du point de terminaison à l'utilisateur :** Traffic Manager renvoie un enregistrement CNAME qui mappe le nom de domaine Traffic Manager au nom de domaine du point de terminaison. Le serveur DNS de l'utilisateur résout le nom de domaine du point de terminaison en son adresse IP et l'envoie à l'utilisateur.
1. **Appel du point de terminaison par l'utilisateur :** l'utilisateur appelle directement le point de terminaison retourné à l'aide de son adresse IP.

Étant donné que le domaine d'entreprise et l'adresse IP résolue sont mis en cache sur l'ordinateur client, l'utilisateur continue à interagir avec le point de terminaison choisi jusqu'à ce que son entrée de cache DNS locale expire. Il est important de noter que le client DNS met en cache les entrées d'hôte DNS pendant leur durée de vie. La récupération des entrées d'hôte à partir du cache du client DNS ignore le profil Traffic Manager, et vous pouvez rencontrer des délais de connexion si le point de terminaison devient indisponible avant l'expiration de la durée de vie. Si la durée de vie d'une entrée d'hôte DNS dans le cache expire et que l'ordinateur client doit de nouveau résoudre le nom de domaine d'entreprise, il envoie une requête DNS. L'ordinateur client peut recevoir l'adresse IP d'un autre point de terminaison suivant la méthode d'équilibrage de charge appliquée et l'intégrité des points de terminaison au moment de la demande.

## Comment implémenter Traffic Manager


La figure 2 montre les étapes, dans l'ordre, à suivre pour implémenter Traffic Manager. Vous pouvez effectuer ces étapes dans un ordre légèrement différent une fois que vous maîtrisez la configuration de Traffic Manager et les meilleures pratiques. Les nombres dans la figure 2 correspondent aux descriptions numérotées plus bas :

![Comment configurer Traffic Manager](./media/traffic-manager/IC740855.jpg)

Figure 2

1. **Déployer vos services cloud Azure, sites web Azure ou autres points de terminaison dans votre environnement de production.** Quand vous créez un profil Traffic Manager, il doit être associé à un abonnement. Vous ajoutez ensuite les points de terminaison pour les services cloud et les sites web de niveau Standard en production qui font partie du même abonnement. Si un point de terminaison est dans un environnement intermédiaire et qu'il ne se trouve pas dans un environnement de production Azure ou qu'il n'appartient pas au même abonnement, il peut être ajouté comme point de terminaison externe. Pour plus d'informations sur les services cloud, consultez [Services cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074). Pour plus d'informations sur les sites web, consultez [Sites web](http://go.microsoft.com/fwlink/p/?LinkId=393327).
1. **Choisir un nom pour votre domaine Traffic Manager.** Choisissez un nom pour votre domaine avec un préfixe unique. La dernière partie du domaine, trafficmanager.net, est fixe. Pour plus d'informations, consultez [Meilleures pratiques](#bkmk_TrafficManagerBestPracticesProfile).
1. **Choisir la configuration de surveillance à utiliser.** Traffic Manager surveille les points de terminaison pour qu'ils soient en ligne, quelle que soit la méthode d'équilibrage de charge. Une fois que vous avez configuré les paramètres de surveillance, Traffic Manager n'envoie aucun trafic aux points de terminaison qui sont hors connexion d'après le système de surveillance, sauf s'il détecte que tous les points de terminaison sont hors connexion ou qu'il ne peut détecter l'état d'aucun point de terminaison contenu dans le profil. Pour plus d'informations sur la surveillance, consultez [À propos de la surveillance Traffic Manager](https://msdn.microsoft.com/library/azure/dn339013.aspx).
1. **Choisir la méthode d'équilibrage de charge à utiliser.** Trois méthodes d'équilibrage de charge différentes sont disponibles. Prenez le temps d'analyser la méthode qui vous convient le mieux. Vous pourrez changer de méthode à tout moment. Notez également que chaque méthode nécessite des étapes de configuration légèrement différentes. Pour plus d'informations sur les méthodes d'équilibrage de charge, consultez [À propos des méthodes d'équilibrage de charge de Traffic Manager](https://msdn.microsoft.com/library/azure/dn339010.aspx).
1. **Créer votre profil et configurer les paramètres.** Vous pouvez utiliser l'API REST, Windows PowerShell ou le portail de gestion pour créer votre profil Traffic Manager et configurer les paramètres. Pour plus d'informations, consultez [Comment configurer les paramètres Traffic Manager](#Configure). Les étapes suivantes supposent que vous allez utiliser l'option Création rapide dans le portail de gestion.
	- 	**Créer votre profil Traffic Manager :** pour créer un profil à l'aide de l'option Création rapide dans le portail de gestion, consultez [Création d'un profil Traffic Manager à l'aide de l'option Création rapide](https://msdn.microsoft.com/library/azure/dn339012.aspx).
	- 	**Configurer les paramètres de la méthode d'équilibrage de charge **: en mode Création rapide, vous devez sélectionner la méthode d'équilibrage de charge pour votre profil. Vous pouvez modifier ce paramétrage à tout moment après avoir effectué les étapes de création rapide. Pour les étapes de configuration, consultez la rubrique qui correspond à votre méthode d'équilibrage de charge : [Configurer l'équilibrage de charge basé sur les performances](https://msdn.microsoft.com/library/azure/hh744835.aspx)
	
	[AZURE.NOTE]**La méthode d'équilibrage de charge de tourniquet prend maintenant en charge une répartition pondérée du trafic réseau. Toutefois, pour le moment, vous devez utiliser des API REST ou Windows PowerShell pour configurer la pondération. Pour plus d'informations et un exemple de configuration, consultez [Azure Traffic Manager External Endpoints and Weighted Round Robin via PowerShell](https://msdn.microsoft.com/library/azure/hh744829.aspx) sur le blog Azure.**
	- 	**Configurer les points de terminaison :** les points de terminaison ne sont pas configurés en mode Création rapide. Après avoir créé votre profil en spécifiant votre méthode d'équilibrage de charge, vous devez indiquer les points de terminaison à Traffic Manager.
	- 	**Configurer les paramètres de surveillance :** les paramètres de surveillance ne sont pas configurés en mode Création rapide. Après avoir créé votre profil en spécifiant votre méthode d'équilibrage de charge, vous devez indiquer à Traffic Manager ce qu'il doit surveiller. Pour savoir comment configurer la surveillance, consultez [Configurer la surveillance Traffic Manager](configure-traffic-manager-monitoring.md).
1. **Tester votre profil Traffic Manager.** Vérifiez que votre profil et votre domaine fonctionnent comme prévu. Pour plus d'informations, consultez [Test des paramètres de Traffic Manager](testing-traffic-manager-settings.md).
1. **Pointer l'enregistrement de ressource DNS du nom de votre domaine d'entreprise vers le profil pour l'activer.** Pour plus d'informations, consultez [Redirection d'un domaine Internet d'entreprise vers un domaine Traffic Manager](point-a-company-internet-domain-to-a-traffic-manager-domain.md).

Sur la base de l'exemple de la figure 1, vous devez configurer comme suit l'enregistrement de ressource DNS sur vos serveurs pour pointer le nom de domaine d'entreprise sur le nom de domaine Traffic Manager :

    www.contoso.com IN CNAME contoso.trafficmanager.net


## Comment configurer les paramètres Traffic Manager


Vous pouvez configurer les paramètres Traffic Manager à l'aide du portail de gestion, de l'API REST et d'applets de commande Windows PowerShell.

Bien que chaque élément d'API REST ne soit pas visible dans le portail de gestion, de nombreux paramètres sont disponibles à l'aide de ces méthodes. Pour plus d'informations sur l'utilisation des API REST, consultez [Opérations sur Traffic Manager (informations de référence sur les API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584).


Pour plus d'informations sur les applets de commande Windows PowerShell pour Traffic Manager, consultez [Applets de commande Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769).


[AZURE.NOTE] **Actuellement, le portail de gestion ne permet pas de configurer des points de terminaison externes (type = " Any "), des pondérations pour la méthode d'équilibrage de charge de tourniquet et des profils imbriqués. Vous devez utiliser REST (consultez " Créer une définition ") ou Windows PowerShell (consultez " Add-AzureTrafficManagerEndpoint ").**

### Configuration des paramètres dans le portail de gestion


Dans le portail de gestion, vous pouvez créer votre profil Traffic Manager à l'aide de l'option Création rapide. L'option Création rapide vous permet de créer un profil de base. Après avoir créé votre profil, vous pouvez configurer des paramètres supplémentaires ou modifier les paramètres que vous avez précédemment configurés. Pour plus d'informations sur la création de votre profil Traffic Manager à l'aide de l'option Création rapide, consultez " Création d'un profil Traffic Manager à l'aide de l'option Création rapide ".

[Création d'un profil Traffic Manager à l'aide de l'option Création rapide](https://msdn.microsoft.com/library/azure/dn339012.aspx)

Vous pouvez configurer les paramètres suivants dans le portail de gestion :

-**Préfixe du DNS :** préfixe unique que vous créez. Les profils sont affichés dans le portail de gestion par préfixe.

-**TTL du DNS :** cette valeur contrôle la fréquence à laquelle le serveur de noms cache local du client interroge le système DNS Traffic Manager pour connaître les entrées DNS mises à jour.

-**Abonnement :** sélectionnez l'abonnement qui correspond à votre profil. Notez que cette option n'apparaît que si vous avez plusieurs abonnements.

-**Méthode d'équilibrage de charge :** façon dont vous souhaitez que Traffic Manager gère l'équilibrage de charge.

-**Ordre de basculement :** ordre des points de terminaison quand vous utilisez la méthode d'équilibrage de charge par basculement.

-**Surveillance :** les paramètres de surveillance comprennent le protocole (HTTP ou HTTPS), le port, le chemin d'accès relatif et le nom de fichier.

### Configuration des paramètres à l'aide d'API REST


Vous pouvez créer et configurer votre profil Traffic Manager à l'aide d'API REST. Pour plus d'informations, consultez [Opérations sur Traffic Manager (informations de référence sur les API REST)](http://go.microsoft.com/fwlink/?LinkId=313584).

-**Profil :** un profil contient un préfixe de nom de domaine que vous créez. Chaque profil correspond à votre abonnement. Vous pouvez créer plusieurs profils par abonnement. Le nom du profil est visible dans le portail de gestion. Le nom que vous créez, contenu dans le profil, est appelé domaine Traffic Manager.

-**Définition :** une définition contient des paramètres de stratégie et des paramètres de surveillance. Une définition correspond à un profil. Vous ne pouvez avoir qu'une seule définition par profil. La définition proprement dite n'est pas visible dans le portail de gestion, bien que la plupart des paramètres contenus dans la définition soient visibles et puissent être configurés dans le portail de gestion.

-**Options DNS :** chaque définition comprend des options DNS. Celles-ci permettent de configurer le TTL du DNS.

-**Paramètres de surveillance :** chaque définition comprend des paramètres de surveillance. Ceux-ci permettent de configurer le protocole, le port, le nom de fichier et le chemin d'accès relatif. Les paramètres de surveillance sont visibles et peuvent être configurés dans le portail de gestion. Pour plus d'informations, consultez [À propos de la surveillance Traffic Manager](https://msdn.microsoft.com/library/azure/dn339013.aspx).

-**Stratégie :** chaque définition comprend des paramètres de stratégie. La stratégie spécifie les méthodes d'équilibrage de charge et les points de terminaison. La stratégie proprement dite n'est pas visible dans le portail de gestion, bien que certains paramètres de la stratégie soient visibles et puissent être configurés dans le portail de gestion. Pour plus d'informations, consultez [À propos des méthodes d'équilibrage de charge dans Traffic Manager](about-traffic-manager-balancing-methods.md).

## Configuration des paramètres à l'aide de Windows PowerShell

Vous pouvez créer et configurer votre profil Traffic Manager à l'aide de Windows PowerShell. Pour plus d'informations, consultez [Applets de commande Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769).

## <a name=bkmk_TrafficManagerBestPracticesProfile>Best practices</a>

- **Rendez vos préfixes uniques et faciles à comprendre : le nom DNS de votre profil Traffic Manager doit être unique. Vous pouvez contrôler la première partie du nom DNS uniquement. Le nom de domaine Traffic Manager est uniquement utilisé pour l'identification et la redirection des demandes du client. Les ordinateurs clients n'affichent jamais ces noms à l'utilisateur final. Toutefois, les profils étant identifiés par ce nom de domaine, il est important que vous puissiez l'identifier rapidement parmi les autres noms de domaine répertoriés dans le portail de gestion.
- **Utilisez des points pour renforcer l'unicité ou la lisibilité des noms de domaine : vous pouvez également utiliser des points pour séparer les parties du préfixe de votre nom de domaine.  Si vous envisagez de créer plusieurs stratégies dans Traffic Manager, utilisez une hiérarchie cohérente pour différencier un service d'un autre. Par exemple, Contoso a des services globaux pour le web, la facturation et la gestion de l'utilitaire. Les trois stratégies seraient web.contoso.trafficmanager.net, bill.contoso.trafficmanager.net (facturation) et util.contoso.trafficmanager.net (gestion de l'utilitaire). Quand vous configurez des services cloud ou des sites web, utilisez des noms qui incluent l'emplacement. Par exemple, web-us-contoso.cloudapp.net et web-asia-contoso.cloudapp.net. Les limites sont celles imposées par DNS. Un nom de domaine est une suite d'étiquettes séparées par des points(étiquette.étiquette.étiquette.étiquette.etc.). Au moment de la rédaction de cette documentation, les limites pour les noms de domaine dans Traffic Manager sont les suivantes :
	- Chaque étiquette peut comporter un maximum de 63 caractères.
	- Vous ne pouvez avoir plus de 40 étiquettes au total. Étant donné que deux étiquettes sont utilisées par trafficmanager.net, il vous en reste 38 pour votre préfixe.
	- Le nom de domaine complet peut comporter un maximum de 253 caractères. Gardez à l'esprit que trafficmanager.net utilise 19 de ces caractères.
- **TTL du DNS -**  cette valeur contrôle la fréquence à laquelle le serveur de noms cache local du client interroge le système DNS Traffic Manager pour connaître les entrées DNS mises à jour. Toute modification dans Traffic Manager, telles que les modifications de profil ou de disponibilité du point de terminaison, n'est répercutée dans le système global des serveurs DNS qu'au terme de cette période de temps. Nous vous recommandons de laisser le paramètre sur sa valeur par défaut, à savoir de 300 secondes (5 minutes). Une valeur supérieure augmente la durée pendant laquelle les réponses DNS Traffic Manager sont mises en cache par les clients et les programmes de résolution DNS, ce qui réduit la latence globale des requêtes DNS. Toutefois, si un basculement très rapide est nécessaire, vous pouvez configurer une valeur inférieure.
- **Les points de terminaison doivent se trouver dans un seul abonnement -** tous les points de terminaison doivent appartenir à l'abonnement où vous créez le profil. Vous pouvez ajouter des points de terminaison à partir de différents abonnements à un profil en tant que points de terminaison externes, mais Azure ne les supprime pas automatiquement si vous désactivez ou supprimez le service associé. Les points de terminaison externes restent donc dans le profil Traffic Manager et continueront à vous être facturés, sauf si vous les supprimez manuellement.

-**Services de production uniquement -** seuls les points de terminaison dans un environnement de production sont disponibles. Vous ne pouvez pas effectuer de redirection vers des points de terminaison en cours d'exécution dans un environnement intermédiaire. Notez que si vous procédez à une permutation d'adresses IP virtuelles pendant qu'un profil redirige le trafic, celui-ci utilise le point de terminaison récemment permuté dans l'environnement de production.

-**Nommez vos points de terminaison afin qu'ils soient facilement identifiables -** réfléchissez au préfixe DNS que vous souhaitez utiliser. contrairement au nom du service cloud ou du site web, le nom DNS a l'avantage d'être systématiquement unique dans un abonnement. Pour éviter toute confusion, donnez à un site web ou à un service cloud un nom et un préfixe DNS identiques ou similaires. Si vous avez plus de 20 services cloud et sites web, une mauvaise affectation de noms peut compliquer la recherche du point de terminaison correct. En outre, quand les points de terminaison ne sont pas nommés correctement, les profils sont difficiles à gérer.

-**Tous les points de terminaison dans un profil doivent traiter les mêmes opérations et ports -** si vous combinez vos points de terminaison, le risque qu'un client appelle un point de terminaison qui ne peut pas traiter sa demande sera accru.

-**Tous les services cloud dans un profil doivent utiliser les mêmes paramètres de surveillance -** vous ne pouvez choisir qu'un seul chemin d'accès et fichier pour surveiller tous les points de terminaison d'une définition donnée. Vous pouvez entrer " / " dans la zone de texte " chemin d'accès relatif et nom de fichier " pour que la surveillance essaie d'accéder au chemin d'accès et au nom de fichier par défaut.

-**Désactivez les points de terminaison pendant les modifications temporaires au lieu de modifier votre configuration -** dans de nombreux cas, vous pouvez souhaiter mettre un point de terminaison hors connexion. Au lieu de supprimer le point de terminaison de votre profil, désactivez-le simplement dans votre profil. Ainsi, il fait toujours partie du profil, mais celui-ci se comporte comme si ce n'était pas le cas. Cela s'avère particulièrement utile pour supprimer temporairement un point de terminaison en mode de maintenance ou redéployé. Une fois le point de terminaison à nouveau opérationnel, vous pouvez l'activer. Pour plus d'informations, consultez [Désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md).

-**Stockage -** la façon dont vous concevez l'emplacement et la distribution de votre stockage joue un rôle important pendant l'utilisation de Traffic Manager. Considérez la transaction de bout en bout, ainsi que le flux de vos données, quand vous concevez et déployez vos applications pour Traffic Manager.

-**SQL Azure -** Comme pour la conception du stockage, analysez vos contraintes en matière de données et d'état d'application quand vous étendez vos points de terminaison à plusieurs zones géographiques.

## <a name="Nested">Profils imbriqués</a>

Vous pouvez spécifier le nom d'un autre profil Traffic Manager en tant que point de terminaison, pratique nommée " profils imbriqués ". Le nom du profil Traffic Manager est son nom DNS, tel que contoso-europe.trafficmanager.net.

Cela vous permet de configurer Traffic Manager de manière à ce que les requêtes de nom DNS entrantes soient analysées sur plusieurs niveaux pour que le client à l'origine de la demande soit dirigé vers l'ensemble de points de terminaison approprié. La figure 3 montre un exemple.

![Exemple de profils Traffic Manager imbriqués](./media/traffic-manager/IC751072.png)

**Figure 3**

Vous pouvez imbriquer jusqu'à 10 niveaux, et chaque profil peut être configuré avec une autre méthode d'équilibrage de charge.

Par exemple, vous pouvez créer une configuration présentant les caractéristiques suivantes :

-Au niveau supérieur (profil Traffic Manager mappé à votre nom DNS externe), vous pouvez configurer le profil avec la méthode d'équilibrage de charge basé sur les performances.

-Au niveau intermédiaire, un ensemble de profils Traffic Manager représente différents centres de données et utilise la méthode d'équilibrage de charge de tourniquet.

-Au niveau inférieur, un ensemble de points de terminaison de service cloud dans chaque centre de données traite les demandes de trafic de l'utilisateur.

Le résultat est que les utilisateurs sont dirigés vers un centre de données régional approprié du point de vue des performances et, dans ce centre de données, vers un service cloud en fonction d'une distribution de charge égale ou pondérée. Par exemple, la pondération permet de distribuer un petit pourcentage du trafic vers un déploiement nouveau ou d'évaluation à des fins de test ou de collecte de commentaires des clients.

La figure 4 illustre la configuration.

![Exemple de profils Traffic Manager à plusieurs niveaux](. / media/traffic-manager/IC751073.png)

**Figure 4**

Dans la figure 4, le profil Traffic Manager situé au niveau supérieur est un profil parent, tandis que les profils Traffic Manager se trouvant au niveau intermédiaire sont des profils enfants.

Si Traffic Manager dirige les utilisateurs vers un profil enfant possédant un petit nombre de points de terminaison sains, ces points de terminaison risquent d'être surchargés et les performances peuvent s'en ressentir. Pour éviter cette situation, vous pouvez configurer le profil Traffic Manager parent avec un seuil de points de terminaison sains qui détermine si un point de terminaison dans les profils enfants de ce parent peut recevoir du trafic. Par exemple, pour que les profils enfants possèdent au moins trois points de terminaison sains, vous devez définir la valeur de ce seuil sur 3. Dans l'exemple de la figure 4, vous configureriez ce seuil pour le profil Traffic Manager de niveau supérieur.

Pour ajouter un profil Traffic Manager en tant que point de terminaison et configurer le nombre minimal de points de terminaison sains, vous devez utiliser REST (consultez [Créer une définition](http://go.microsoft.com/fwlink/p/?LinkId=400772)) or Windows PowerShell (consultez [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)). Vous ne pouvez pas utiliser le portail de gestion.

## Figures de Traffic Manager


Pour obtenir les figures de cette rubrique sous la forme de diapositives PowerPoint en vue d'illustrer votre propre présentation de Traffic Manager ou de les modifier selon vos besoins, consultez [Figures de Traffic Manager dans la documentation MSDN](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99).

## Voir aussi

[Tâches de Configuration de Traffic Manager](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Services cloud](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sites web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Opérations pour Traffic Manager (informations de référence sur les API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

<!--HONumber=49-->