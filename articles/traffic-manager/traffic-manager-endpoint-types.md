<properties
   pageTitle="Types de point de terminaison Traffic Manager | Microsoft Azure"
   description="Cet article explique les différents types de points de terminaison pouvant être utilisés avec Azure Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2016"
   ms.author="jtuliani" />

# Points de terminaison Traffic Manager

Microsoft Azure Traffic Manager vous permet de contrôler la distribution du trafic utilisateur sur vos déploiements d’applications exécutés dans différents centres de données ou dans d’autres régions du monde entier.

Chaque déploiement d’application doit être configuré en tant que « point de terminaison » dans Traffic Manager. Lorsque Traffic Manager reçoit une demande DNS, il sélectionne parmi ces points de terminaison celui qu’il doit renvoyer dans la réponse DNS, en fonction de la disponibilité des points de terminaison et de la méthode de routage du trafic choisie. Pour plus d’informations, consultez l’article [Fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md).

Cette page explique comment Traffic Manager prend en charge les différents types de points de terminaison.

## Types de points de terminaison Traffic Manager

Traffic Manager prend en charge trois types de points de terminaison :

- Les **points de terminaison Azure** sont utilisés pour les services hébergés dans Azure.
- Les **points de terminaison externes** sont utilisés pour les services hébergés en dehors d’Azure, c’est-à-dire les services hébergés en local ou par un autre hébergeur.
- Les **points de terminaison imbriqués** servent à combiner différents profils Traffic Manager pour créer des schémas de routage du trafic plus souples afin de répondre aux besoins des déploiements plus vastes et plus complexes.

Différents types de points de terminaison peuvent être combinés dans un même profil Traffic Manager sans la moindre restriction. Chaque profil peut contenir n’importe quelle combinaison de types de points de terminaison.

Les sections suivantes décrivent de manière plus approfondie chaque type de point de terminaison.

### Points de terminaison Azure

Les points de terminaison Azure sont utilisés pour configurer des services Azure dans Traffic Manager. Les points de terminaison Azure prennent actuellement en charge les types de ressources Azure suivantes :

- Machines virtuelles IaaS « classiques » et services cloud PaaS
- Web Apps
- Ressources PublicIPAddress (pouvant être connectées aux machines virtuelles directement ou par le biais de l’Azure Load Balancer). Notez que publicIpAddress doit se voir affecter un nom DNS pour être utilisé dans Traffic Manager.

Les ressources PublicIPAddress sont des ressources Azure Resource Manager ; elles ne sont pas liées aux API Gestion des services Azure. Elles sont donc uniquement prises en charge dans les expériences Azure Resource Manager de Traffic Manager. Les autres types de points de terminaison sont pris en charge via les expériences Resource Manager et Gestion des services dans Traffic Manager.

Lorsque vous utilisez des points de terminaison Azure, Traffic Manager détecte l’arrêt ou le démarrage d’une machine virtuelle IaaS « classique » ou d’un service cloud PaaS. Ces conditions se reflètent dans l’état du point de terminaison (voir [À propos de la surveillance avec Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status) pour plus d’informations.) Lorsque le service sous-jacent est arrêté, Traffic Manager ne facture plus les contrôles d’intégrité des points de terminaison et ne dirige pas le trafic vers le point de terminaison ; la facturation ne reprend et le point de terminaison ne devient éligible à la réception du trafic que lorsque le service est redémarré. Ce principe ne s’applique pas aux points de terminaison PublicIpAddress.

### Points de terminaison externes

Les points de terminaison externes permettent de configurer Traffic Manager pour diriger le trafic vers des services extérieurs à Azure, par exemple un service hébergé en local ou un service hébergé par un autre fournisseur.

Les points de terminaison externes peuvent être utilisés indépendamment ou être combinés avec des points de terminaison Azure dans le même profil Traffic Manager. La combinaison de points de terminaison Azure avec des points de terminaison externes permet la prise en charge de divers scénarios :

- Utilisation d’Azure pour renforcer la redondance d’une application locale existante, dans un modèle de basculement actif-actif ou actif-passif.
- Utilisation d’Azure pour étendre une application locale existante à d’autres emplacements géographiques, parallèlement à un [routage du trafic Traffic Manager basé sur les performances](traffic-manager-routing-methods.md#performance-traffic-routing-method), afin de réduire la latence de l’application et d’améliorer les performances pour les utilisateurs finaux.
- Utilisation d’Azure afin de fournir une capacité supplémentaire pour une application locale existante, soit en continu, soit en mode « croissance dans le cloud » pour gérer un pic de la demande.

Dans certains cas, il peut être utile d’utiliser des points de terminaison externes pour référencer des services Azure (pour obtenir des exemples, consultez la [FAQ](#faq)). Dans ce cas, la facturation des contrôles d’intégrité dépend de la vitesse des points de terminaison Azure, et non de la vitesse des points de terminaison externes. Contrairement aux points de terminaison Azure, si vous arrêtez ou supprimez le service sous-jacent, les contrôles d’intégrité continueront à vous être facturés jusqu’à ce que vous désactiviez ou supprimiez de façon explicite le point de terminaison dans Traffic Manager.

### Points de terminaison imbriqués

Les points de terminaison imbriqués servent à combiner différents profils Traffic Manager pour créer des schémas de routage du trafic plus souples afin de répondre aux besoins des déploiements plus vastes et plus complexes.

Dans le cas des points de terminaison imbriqués, un profil « enfant » est ajouté comme point de terminaison à un profil « parent », de manière à créer une hiérarchie. Les profils enfant et parent peuvent contenir d’autres points de terminaison de tout type, y compris d’autres profils imbriqués.

Pour plus d’informations, consultez [Profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md).

## Utilisation d’applications web en tant que points de terminaison

D’autres aspects doivent être pris en compte lors de la configuration d’applications web en tant que points de terminaison dans Traffic Manager :

1. Seules les applications web associées à une référence (SKU) « Standard » ou supérieures peuvent être utilisées avec Traffic Manager. Les appels transmis à Traffic Manager pour l’ajout d’une application web d’une SKU inférieure échoueront. Si vous utilisez une application web associée à une référence SKU inférieure, Traffic Manager n’enverra plus de trafic vers cette application web et le point de terminaison risque d’être supprimé de Traffic Manager.

2. Lorsque le service Web Apps reçoit une requête HTTP, il utilise l’en-tête « host » de la requête pour identifier l’application web qui doit traiter la requête. Cet en-tête contient le nom DNS utilisé pour lancer la requête, par exemple « contosoapp.azurewebsites.net ». Pour utiliser un autre nom DNS avec votre application web, le nom DNS doit être enregistré en tant que domaine personnalisé pour l’application. Lorsque vous ajoutez un point de terminaison d’application web à un profil Traffic Manager en tant que point de terminaison Azure, le nom DNS du profil Traffic Manager est automatiquement enregistré en tant que domaine personnalisé pour l’application. Cet enregistrement est supprimé automatiquement lors de la suppression du point de terminaison.

3. En règle générale, une application web sera configurée en tant que point de terminaison Azure. Dans certaines circonstances, il est cependant utile de configurer une application web en tant que point de terminaison externe (pour obtenir un exemple, voir le paragraphe suivant). Les applications web ne peuvent être configurées en tant que points de terminaison externes dans Traffic Manager que dans le cadre des expériences Traffic Manager de Resource Manager. Ceci n’est pas pris en charge via les expériences Gestion des services.

4. Chaque profil Traffic Manager peut contenir au maximum un point de terminaison d’application web provenant de chaque région Azure. Un moyen de contourner cette contrainte est donné dans la [FAQ](#faq).

## Activation et désactivation des points de terminaison

La désactivation d’un point de terminaison dans Traffic Manager est particulièrement utile pour supprimer temporairement le trafic d’un point de terminaison en mode de maintenance ou en cours de redéploiement. Une fois le point de terminaison à nouveau en fonction, il peut être réactivé.

Les points de terminaison peuvent être activés et désactivés via le portail Traffic Manager, PowerShell, l’interface de ligne de commande ou l’API REST, aussi bien dans les expériences Resource Manager que dans les expériences Gestion des services.

>[AZURE.NOTE] La désactivation d’un point de terminaison Azure n’a aucun lien avec son état de déploiement dans Azure. Un service Azure (par exemple, une machine virtuelle ou une application web) reste actif et capable de recevoir le trafic même lorsqu’il est désactivé dans Traffic Manager, si le trafic est envoyé directement à ce service plutôt que via le nom DNS du profil Traffic Manager. Pour plus d’informations, consultez l’article [Fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md).

Lorsqu’un point de terminaison est désactivé, il n’est plus renvoyé dans les réponses DNS et, par conséquent, aucun trafic n’est dirigé vers le point de terminaison. En outre, les contrôles d’intégrité du point de terminaison sont suspendus et ne sont plus facturés. La désactivation d’un point de terminaison équivaut à la suppression d’un point de terminaison, à la différence près qu’il est plus facile de le réactiver.

La capacité actuelle de chaque point de terminaison à recevoir du trafic dépend de l’état du profil (activé/désactivé), de l’état du point de terminaison (activé/désactivé) et des résultats des contrôles d’intégrité effectués sur ce point de terminaison. Pour plus d’informations, consultez la rubrique relative à la [surveillance des points de terminaison avec Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status).

>[AZURE.NOTE] Comme Traffic Manager fonctionne au niveau du DNS, il est impossible de modifier les connexions existantes à un point de terminaison. Lorsque vous dirigez le trafic entre les points de terminaison (en modifiant les paramètres de profil, ou lors d’un basculement ou d’une restauration automatique), Traffic Manager dirige les nouvelles connexions vers les points de terminaison disponibles. Toutefois, les autres points de terminaison peuvent continuer à recevoir le trafic via les connexions existantes, jusqu’à ce que ces sessions s’arrêtent. Pour libérer le trafic à partir des connexions existantes, les applications doivent limiter la durée de session utilisée avec chaque point de terminaison.

Si tous les points de terminaison d’un profil sont désactivés, ou si le profil lui-même est désactivé, les requêtes DNS reçoivent une réponse « NXDOMAIN ». Cela revient à un scénario dans lequel le profil a été supprimé.

## Forum Aux Questions

### Puis-je utiliser Traffic Manager avec des points de terminaison de plusieurs abonnements ?
Cela n’est pas possible pour Azure Web Apps. Cela est dû au fait que les applications web nécessitent que tout nom de domaine personnalisé utilisé avec les applications Web ne soit utilisé que dans un seul abonnement. Il n’est pas possible d’utiliser des applications web à partir de plusieurs abonnements portant le même nom de domaine et, par conséquent, elles ne peuvent pas être utilisées avec Traffic Manager.

Pour les autres types de point de terminaison, il est possible d’utiliser Traffic Manager avec des points de terminaison provenant de plusieurs abonnements. La procédure à suivre varie selon que vous utilisez les API Gestion des services ou les API Resource Manager de Traffic Manager. Le [portail Azure](https://portal.azure.com) utilise Resource Manager et le [portail Classic](https://manage.windowsazure.com) utilise Gestion des services.

Dans Resource Manager, vous pouvez ajouter à Traffic Manager des points de terminaison de n’importe quel abonnement tant que la personne qui configure le profil Traffic Manager dispose d’un accès en lecture au point de terminaison. Ces autorisations peuvent être accordées à l’aide du [contrôle d’accès en fonction du rôle Azure Resource Manager (RBAC)](../active-directory/role-based-access-control-configure.md).

Dans la Gestion des services, Traffic Manager exige que n’importe quel(-le) service cloud ou application web configuré(e) en tant que point de terminaison Azure réside dans le même abonnement que le profil Traffic Manager. Les points de terminaison de service cloud dans d’autres abonnements peuvent être ajoutés à Traffic Manager en tant que points de terminaison « externes », auquel cas ils seront toujours facturés au tarif d’un point de terminaison « interne ».

### Puis-je utiliser Traffic Manager avec les emplacements intermédiaires de services cloud ?
Oui. Les emplacements intermédiaires de services cloud peuvent être configurés dans Traffic Manager en tant que points de terminaison externes.

Étant donné que le service référencé par le point de terminaison réside dans Azure, les contrôles d’intégrité seront facturés au tarif utilisé pour les points de terminaison Azure.

Comme le type de point de terminaison externe est utilisé, les modifications apportées au service sous-jacent ne sont pas sélectionnées automatiquement. Par conséquent, si le service cloud est arrêté ou supprimé, le point de terminaison Traffic Manager continuera d’être facturé pour les contrôles d’intégrité tant que vous n’aurez pas désactivé ou supprimé le point de terminaison dans Traffic Manager.

### Traffic Manager prend-il en charge les points de terminaison IPv6 ?

Oui. Bien que Traffic Manager ne fournisse pas actuellement de serveurs de noms adressables en IPv6, il prend en charge les clients IPv6 connectés à des points de terminaison IPv6.

Un client utilisant uniquement IPv6 peut toujours utiliser Traffic Manager puisque le client utilise un service DNS récursif au lieu de transmettre directement les requêtes DNS à Traffic Manager. Il peut envoyer des demandes à ce service via IPv6, après quoi le service récursif doit être en mesure de contacter les serveurs de noms Traffic Manager à l’aide du protocole IPv4.

À réception d’une requête DNS, Traffic Manager renvoie le nom DNS du point de terminaison auquel le client doit se connecter. Un point de terminaison IPv6 peut être pris en charge en configurant simplement un enregistrement AAAA DNS pointant le nom DNS du point de terminaison vers l’adresse IPv6.

Notez que pour garantir le bon déroulement des contrôles d’intégrité de Traffic Manager, le service doit également exposer un point de terminaison IPv4. Cette opération doit être mappée à partir du même nom DNS de point de terminaison, à l’aide d’un enregistrement DNS A.

### Puis-je utiliser Traffic Manager avec plusieurs applications web dans la même région ?

En règle générale, Traffic Manager est utilisé pour diriger le trafic vers des applications déployées dans des régions différentes. Vous pouvez toutefois l’utiliser dans les applications comportant plusieurs déploiements dans la même région.

Dans le cas des applications web, les points de terminaison Azure Traffic Manager ne permettent pas d’ajouter au même profil Traffic Manager plusieurs points de terminaison d’application web provenant de la même région Azure. Les étapes suivantes proposent une solution de contournement :

1.	Vérifiez que vos applications web de la même région se trouvent dans d’autres « unités d’échelle »de l’application web (par exemple, différentes instances du service d’application web). Pour ce faire, vérifiez l’entrée DNS <...>.azurewebsites.net dans le chemin d’accès DNS. L’unité d’échelle doit ressembler à « waws-prod-xyz-123.vip.azurewebsites.net ». Un nom de domaine spécifié doit correspondre à un seul site d’une unité d’échelle donnée, c’est pourquoi deux applications web se trouvant dans la même unité d’échelle ne peuvent pas partager un même profil Traffic Manager.
2.	En supposant que chaque application web se trouve dans une unité d’échelle différente, ajoutez votre nom de domaine personnel en tant que nom d’hôte personnalisé pour chaque application web. Cela suppose que toutes les applications web appartiennent au même abonnement.
3.	Ajoutez un (et un seul) point de terminaison d’application web à votre profil Traffic Manager selon la procédure habituelle et configurez-le en tant que point de terminaison Azure.
4.	Ajoutez chaque point de terminaison d’application web supplémentaire à votre profil Traffic Manager en tant que point de terminaison externe. Pour cela, vous devez utiliser l’expérience Resource Manager de Traffic Manager, et non l’expérience Gestion des services.
5.	Créez un enregistrement DNS CNAME à partir de votre domaine personnel (selon l’étape 2 ci-dessus) pour le nom DNS de votre profil Traffic Manager (<...>.trafficmanager.net).
6.	Accédez à votre site via le nom de domaine personnel, et non en utilisant le nom DNS du profil Traffic Manager.

## Étapes suivantes

- En savoir plus sur le [fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md).

- En savoir plus sur le [basculement automatique et la surveillance des points de terminaison](traffic-manager-monitoring.md) de Traffic Manager.

- En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md) avec Traffic Manager.

<!---HONumber=AcomDC_0817_2016-->