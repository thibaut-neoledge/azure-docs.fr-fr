---
title: Types de point de terminaison Traffic Manager | Microsoft Docs
description: "Cet article explique les différents types de points de terminaison pouvant être utilisés avec Azure Traffic Manager."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 4e506986-f78d-41d1-becf-56c8516e4d21
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 2ced9e73a65160f4f3c8ba92affc143ca554d07c

---

# <a name="traffic-manager-endpoints"></a>Points de terminaison Traffic Manager
Microsoft Azure Traffic Manager vous permet de contrôler la distribution du trafic réseau sur vos déploiements d’applications exécutés dans différents centres de données. Vous pouvez configurer chaque déploiement d’application en tant que « point de terminaison » dans Traffic Manager. Lorsque Traffic Manager reçoit une demande DNS, il choisit un point de terminaison disponible à renvoyer dans la réponse DNS. Traffic Manager base son choix sur l’état du point de terminaison actuel et la méthode de routage du trafic. Pour plus d’informations, consultez l’article [Fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md).

Traffic Manager prend en charge trois types de points de terminaison :

* **points de terminaison Azure** sont utilisés pour les services hébergés dans Azure.
* **points de terminaison externes** sont utilisés pour les services hébergés en dehors d’Azure, c’est-à-dire les services hébergés en local ou par un autre hébergeur.
* **points de terminaison imbriqués** servent à combiner différents profils Traffic Manager pour créer des schémas de routage du trafic plus souples afin de répondre aux besoins des déploiements plus vastes et plus complexes.

Différents types de points de terminaison peuvent être combinés dans un même profil Traffic Manager sans la moindre restriction. Chaque profil peut contenir n’importe quelle combinaison de types de points de terminaison.

Les sections suivantes décrivent de manière plus approfondie chaque type de point de terminaison.

## <a name="azure-endpoints"></a>points de terminaison Azure

Les points de terminaison Azure sont utilisés pour les services Azure dans Traffic Manager. Les types de ressource Azure suivants sont pris en charge :

* Machines virtuelles IaaS « classiques » et services cloud PaaS
* Applications Web
* Ressources PublicIPAddress (pouvant être connectées aux machines virtuelles directement ou par le biais de l’Azure Load Balancer). publicIpAddress doit se voir affecter un nom DNS pour être utilisé dans un profil Traffic Manager.

Les ressources PublicIPAddress sont des ressources Azure Resource Manager. Elles n’existent pas dans le modèle de déploiement Classic. Elles sont donc uniquement prises en charge dans les expériences Azure Resource Manager de Traffic Manager. Les autres types de points de terminaison sont pris en charge via les modèles de déploiement Resource Manager et Classic.

Lorsque vous utilisez des points de terminaison Azure, Traffic Manager détecte l’arrêt ou le démarrage d’une machine virtuelle IaaS « classique », d’un service cloud ou d’une application web. Cet état est reflété dans l’état du point de terminaison. Pour plus d’informations, consultez [Surveillance des points de terminaison Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status). Lorsque le service sous-jacent est arrêté, Traffic Manager n’effectue pas les vérifications d’intégrité des points de terminaison et ne dirige pas le trafic vers le point de terminaison. Aucun événement de facturation Traffic Manager ne se produit pour l’instance arrêtée. Lorsque le service est redémarré, la facturation reprend et le point de terminaison est éligible pour recevoir le trafic. Cette détection ne s’applique pas aux points de terminaison PublicIpAddress.

## <a name="external-endpoints"></a>points de terminaison externes

Les points de terminaison externes sont utilisés pour les services hébergés en dehors d’Azure. Par exemple, un service hébergé en local ou avec un autre fournisseur. Les points de terminaison externes peuvent être utilisés individuellement ou être combinés avec des points de terminaison Azure dans le même profil Traffic Manager. La combinaison de points de terminaison Azure avec des points de terminaison externes permet la prise en charge de divers scénarios :

* Dans un modèle de basculement actif-actif ou actif-passif, utilisez Azure pour renforcer la redondance d’une application locale existante.
* Pour réduire la latence des applications pour les utilisateurs dans le monde entier, étendez une application locale vers des emplacements géographiques supplémentaires dans Azure. Pour plus d’informations, consultez [Routage du trafic Traffic Manager basé sur les performances](traffic-manager-routing-methods.md#performance-traffic-routing-method).
* Utilisez Azure afin de fournir une capacité supplémentaire pour une application locale existante, soit en continu, soit en mode « croissance dans le cloud » pour gérer un pic de la demande.

Dans certains cas, il est utile d’utiliser des points de terminaison externes pour référencer des services Azure (pour obtenir des exemples, consultez la [FAQ](#faq)). Dans ce cas, la facturation des contrôles d’intégrité dépend de la vitesse des points de terminaison Azure, et non de la vitesse des points de terminaison externes. Contrairement aux points de terminaison Azure, si vous arrêtez ou supprimez le service sous-jacent, les contrôles d’intégrité continuent à vous être facturés jusqu’à ce que vous désactiviez ou supprimiez le point de terminaison dans Traffic Manager.

## <a name="nested-endpoints"></a>points de terminaison imbriqués

Les points de terminaison imbriqués combinent plusieurs profils Traffic Manager pour créer des schémas de routage du trafic souples et répondre aux besoins des déploiements plus vastes et plus complexes. Dans le cas des points de terminaison imbriqués, un profil « enfant » est ajouté comme point de terminaison à un profil « parent ». Les profils enfant et parent peuvent contenir d’autres points de terminaison de tout type, y compris d’autres profils imbriqués. Pour plus d’informations, consultez [Profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Utilisation d’applications web en tant que points de terminaison

D’autres aspects doivent être pris en compte lors de la configuration d’applications web en tant que points de terminaison dans Traffic Manager :

1. Seules les applications web associées à une référence (SKU) « Standard » ou supérieures peuvent être utilisées avec Traffic Manager. Les tentatives d’ajout d’une application web d’une référence SKU inférieure échouent. Si vous utilisez une application web associée à une référence SKU inférieure, Traffic Manager n’envoie plus de trafic vers cette application web.
2. Lorsqu’un point de terminaison reçoit une requête HTTP, il utilise l’en-tête « host » de la requête pour identifier l’application web qui doit traiter la requête. Cet en-tête contient le nom DNS utilisé pour lancer la requête, par exemple « contosoapp.azurewebsites.net ». Pour utiliser un autre nom DNS avec votre application web, le nom DNS doit être enregistré en tant que nom de domaine personnalisé pour l’application. Lorsque vous ajoutez un point de terminaison d’application web en tant que point de terminaison Azure, le nom DNS du profil Traffic Manager est automatiquement enregistré pour l’application. Cet enregistrement est supprimé automatiquement lors de la suppression du point de terminaison.
3. Chaque profil Traffic Manager peut contenir au maximum un point de terminaison d’application web provenant de chaque région Azure. Pour éviter cette contrainte, vous pouvez configurer une application web en tant que point de terminaison externe. Pour plus d’informations, visitez le [FAQ](#faq).

## <a name="enabling-and-disabling-endpoints"></a>Activation et désactivation des points de terminaison

La désactivation d’un point de terminaison dans Traffic Manager peut être particulièrement utile pour supprimer temporairement le trafic d’un point de terminaison en mode de maintenance ou en cours de redéploiement. Une fois le point de terminaison à nouveau fonctionnel, il peut être réactivé.

Les points de terminaison peuvent être activés et désactivés via le portail Traffic Manager, PowerShell, l’interface de ligne de commande ou l’API REST, aussi bien dans modèles de déploiement Resource Manager que Classic.

> [!NOTE]
> La désactivation d’un point de terminaison Azure n’a aucun lien avec son état de déploiement dans Azure. Un service Azure (tel qu’une machine virtuelle ou une application web) reste en fonction et peut recevoir du trafic même lorsqu’il est désactivé dans Traffic Manager. Le trafic peut être adressé directement sur l’instance de service plutôt que via le nom DNS du profil Traffic Manager. Pour plus d’informations, consultez l’article [Fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md).

L’éligibilité actuelle de chaque point de terminaison pour recevoir le trafic dépend des facteurs suivants :

* L’état du profil (activé/désactivé)
* L’état du point de terminaison (activé/désactivé)
* Les résultats du contrôle d’intégrité pour ce point de terminaison

Pour plus d’informations, consultez la rubrique relative à la [surveillance des points de terminaison avec Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Comme Traffic Manager fonctionne au niveau du DNS, il est impossible de modifier les connexions existantes à un point de terminaison. Lorsqu’un point de terminaison n’est pas disponible, Traffic Manager dirige les nouvelles connexions vers un autre point de terminaison disponible. Toutefois, l’hôte situé derrière le point de terminaison désactivé ou non sain peut continuer à recevoir le trafic via les connexions existantes, jusqu’à ce que ces sessions s’arrêtent. Les applications doivent limiter la durée de session pour autoriser le trafic à transiter à partir des connexions existantes.

Si tous les points de terminaison d’un profil sont désactivés, ou si le profil lui-même est désactivé, Traffic Manager envoie une réponse NXDOMAIN à une nouvelle requête DNS.

## <a name="faq"></a>Forum Aux Questions

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

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md).
* En savoir plus sur le [basculement automatique et la surveillance des points de terminaison](traffic-manager-monitoring.md)de Traffic Manager.
* En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md)avec Traffic Manager.




<!--HONumber=Nov16_HO3-->


