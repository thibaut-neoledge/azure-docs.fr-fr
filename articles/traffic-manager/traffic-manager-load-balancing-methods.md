<properties 
   pageTitle="Méthodes de routage de Traffic Manager"
   description="Cet article vous aide à comprendre les différentes méthodes de routage du trafic utilisées par Traffic Manager."
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
   ms.date="07/10/2015"
   ms.author="joaoma" />

# Méthodes de routage de Traffic Manager

Trois méthodes de routage sont disponibles dans Traffic Manager. Chaque profil Traffic Manager ne peut utiliser qu’une seule méthode de routage à la fois, mais vous pouvez sélectionner une autre méthode de routage pour votre profil à tout moment.

Il est important de noter que toutes les méthodes de routage incluent la surveillance des points de terminaison. Après avoir configuré votre profil Traffic Manager pour spécifier la méthode de routage qui répond le mieux à vos besoins, configurez vos paramètres de surveillance. Quand la surveillance est correctement configurée, Traffic Manager surveille l'état de vos points de terminaison, composés de services cloud et de sites web, et n'envoie pas de trafic vers les points de terminaison qui lui semblent indisponibles. Pour plus d’informations sur la surveillance de Traffic Manager, consultez [À propos de la surveillance avec Traffic Manager](traffic-manager-monitoring.md).

Les trois méthodes de routage de Traffic Manager sont :

- **Basculement **: sélectionnez cette méthode quand vos points de terminaison se trouvent dans le même centre de données Azure (appelé région dans le portail de gestion) ou dans des centres de données Azure différents, et que vous voulez utiliser un point de terminaison principal pour tout le trafic, tout en fournissant des sauvegardes au cas où le point de terminaison principal ou les points de terminaison de sauvegarde ne seraient pas disponibles. Pour plus d’informations, consultez la section [Méthode de routage du trafic par basculement](#failover-traffic-routing-method).

- **Tourniquet** : sélectionnez cette méthode quand vous souhaitez répartir la charge sur un ensemble de points de terminaison situés dans le même centre de données ou dans différents centres de données. Pour plus d’informations, consultez la section [Méthode de routage du trafic en tourniquet (round robin)](#round-robin-traffic-routing-method).

- **Performances** : sélectionnez cette méthode quand vos points de terminaison se trouvent à des emplacements géographiques différents et que vous souhaitez que les clients à l’origine des demandes utilisent le point de terminaison « le plus proche » (latence la plus faible). Pour plus d’informations, consultez la section [Méthode de routage du trafic basé sur les performances](#performance-traffic-routing-method).

Notez qu'Azure Websites fournit déjà des méthodes de routage du trafic par basculement et tourniquet (round robin) pour les sites web d’un centre de données, indépendamment du mode de site web. Traffic Manager vous permet de spécifier un routage du trafic par basculement et tourniquet (round robin) pour des sites web de différents centres de données.

>[AZURE.NOTE]La durée de vie DNS indique aux clients DNS et aux programmes de résolution sur les serveurs DNS la durée pendant laquelle les noms résolus doivent être mis en cache. Les clients continuent à utiliser un point de terminaison pendant la résolution de son nom de domaine jusqu'à ce qu'expire l'entrée de cache DNS local associée au nom.

## Méthode de routage du trafic par basculement

Bien souvent, une organisation souhaite assurer une certaine fiabilité pour ses services. Pour ce faire, elle fournit des services de sauvegarde au cas où le service principal serait hors fonction. Dans le cadre du basculement des services, il est courant d'utiliser un ensemble de points de terminaison identiques et de diriger le trafic vers un service principal, avec une liste répertoriant une ou plusieurs sauvegardes. Si le service principal n'est pas disponible, les clients à l'origine de la demande sont redirigés vers le service suivant dans l'ordre. Si les premier et deuxième services dans la liste ne sont pas disponibles, le trafic est envoyé vers le troisième, et ainsi de suite.

Quand vous configurez la méthode de routage du trafic par basculement, l'ordre des points de terminaison sélectionnés est important. À l'aide du portail de gestion, vous pouvez configurer l'ordre de basculement dans la page Configuration associée au profil.

La figure 1 est un exemple qui illustre la méthode de routage du trafic par basculement pour un ensemble de points de terminaison.

![Équilibrage de charge par basculement dans Traffic Manager](./media/traffic-manager-load-balancing-methods/IC750592.jpg)

**Figure 1**

Les étapes numérotées suivantes correspondent aux chiffres de la figure 1.

1. Traffic Manager reçoit une demande entrante d'un client via DNS et localise le profil.
2. Le profil contient une liste ordonnée de points de terminaison. Traffic Manager identifie le point de terminaison qui apparaît en premier dans la liste. Si le point de terminaison est en ligne (d'après la surveillance en cours des points de terminaison), il spécifie le nom DNS de ce point de terminaison dans la réponse DNS au client. Si le point de terminaison est hors connexion, Traffic Manager détermine le point de terminaison en ligne suivant dans la liste. Dans cet exemple CS-A est hors connexion (non disponible), mais CS-B est en ligne (disponible).
3. Traffic Manager renvoie le nom de domaine de CS-B au serveur DNS du client qui résout le nom de domaine en une adresse IP et l'envoie au client.
4. Le client démarre le trafic vers CS-B.

## Méthode de routage du trafic en tourniquet (round robin)

L’une des méthodes courantes de routage du trafic consiste à fournir un ensemble de points de terminaison identiques et de leur envoyer le trafic en tourniquet (round robin). La méthode tourniquet répartit le trafic entre les différents points de terminaison. Elle sélectionne un point de terminaison sain au hasard et n'envoie pas le trafic aux services détectés comme étant indisponibles. Pour plus d’informations, consultez [Surveillance avec Traffic Manager](../traffic-manager-onitoring.md).

La figure 2 est un exemple qui illustre la méthode de routage du trafic en tourniquet (round robin) pour un ensemble de points de terminaison.

![Équilibrage de charge de tourniquet dans Traffic Manager](./media/traffic-manager-load-balancing-methods/IC750593.jpg)

**Figure 2**

Les étapes numérotées suivantes correspondent aux chiffres de la figure 2.

1. Traffic Manager reçoit une demande entrante d'un client et localise le profil.
2. Le profil contient une liste de points de terminaison. Traffic Manager sélectionne un point de terminaison dans cette liste de manière aléatoire, à l'exception des points de terminaison hors connexion (non disponibles) déterminés par la surveillance des point de terminaison Traffic Manager. Dans cet exemple, il s'agit du point de terminaison CS-B.
3. Traffic Manager renvoie le nom de domaine de CS-B au serveur DNS du client. Le serveur DNS du client résout ce nom de domaine en adresse IP et l'envoie au client.
4. Le client démarre le trafic vers CS-B.

De plus, le routage du trafic en tourniquet (round robin) prend en charge une répartition pondérée du trafic réseau. La figure 3 est un exemple qui illustre la méthode de routage du trafic en tourniquet (round robin) pondéré pour un ensemble de points de terminaison.

![Équilibrage de charge pondéré de tourniquet](./media/traffic-manager-load-balancing-methods/IC750594.png)

**Figure 3**

Le routage du trafic en tourniquet (round robin) pondéré vous permet de répartir la charge sur plusieurs points de terminaison selon une valeur de pondération affectée à chacun d'eux. Plus la pondération est élevée, plus un point de terminaison est fréquemment retourné. Scénarios dans lesquels cette méthode peut être utile :

- Mise à niveau progressive d’une application : allouez un pourcentage de trafic à acheminer vers un nouveau point de terminaison, puis augmentez progressivement le trafic au fil du temps jusqu’à 100 %.
- Migration d’applications vers Azure : créez un profil avec des points de terminaison Azure et externes, et spécifiez la pondération du trafic acheminé vers chaque point de terminaison.
- Extension du cloud pour une capacité supplémentaire : étendez rapidement un déploiement local dans le cloud en le plaçant derrière un profil Traffic Manager. Quand vous avez besoin d'une capacité supplémentaire dans le cloud, vous pouvez ajouter ou activer des points de terminaison supplémentaires et spécifier quelle partie du trafic est destinée à chaque point de terminaison.

Pour le moment, vous ne pouvez pas utiliser le portail de gestion pour configurer le routage du trafic pondéré. Azure fournit un accès par programmation à cette méthode à l'aide des applets de commande PowerShell Azure et des API REST Service Management associées.

Pour plus d’informations sur l’utilisation des API REST, consultez [Opérations sur Traffic Manager (informations de référence sur les API REST)](http://go.microsoft.com/fwlink/p/?LinkId=313584).

Pour plus d’informations sur l’utilisation des applets de commande Azure PowerShell, consultez [Applets de commande Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769). Pour obtenir un exemple de configuration, consultez [Azure Traffic Manager External Endpoints and Weighted Round Robin via PowerShell](http://azure.microsoft.com/blog/2014/06/26/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/) (en anglais) dans le blog Azure.

Pour tester le profil à partir d'un seul client et observer le comportement de tourniquet égal ou pondéré, vérifiez que le nom DNS est résolu dans les différentes adresses IP des points de terminaison en fonction des valeurs égales ou pondérées dans le profil. Pendant le test, vous devez désactiver la mise en cache DNS côté client ou effacer le cache DNS entre chaque tentative pour qu'une nouvelle requête de nom DNS soit envoyée.

## Méthode de routage du trafic basé sur les performances

Pour assurer le routage du trafic pour des points de terminaison situés dans différents centres de données à travers le monde, vous pouvez diriger le trafic entrant vers le point de terminaison le plus proche, déterminé par la plus faible latence par rapport au client à l'origine de la demande. En règle générale, le point de terminaison « le plus proche » est directement lié à la distance géographique la plus courte. La méthode de routage du trafic basé sur les performances permet une répartition en fonction de l’emplacement et de la latence, mais ne tient pas compte des modifications apportées en temps réel à la charge ou à la configuration réseau.

La méthode de routage du trafic basé sur les performances localise le client à l'origine de la demande et l'adresse au point de terminaison le plus proche. La « proximité » est déterminée par une table de latence Internet affichant la durée de l'aller-retour entre plusieurs adresses IP et chaque centre de données Azure. Cette table est régulièrement mise à jour et n'est pas censée refléter en temps réel les performances sur Internet. Elle ne prend pas en compte la charge sur un service donné, bien que Traffic Manager surveille vos points de terminaison selon la méthode que vous choisissez et ne les inclue pas dans les réponses DNS s'ils sont indisponibles. En d'autres termes, le routage du trafic basé sur les performances intègre aussi la méthode de routage du trafic par basculement.

La figure 4 est un exemple qui illustre la méthode de routage du trafic basé sur les performances pour un ensemble de points de terminaison.

![Équilibrage de charge basé sur les performances dans Traffic Manager](./media/traffic-manager-load-balancing-methods/IC753237.jpg)

**Figure 4**

Les étapes numérotées suivantes correspondent aux chiffres de la figure 4.

1. Traffic Manager génère régulièrement la table de latence Internet. L'infrastructure de Traffic Manager effectue des tests pour déterminer la durée de l'aller-retour entre différents points dans le monde et les centres de données Azure qui hébergent les points de terminaison.
2. Traffic Manager reçoit une demande entrante d'un client via son serveur DNS local et localise le profil.
3. Traffic Manager recherche dans la table de latence Internet la ligne correspondant à l'adresse IP de la demande DNS entrante. Étant donné que le serveur DNS local de l'utilisateur effectue une requête DNS itérative pour trouver le serveur DNS faisant autorité pour le nom du profil Traffic Manager, la requête DNS est envoyée depuis l'adresse IP du serveur DNS local du client.
4. Traffic Manager localise le centre de données présentant la durée la plus courte parmi les centres de données qui hébergent les points de terminaison définis dans le profil. Dans cet exemple, il s'agit de CS-B.
5. Traffic Manager renvoie le nom de domaine de CS-B au serveur DNS local du client qui résout le nom de domaine en une adresse IP et l'envoie au client.
6. Le client démarre le trafic vers CS-B.

**Points à noter :**

- Si votre profil contient plusieurs points de terminaison dans le même centre de données, le trafic dirigé vers ce centre est distribué uniformément entre les points de terminaison considérés comme disponibles et sains par la surveillance des points de terminaison.
- Si tous les points de terminaison dans un centre de données donné ne sont pas disponibles (d’après la surveillance des points de terminaison), le trafic pour ces points de terminaison est distribué entre tous les autres points de terminaison disponibles spécifiés dans le profil, pas en direction du ou des points de terminaison les plus proches suivants. Cela a pour but d'éviter un échec en cascade qui pourrait se produire en cas de surcharge du point de terminaison le plus proche.
- Quand la table de latence Internet est mise à jour, vous pouvez remarquer une différence dans les modèles de trafic et la charge sur vos points de terminaison. Ces modifications sont généralement minimes.
- Si vous utilisez la méthode de routage du trafic basé sur les performances avec des points de terminaison externes, vous devez spécifier l’emplacement de ces points de terminaison. Choisissez la région Azure la plus proche de votre déploiement. Pour plus d’informations, consultez [Gérer des points de terminaison dans Traffic Manager](traffic-manager-endpoints.md).

## Figures Traffic Manager

Si vous souhaitez récupérer les figures de cette rubrique sous forme de diapositives PowerPoint pour votre propre présentation de Traffic Manager ou pour les modifier, consultez la page [Traffic Manager figures in MSDN documentation](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99) (en anglais).

## Étapes suivantes

[Vue d’ensemble de Traffic Manager](traffic-manager-overview.md)

[À propos de la surveillance avec Traffic Manager](traffic-manager-monitoring.md)

[Opérations sur Traffic Manager (Référence sur l’API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

[Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Sites Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Applets de commande Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769)

 

<!---HONumber=July15_HO4-->