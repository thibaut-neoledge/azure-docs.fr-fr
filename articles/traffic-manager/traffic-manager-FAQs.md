---
title: "Azure Traffic Manager - FAQ | Microsoft Docs"
description: "Cet article fournit des réponses aux questions fréquemment posées sur Traffic Manager."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 8c4c8db3cf57537dd77d33b3ded2dc24167f511f
ms.lasthandoff: 03/25/2017

---

# <a name="traffic-manager-frequently-asked-questions-faq"></a>Forum Aux Questions (FAQ) relatif à Traffic Manager

## <a name="traffic-manager-basics"></a>Concepts de base de Traffic Manager

### <a name="what-ip-address-does-traffic-manager-use"></a>Quelle est l’adresse IP utilisée par Traffic Manager ?

Comme expliqué dans la section [Fonctionnement de Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager fonctionne au niveau du DNS. Il envoie des réponses DNS pour diriger les clients vers le point de terminaison de service approprié. Les clients se connectent ensuite directement au point de terminaison du service, et non via Traffic Manager.

Par conséquent, Traffic Manager ne fournit pas de point de terminaison ou d’adresse IP pour permettre aux clients de se connecter. Par conséquent, si vous souhaitez une adresse IP statique pour votre service, celle-ci doit être configurée au niveau du service, pas dans Traffic Manager.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Traffic Manager prend-il en charge les sessions « persistantes » ?

Comme expliqué dans la section [Fonctionnement de Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager fonctionne au niveau du DNS. Il utilise les réponses DNS pour diriger les clients vers le point de terminaison de service approprié. Les clients se connectent directement au point de terminaison du service, et non via Traffic Manager. Par conséquent, Traffic Manager ne voit pas le trafic HTTP entre le client et le serveur.

En outre, l’adresse IP source de la requête DNS reçue par Traffic Manager appartient au service DNS récursif, pas au client. N’ayant donc aucun moyen d’effectuer le suivi de clients individuels, Traffic Manager ne peut pas implémenter de sessions « persistantes ». Cette limitation est commune à tous les systèmes de gestion du trafic DNS et n’est pas spécifique de Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Pourquoi une erreur HTTP s’affiche-t-elle quand j’utilise Traffic Manager ?

Comme expliqué dans la section [Fonctionnement de Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager fonctionne au niveau du DNS. Il utilise les réponses DNS pour diriger les clients vers le point de terminaison de service approprié. Les clients se connectent ensuite directement au point de terminaison du service, et non via Traffic Manager. Traffic Manager ne voit pas le trafic HTTP entre le client et le serveur. C’est pourquoi, toute erreur HTTP que vous voyez doit provenir de votre application. Pour que le client se connecte à l’application, toutes les étapes de résolution DNS sont accomplies. Cela inclut toute interaction de Traffic Manager sur le flux de trafic de l’application.

Un examen approfondi de l’application doit donc être effectué.

L’en-tête d’hôte HTTP envoyé à partir du navigateur du client est la source de problèmes la plus courante. Assurez-vous que l’application est configurée pour accepter l’en-tête d’hôte correct pour le nom de domaine que vous utilisez. Pour les points de terminaison utilisant Azure App Service, voir [Configuration d’un nom de domaine personnalisé pour une application web dans Azure App Service utilisant Traffic Manager](../app-service-web/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Quel est l’impact de Traffic Manager sur les performances ?

Comme expliqué dans la section [Fonctionnement de Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager fonctionne au niveau du DNS. Étant donné que les clients se connectent directement à vos points de terminaison de service, il n’y a aucun impact sur les performances inhérent à l’utilisation de Traffic Manager une fois la connexion établie.

Étant donné que Traffic Manager s’intègre aux applications au niveau du DNS, cela nécessite l’insertion d’une recherche DNS supplémentaire dans la chaîne de résolution DNS. L’impact sur le temps de résolution DNS de Traffic Manager est minime. Traffic Manager utilise un réseau mondial de serveurs de noms, et une mise en réseau [anycast](https://en.wikipedia.org/wiki/Anycast) pour garantir que les requêtes DNS sont toujours routées vers le serveur de noms disponible le plus proche. En outre, la mise en cache des réponses DNS signifie que la latence DNS supplémentaire générée par l’utilisation de Traffic Manager s’applique uniquement à une petite partie des sessions.

La méthode de Performance route le trafic vers le point de terminaison disponible le plus proche. Le résultat est que l’impact sur les performances globales associé à cette méthode doit être minimal. Toute augmentation de la latence DNS doit être compensée par une diminution de latence du réseau vers le point de terminaison.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Quels protocoles d’application puis-je utiliser avec Traffic Manager ?

Comme expliqué dans la section [Fonctionnement de Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager fonctionne au niveau du DNS. Une fois la recherche DNS terminée, les clients se connectent au point de terminaison d’application directement, et non via Traffic Manager. Cette connexion peut par conséquent utiliser tout protocole d’application. Toutefois, les contrôles d’intégrité de point de terminaison de Traffic Manager requièrent un point de terminaison HTTP ou HTTPS. Le point de terminaison pour un contrôle d’intégrité peut être différent du point de terminaison d’application auquel les clients se connectent.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Puis-je utiliser Traffic Manager avec un nom de domaine « nu » ?

Non. Les normes DNS n’autorisent pas la coexistence d’enregistrements CNAME avec d’autres enregistrements DNS du même nom. Le sommet (ou racine) d’une zone DNS contient toujours deux enregistrements DNS existants : la SOA et les enregistrements NS faisant autorité. Cela signifie qu’un enregistrement CNAME ne peut pas être créé au sommet de la zone sans violer les normes DNS.

Traffic Manager nécessite un enregistrement CNAME DNS pour mapper le nom DNS personnel. Par exemple, vous mappez www.contoso.com au nom DNS de profil Traffic Manager contoso.trafficmanager.net. En outre, le profil Traffic Manager renvoie un deuxième CNAME DNS pour indiquer le point de terminaison auquel le client doit se connecter.

Pour contourner ce problème, nous vous recommandons d’utiliser une redirection HTTP pour diriger le trafic du nom de domaine nu vers une autre URL, qui peut ensuite utiliser Traffic Manager. Par exemple, le domaine nu « contoso.com » peut rediriger les utilisateurs vers l’enregistrement CNAME « www.contoso.com » qui pointe vers le nom DNS de Traffic Manager.

La prise en charge complète des domaines nus dans Traffic Manager est suivie dans notre backlog de fonctionnalités. Vous pouvez inscrire votre prise en charge pour cette demande de fonctionnalité en [votant pour celle-ci sur le site de commentaires de notre communauté](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Traffic Manager considère-t-il l’adresse de sous-réseau client lors du traitement des requêtes DNS ? 
Non, pour l’heure Traffic Manager considère uniquement l’adresse IP source de la requête DNS qu’il reçoit, en général l’adresse IP du programme de résolution DNS, lorsque vous effectuez des recherches pour les méthodes de routage géographique et basé sur les performances.  
Plus précisément, [RFC 7871 – Sous-réseau client dans les requêtes DNS](https://tools.ietf.org/html/rfc7871) qui fournit un [mécanisme d’extension pour DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) qui peut passer l’adresse du sous-réseau du client à partir de programmes de résolution qui le prennent en charge à des serveurs DNS n’est actuellement pas pris en charge dans Traffic Manager. Vous pouvez inscrire votre prise en charge pour cette demande de fonctionnalité en [sur le site de commentaires de notre communauté](https://feedback.azure.com/forums/217313-networking).


## <a name="traffic-manager-geographic-traffic-routing-method"></a>Méthode de routage du trafic « Geographic » (Géographique) de Traffic Manager

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Quels sont les cas d’utilisation dans lesquels le routage géographique est utile ? 
Le type de routage géographique peut être utilisé dans les scénarios dans lesquels un client Azure doit distinguer ses utilisateurs en fonction de régions géographiques. Par exemple, à l’aide de la méthode de routage de trafic géographique, vous pouvez attribuer une expérience utilisateur différente en fonction de la région. Un autre exemple est le respect des mandats de souveraineté de données locales qui nécessitent que les utilisateurs d’une région spécifique soient servis uniquement par les points de terminaison existant dans cette région.

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Quelles sont les régions prises en charge par Traffic Manager pour le routage géographique ? 
La hiérarchie de pays/régions utilisée par Traffic Manager se trouve [ici](traffic-manager-geographic-regions.md). Si cette page est maintenue à jour avec les éventuelles modifications apportées, vous pouvez également récupérer par programme les mêmes informations à l’aide de [l’API REST d’Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Comment Traffic Manager détermine-t-il l’emplacement à partir duquel un utilisateur exécute une requête ? 
Traffic Manager examine l’adresse IP source de la requête (très probablement un programme de résolution DNS local effectuant la requête pour le compte de l’utilisateur) et utilise une adresse IP interne pour le mappage des régions afin de déterminer l’emplacement. Ce mappage est mis à jour en permanence pour prendre en compte les modifications apportées à Internet. 

### <a name="is-it-guaranteed-that-traffic-manager-will-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Est-il garanti que Traffic Manager détermine correctement l’emplacement géographique exact de l’utilisateur dans tous les cas ?
Non, Traffic Manager ne peut pas garantir que la région géographique que nous déduisons à partir de l’adresse IP source d’une requête DNS correspond toujours à l’emplacement de l’utilisateur, et ce pour les raisons suivantes : 

- Tout d’abord, comme décrit dans le Forum aux questions précédent, l’adresse IP source que nous voyons est celle d’un programme de résolution DNS qui effectue une recherche pour le compte de l’utilisateur. Bien que l’emplacement géographique du programme de résolution DNS est un bon indicateur de l’emplacement géographique de l’utilisateur, il peut également être différent selon l’encombrement du service de résolution DNS et du service de résolution DNS spécifique qu'un client a choisi d’utiliser. Par exemple, un client en Malaisie peut définir dans les paramètres de son appareil l’utilisation d’un service de résolution DNS dont le serveur DNS à Singapour peut être sélectionné pour gérer les résolutions de requête pour cet utilisateur/appareil. Dans ce cas, Traffic Manager affiche uniquement l’adresse IP du programme de résolution, qui correspond à l’emplacement à Singapour. En outre, consultez le Forum aux questions précédent concernant la prise en charge des adresses de sous-réseau client sur cette page.

- Deuxièmement, Traffic Manager utilise une table interne pour mapper l’adresse IP sur la traduction de région géographique. Bien que ce mappage est validé et mis à jour en permanence pour améliorer sa précision et prendre en compte l’évolution d’Internet, il est toujours possible que nos informations ne sont pas une représentation exacte de l’emplacement géographique de toutes les adresses IP.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Pour le routage géographique, un point de terminaison doit-il être situé physiquement dans la même région que celle avec laquelle il est configuré ? 
Non, l’emplacement du point de terminaison n’impose aucune restriction sur les régions qui peuvent lui être mappées. Par exemple, tous les utilisateurs d’Inde peuvent être dirigés vers un point de terminaison situé dans la région Azure Centre des États-Unis.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Puis-je affecter des régions géographiques aux points de terminaison dans un profil qui n’est pas configuré pour procéder au routage géographique ? 
Oui, si la méthode de routage d’un profil n’est pas géographique, vous pouvez utiliser [l’API REST d’Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/) pour affecter des régions géographiques aux points de terminaison de ce profil. Cette configuration est ignorée dans le cas des profils de type de routage non géographique. Si vous modifiez ultérieurement un tel profil en type de routage géographique, Traffic Manager utilise ces mappages.


### <a name="when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic-i-am-getting-an-error"></a>Lorsque j’essaie de modifier la méthode de routage d’un profil existant en méthode de routage géographique, j’obtiens une erreur.
Au moins une région doit être mappée à tous les points de terminaison sous un profil doté d’une méthode de routage géographique. Pour convertir un profil existant en type de routage géographique, vous devez tout d’abord associer les régions géographiques à tous ses points de terminaison à l’aide de [l’API REST d’Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/). Si vous utilisez le portail, vous devez d’abord supprimer les points de terminaison, modifier la méthode de routage du profil en méthode de routage géographique, puis ajouter les points de terminaison ainsi que le mappage de leurs régions géographiques. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Pourquoi est-il vivement recommandé que les clients créent des profils imbriqués à la place de points de terminaison sous un profil avec le routage géographique activé ? 
Une région ne peut être affectée qu’à un seul point de terminaison dans un profil s’il utilise le type de routage géographique. Si ce point de terminaison n’est pas un type imbriqué auquel un profil enfant est attaché et s’il devient défectueux, Traffic Manager continue à lui envoyer le trafic puisque l’alternative de ne pas envoyer de trafic ne se révèle pas meilleure. Traffic Manager ne bascule vers aucun autre point de terminaison même si la région affectée est « parente » de la région affectée au point de terminaison qui s’est détérioré (par exemple, si un point de terminaison auquel la région Espagne est affectée devient défectueux, nous ne basculons pas vers un autre point de terminaison auquel la région Europe est affectée). De cette façon, Traffic Manager respecte les limites géographiques qu’un client a configurées dans son profil. Pour profiter du basculement vers un autre point de terminaison lorsqu’un point de terminaison est défectueux, il est recommandé que les régions géographiques soient affectées aux profils imbriqués avec plusieurs points de terminaison et non des points de terminaison individuels. De cette façon, en cas d’échec d’un point de terminaison du profil enfant imbriqué, le trafic peut basculer vers un autre point de terminaison dans le même profil enfant imbriqué.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Existe-t-il des restrictions quant à la version de l’API qui prend en charge ce type de routage ?

Oui, seules les versions de l’API 2017-03-01 et plus récentes prennent en charge le type de routage Géographique. Les anciennes versions de l’API ne peuvent pas être utilisées pour créer des profils de type de routage géographique ou affecter des régions géographiques à des points de terminaison. Si une ancienne version de l’API est utilisée pour récupérer les profils d’un abonnement Azure, les profils de type de routage géographique ne sont pas retournés. En outre, si vous utilisez des versions anciennes de l’API, les profils retournés qui contiennent des points de terminaison avec une affectation de régions géographiques n’affichent pas les régions géographiques affectées.



## <a name="traffic-manager-endpoints"></a>Points de terminaison Traffic Manager

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Puis-je utiliser Traffic Manager avec des points de terminaison de plusieurs abonnements ?

L’utilisation de points de terminaison à partir de plusieurs abonnements n’est pas possible avec Azure Web Apps. Azure Web Apps requiert que tout nom de domaine personnalisé utilisé avec Web Apps ne soit utilisé que dans un seul abonnement. Il n’est pas possible d’utiliser des applications web à partir de plusieurs abonnements portant le même nom de domaine.

Pour les autres types de point de terminaison, il est possible d’utiliser Traffic Manager avec des points de terminaison provenant de plusieurs abonnements. La procédure à suivre pour configurer Traffic Manager varie selon que vous utilisez le modèle de déploiement Classic ou Resource Manager.

* Dans Resource Manager, vous pouvez ajouter à Traffic Manager des points de terminaison de n’importe quel abonnement tant que la personne qui configure le profil Traffic Manager dispose d’un accès en lecture au point de terminaison. Ces autorisations peuvent être accordées à l’aide du [contrôle d’accès en fonction du rôle Azure Resource Manager (RBAC)](../active-directory/role-based-access-control-configure.md).
* Dans l’interface modèle de déploiement Classic, Traffic Manager exige que les services cloud ou applications web configurés en tant que points de terminaison Azure résident dans le même abonnement que le profil Traffic Manager. Les points de terminaison de service cloud dans d’autres abonnements peuvent être ajoutés à Traffic Manager en tant que points de terminaison « externes ». Ces derniers sont facturés comme points de terminaison Azure, plutôt qu’au tarif des points de terminaison externes.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Puis-je utiliser Traffic Manager avec les emplacements intermédiaires de services cloud ?

Oui. Les emplacements intermédiaires de services cloud peuvent être configurés dans Traffic Manager en tant que points de terminaison externes. Les contrôles d’intégrité sont toujours facturés au tarif des points de terminaison Azure. Comme le type de point de terminaison externe est utilisé, les modifications apportées au service sous-jacent ne sont pas sélectionnées automatiquement. Avec les points de terminaison externes, Traffic Manager ne peut pas détecter lorsque le service cloud est arrêté ou supprimé. Par conséquent, Traffic Manager continue de facturer les vérifications d’intégrité jusqu’à ce que le point de terminaison soit désactivé ou supprimé.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Traffic Manager prend-il en charge les points de terminaison IPv6 ?

Traffic Manager ne fournit pas actuellement de serveurs de noms adressables en IPv6. Toutefois, il prend en charge les clients IPv6 connectés à des points de terminaison IPv6. Un client n’effectue pas de requêtes DNS directement vers Traffic Manager. Au lieu de cela, il utilise un service DNS récursif. Un client utilisant uniquement IPv6 envoie des requêtes au service DNS récursif via IPv6. Après quoi le service récursif doit être en mesure de contacter les serveurs de noms Traffic Manager à l’aide du protocole IPv4.

Traffic Manager répond avec le nom DNS du point de terminaison. Pour prendre en charge un point de terminaison IPv6, un enregistrement AAAA DNS pointant le nom DNS du point de terminaison vers l’adresse IPv6 doit exister. Les contrôles d’intégrité Traffic Manager n’acceptent que les adresses IPv4. Le service doit exposer un point de terminaison IPv4 sur le même nom DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Puis-je utiliser Traffic Manager avec plusieurs applications web dans la même région ?

En règle générale, Traffic Manager est utilisé pour diriger le trafic vers des applications déployées dans des régions différentes. Vous pouvez toutefois l’utiliser dans les applications comportant plusieurs déploiements dans la même région. Les points de terminaison Azure Traffic Manager ne permettent pas d’ajouter au même profil Traffic Manager plusieurs points de terminaison d’application web provenant de la même région Azure.

Les étapes suivantes proposent une solution de contournement :

1. Vérifiez que vos points de terminaison sont dans une autre unité d’échelle d’application web. Un nom de domaine doit correspondre à un seul site dans une unité d’échelle donnée. Par conséquent, deux applications web dans la même unité d’échelle ne peuvent pas partager un profil Traffic Manager.
2. Ajoutez votre nom de domaine personnel en tant que nom d’hôte personnalisé à chaque application web. Chaque application web doit être dans une unité d’échelle différente. Toutes les applications web doivent appartenir au même abonnement.
3. Ajoutez un (et un seul) point de terminaison d’application web à votre profil Traffic Manager et configurez-le en tant que point de terminaison Azure.
4. Ajoutez chaque point de terminaison d’application web supplémentaire à votre profil Traffic Manager en tant que point de terminaison externe. Les points de terminaison externes peuvent être ajoutés uniquement avec le modèle de déploiement Resource Manager.
5. Créez un enregistrement DNS CNAME dans votre domaine personnel qui pointe vers le nom DNS de votre profil Traffic Manager (<...>.trafficmanager.net).
6. Accédez à votre site via le nom de domaine personnel, et non en utilisant le nom DNS du profil Traffic Manager.

##  <a name="traffic-manager-endpoint-monitoring"></a>Surveillance des points de terminaison Traffic Manager

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Traffic Manager est-il résistant aux défaillances des régions Azure ?

Traffic Manager est un composant clé de la distribution d’applications hautement disponibles dans Azure.
Pour garantir une haute disponibilité, Traffic Manager doit avoir un niveau très élevé de disponibilité et être résistant aux défaillances régionales.

Par défaut, les composants de Traffic Manager sont résistants à une panne complète de n’importe quelle région Azure. Cette tolérance s’applique à tous les composants de Traffic Manager : les serveurs de noms DNS, l’API, la couche de stockage et le service de surveillance des points de terminaison.

Dans l’éventualité peu probable d’une panne d’une région Azure entière, Traffic Manager est conçu pour continuer à fonctionner normalement. Les applications déployées dans plusieurs régions Azure peuvent se baser sur Traffic Manager pour diriger le trafic vers une instance disponible de leur application.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>En quoi le choix de l’emplacement du groupe de ressources affecte-t-il Traffic Manager ?

Traffic Manager est un service global unique. Il n’est pas régional. Le choix de l’emplacement du groupe de ressources na aucune influence sur les profils Traffic Manager déployés dans ce groupe de ressources.

Azure Resource Manager exige que tous les groupes de ressources spécifient un emplacement, qui détermine l’emplacement par défaut des ressources déployées dans ce groupe de ressources. Lorsque vous créez un profil Traffic Manager, il est créé dans un groupe de ressources. Tous les profils Traffic Manager utilisent la valeur **global** comme emplacement, à la place de la valeur par défaut du groupe de ressources.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Comment déterminer l’état d’intégrité actuel de chaque point de terminaison ?

L’état de surveillance en cours de chaque point de terminaison, en plus du profil global s’affichent dans le portail Azure. Ces informations sont également disponibles via [l’API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx), [les applets de commande PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) et [l’interface de ligne de commande Azure multiplateforme](../cli-install-nodejs.md) de Traffic Monitor.

Azure ne fournit pas d’historique de l’état d’intégrité du point de terminaison ni de la capacité à déclencher des alertes en cas de modification du niveau d’intégrité du point de terminaison.

### <a name="can-i-monitor-https-endpoints"></a>Puis-je surveiller les points de terminaison HTTPS ?

Oui. Traffic Manager prend en charge la détection sur HTTPS. Configurez **HTTPS** comme protocole dans la configuration de la surveillance.

Traffic manager ne peut pas fournir de validation de certificat :

* Les certificats côté serveur ne sont pas validés.
* Les certificats SNI côté serveur ne sont pas pris en charge.
* Les certificats clients ne sont pas pris en charge.

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Quel en-tête hôte est utilisé pour les contrôles d’intégrité des points de terminaison ?

Traffic Manager utilise des en-têtes d’hôte pour les contrôles d’intégrité HTTP et HTTPS. L’en-tête d’hôte utilisé par Traffic Manager est le nom du point de terminaison cible configuré dans le profil. La valeur utilisée dans l’en-tête hôte ne peut pas être spécifiée séparément de la propriété cible.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Quelles sont les adresses IP à l’origine des contrôles d’intégrité ?

La liste suivante contient les adresses IP à partir desquelles peuvent provenir les contrôles de Traffic Manager. Vous pouvez utiliser cette liste pour vérifier que les connexions entrantes à partir de ces adresses IP sont autorisées aux points de terminaison pour vérifier leur état d’intégrité.

* 40.68.30.66
* 40.68.31.178
* 137.135.80.149
* 137.135.82.249
* 23.96.236.252
* 65.52.217.19
* 40.87.147.10
* 40.87.151.34
* 13.75.124.254
* 13.75.127.63
* 52.172.155.168
* 52.172.158.37
* 104.215.91.84
* 13.75.153.124
* 13.84.222.37
* 23.101.191.199
* 23.96.213.12
* 137.135.46.163
* 137.135.47.215
* 191.232.208.52
* 191.232.214.62
* 13.75.152.253
* 104.41.187.209
* 104.41.190.203

## <a name="traffic-manager-nested-profiles"></a>Profils Traffic Manager imbriqués

### <a name="how-do-i-configure-nested-profiles"></a>Comment configurer des profils imbriqués ?

Des profils Traffic Manager imbriqués peuvent être configurés tant à l’aide d’Azure Resource Manager, qu’à l’aide des API REST Azure classiques, d’applets de commande PowerShell et de commandes d’interface de ligne de commande Azure multiplateformes. Ils sont également pris en charge via le nouveau portail Azure. Ils ne sont pas pris en charge dans le portail Classic.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Combien de couches d’imbrication Traffic Manager prend-il en charge ?

Vous pouvez imbriquer jusqu’à 10 niveaux de profil. Les « boucles » ne sont pas autorisées.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Puis-je combiner d’autres types de point de terminaison avec des profils enfants imbriqués dans le même profil Traffic Manager ?

Oui. Il n’existe aucune restriction sur la manière de combiner des points de terminaison de différents types au sein d’un profil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Comment le modèle de facturation s’applique-t-il aux profils imbriqués ?

Il n’y a aucun impact négatif sur la tarification en cas d’utilisation de profils imbriqués.

La facturation de Traffic Manager inclut deux composantes : des contrôles d’intégrité des points de terminaison et des millions de requêtes DNS.

* Contrôles d’intégrité des points de terminaison : aucun frais n’est facturé pour un profil enfant configuré en tant que point de terminaison dans un profil parent. La surveillance des points de terminaison dans le profil enfant est facturée comme à l’accoutumée.
* Requêtes DNS : chaque requête n’est comptabilisée qu’une seule fois. L’interrogation d’un profil parent qui retourne un point de terminaison à partir d’un profil enfant est facturée pour le profil parent uniquement.

Pour plus d’informations, voir la [page de tarification de Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Y a-t-il un impact sur les performances en cas de profils imbriqués ?

Non. Non, l’utilisation de profils imbriqués n’a aucune incidence sur les performances.

Les serveurs de noms Traffic Manager parcourent la hiérarchie de profils en interne lors du traitement de chaque requête DNS. Une requête DNS adressée à un profil parent peut recevoir une réponse DNS avec un point de terminaison d’un profil enfant. Un seul enregistrement CNAME est utilisé, que vous utilisiez un profil unique ou des profils imbriqués. Il est inutile de créer un enregistrement CNAME pour chaque profil dans la hiérarchie.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Comment Traffic Manager calcule-t-il l’intégrité d’un point de terminaison imbriqué dans un profil parent ?

Le profil parent n’effectue pas de contrôles d’intégrité directement sur le profil enfant. Au lieu de cela, l’intégrité des points de terminaison du profil enfant est utilisée pour calculer l’intégrité globale du profil enfant. Ces informations sont propagées vers le haut de la hiérarchie de profils imbriqués afin de déterminer l’intégrité du point de terminaison imbriqué. Le profil parent utilise cette intégrité agrégée pour déterminer si le trafic peut être dirigé vers le profil enfant.

Le tableau suivant décrit le comportement des contrôles d’intégrité de Traffic Manager pour un point de terminaison imbriqué.

| État d’analyse des profils enfants | État d’analyse des points de terminaison parents | Remarques |
| --- | --- | --- |
| Désactivé. Le profil enfant a été désactivé. |Arrêté |L’état des points de terminaison parents est Arrêté, pas Désactivé. L’état Désactivé sert uniquement à indiquer que vous avez désactivé le point de terminaison dans le profil parent. |
| Détérioré. Au moins un point de terminaison du profil enfant a l’état Détérioré. |En ligne : le nombre de points de terminaison en ligne dans le profil enfant correspond au moins à la valeur de MinChildEndpoints.<BR>CheckingEndpoint : le nombre de points de terminaison en ligne et CheckingEndpoint dans le profil enfant correspond au moins à la valeur de MinChildEndpoints.<BR>Détérioré : dans le cas contraire. |Le trafic est acheminé vers un point de terminaison à l’état CheckingEndpoint. Si la valeur de MinChildEndpoints est trop élevée, le point de terminaison est toujours détérioré. |
| En ligne. Au moins un point de terminaison de profil enfant est en état En ligne. Aucun point de terminaison n’est en état Détérioré. |Voir ci-dessus. | |
| CheckingEndpoints. Au moins un point de terminaison de profil enfant est en état « CheckingEndpoint ». Aucun point de terminaison n’est en état « En ligne » ou « Détérioré ». |Identique à ce qui précède. | |
| Inactif. Tous les points de terminaison du profil enfant sont en état Désactivé ou Arrêté, ou ce profil n’a aucun point de terminaison. |Arrêté | |

## <a name="next-steps"></a>Étapes suivantes :
- En savoir plus sur le [basculement automatique et la surveillance des points de terminaison](../traffic-manager/traffic-manager-monitoring.md)de Traffic Manager.
- En savoir plus sur les [méthodes de routage du trafic](../traffic-manager/traffic-manager-routing-methods.md)de Traffic Manager.
