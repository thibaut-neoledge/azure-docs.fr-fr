---
title: Surveillance et basculement des points de terminaison Traffic Manager | Microsoft Docs
description: "Cet article explique comment Traffic Manager utilise la surveillance des points de terminaison et le basculement automatique des points de terminaison pour aider les clients Azure à déployer des applications haute disponibilité"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: fff25ac3-d13a-4af9-8916-7c72e3d64bc7
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 4df9f744c7dde9224157eca1f869c0c420036d76

---

# <a name="traffic-manager-endpoint-monitoring-and-failover"></a>Surveillance et basculement des points de terminaison Traffic Manager

Azure Traffic Manager inclut la surveillance intégrée des points de terminaison et leur basculement automatique. Cette fonctionnalité vous aide à fournir des applications haute disponibilité résistantes aux défaillances des points de terminaison, notamment les défaillances des régions Azure.

## <a name="configure-endpoint-monitoring"></a>Configuration de la surveillance des points de terminaison

Pour configurer la surveillance des points de terminaison, vous devez spécifier les paramètres suivants sur votre profil Traffic Manager :

* **Protocole**. Choisissez HTTP ou HTTPS. Notez que la surveillance HTTPS ne vérifie pas si votre certificat SSL est valide, mais uniquement qu’il est présent.
* **Port**. Choisissez le port utilisé pour la requête.
* **Chemin d’accès**. Indiquez le chemin d’accès relatif et le nom du fichier ou de la page web auxquels la surveillance accède. Une barre oblique (/) est une entrée valide pour le chemin d’accès relatif. Cette valeur indique que le fichier est dans le répertoire racine (par défaut).

Pour vérifier l’intégrité de chaque point de terminaison, Traffic Manager soumet une requête GET au point de terminaison à l’aide du protocole, du port et du chemin d’accès relatif donné.

Une pratique courante consiste à implémenter une page personnalisée dans votre application, par exemple, /health.aspx. À l’aide de ce chemin d’accès pour la surveillance, vous pouvez effectuer des vérifications spécifiques aux applications, telles que la vérification des compteurs de performance ou de la disponibilité de la base de données. Sur la base de ces contrôles personnalisés, la page renvoie un code d’état HTTP approprié.

Tous les points de terminaison dans un profil Traffic Manager partagent les mêmes paramètres de surveillance. Si vous devez utiliser différents paramètres de surveillance pour les différents points de terminaison, vous pouvez créer des [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>État des points de terminaison et des profils

Vous pouvez activer et désactiver les points de terminaison et les profils Traffic Manager. Toutefois, un changement d’état du point de terminaison peut survenir également comme conséquence des paramètres et des processus automatisés de Traffic Manager.

### <a name="endpoint-status"></a>État du point de terminaison

Vous pouvez activer ou désactiver un point de terminaison spécifique. Le service sous-jacent, qui peut toujours être sain, n’est pas affecté. En modifiant l’état du point de terminaison, vous contrôlez la disponibilité du point de terminaison dans le profil Traffic Manager. Lorsque l’état d’un point de terminaison est désactivé, Traffic Manager ne vérifie pas son intégrité et le point de terminaison n’est pas inclus dans une réponse DNS.

### <a name="profile-status"></a>État du profil

En utilisant le paramètre d’état de profil, vous pouvez activer ou désactiver un profil spécifique. Alors que l’état du point de terminaison affecte un seul point de terminaison, l’état du profil affecte le profil complet, donc l’ensemble des points de terminaison. Lorsque vous désactivez un profil, l’intégrité des points de terminaison n’est pas vérifiée et aucun point de terminaison n’est inclus dans une réponse DNS. Un code de réponse [NXDOMAIN](https://tools.ietf.org/html/rfc2308) est renvoyé pour la requête DNS.

### <a name="endpoint-monitor-status"></a>État de surveillance des points de terminaison

L’état de surveillance des points de terminaison est une valeur générée par Traffic Manager qui affiche l’état du point de terminaison. Vous ne pouvez pas modifier ce paramètre manuellement. L’état de surveillance des points de terminaison est une combinaison des résultats de la surveillance du point de terminaison et de l’état du point de terminaison configuré. Les valeurs possibles de l’état de surveillance des points de terminaison sont affichées dans la table suivante :

| État du profil | État du point de terminaison | État de surveillance des points de terminaison | Remarques |
| --- | --- | --- | --- |
| Désactivé |Activé |Inactif |Le profil a été désactivé. Même si l’état du point de terminaison est Activé, l’état du profil (Désactivé) a la priorité. Dans les profils désactivés, les points de terminaison ne sont pas surveillés. Un code de réponse NXDOMAIN est renvoyé pour la requête DNS. |
| &lt;any&gt; |Désactivé |Désactivé |Le point de terminaison a été désactivé. Les points de terminaison désactivés ne sont pas surveillés. Le point de terminaison n’est pas inclus dans les réponses DNS, et par conséquent, il ne reçoit pas de trafic. |
| Activé |Activé |En ligne |Le point de terminaison est surveillé et sain. Il est inclus dans les réponses DNS et peut recevoir le trafic. |
| Activé |Activé |Détérioré |Les contrôles d’intégrité de surveillance des points de terminaison sont défaillants. Le point de terminaison n’est pas inclus dans les réponses DNS et ne reçoit pas de trafic. |
| Activé |Activé |CheckingEndpoint |Le point de terminaison est surveillé, mais les résultats de la première analyse n’ont pas encore été reçus. CheckingEndpoint est un état temporaire qui survient généralement immédiatement après l’ajout ou l’activation d’un point de terminaison dans le profil. Un point de terminaison avec cet état est inclus dans les réponses DNS et peut recevoir le trafic. |
| Activé |Activé |Arrêté |Le service cloud ou l’application web vers lequel/laquelle le point de terminaison pointe ne s’exécute pas. Vérifiez les paramètres du service cloud ou de l’application web. Un point de terminaison avec l’état Arrêté n’est pas surveillé. Il n’est pas inclus dans les réponses DNS et ne reçoit pas de trafic. |

Pour savoir comment l’état de surveillance des points de terminaison est calculé pour les points de terminaison imbriqués, consultez la section relative aux [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md).

### <a name="profile-monitor-status"></a>État de surveillance du profil

L’état de surveillance du profil est une combinaison de l’état du profil configuré et des valeurs de l’état de surveillance de l’ensemble des points de terminaison. Les valeurs possibles sont décrites dans le tableau suivant :

| État du profil (conformément à la configuration) | État de surveillance des points de terminaison | État de surveillance du profil | Remarques |
| --- | --- | --- | --- |
| Désactivé |&lt;any&gt;  ou profil sans points de terminaison définis. |Désactivé |Le profil a été désactivé. |
| Activé |Un ou plusieurs points de terminaison ont l’état Détérioré. |Détérioré |Passez en revue les valeurs d’état de chaque point de terminaison pour déterminer quels points de terminaison doivent être examinés. |
| Activé |Un ou plusieurs points de terminaison ont l’état En ligne. Aucun point de terminaison n’a l’état Détérioré. |En ligne |Le service accepte le trafic. Aucune action supplémentaire n’est requise. |
| Activé |Un ou plusieurs points de terminaison ont l’état CheckingEndpoint. Aucun point de terminaison n’a l’état En ligne ou Détérioré. |CheckingEndpoints |Cet état de transition se produit lorsqu’un profil est créé ou activé. L’intégrité du point de terminaison est vérifiée pour la première fois. |
| Activé |Tous les points de terminaison dans le profil ont l’état Désactivé ou Arrêté, ou le profil n’inclut aucun point de terminaison défini. |Inactif |Aucun point de terminaison n’est actif, mais le profil est toujours Activé. |

## <a name="endpoint-failover-and-recovery"></a>Basculement et récupération du point de terminaison

Traffic Manager vérifie périodiquement l’intégrité de chaque point de terminaison, y compris des points de terminaison défectueux. Traffic Manager détecte lorsque l’intégrité d’un point de terminaison est rétablie et ramène celui-ci en rotation.

> [!NOTE]
> Traffic Manager considère qu’un point de terminaison a l’état En ligne uniquement lorsqu’il reçoit un message 200 OK. Un point de terminaison est défectueux lorsque l’un des événements suivants se produit :
>
> * Réception d’une réponse autre que 200 (y compris un code 2xx différent ou une redirection 301/302)
> * Demande d’authentification du client
> * Délai d’expiration (le seuil de délai d’expiration est de 10 secondes)
> * Impossible de se connecter
>
> Pour plus d’informations sur le dépannage des vérifications en échec, consultez [Résolution des problèmes liés à l’état Détérioré d’Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).

Le diagramme ci-dessous illustre en détail le processus de surveillance.

![Séquence de basculement et de restauration automatique des points de terminaison Traffic Manager](./media/traffic-manager-monitoring/timeline.png)

1. **GET**. Pour chaque point de terminaison, le système de surveillance de Traffic Manager exécute une requête GET sur le chemin d’accès et le fichier spécifiés dans les paramètres de surveillance.
2. **200 OK**. Le système de surveillance attend un message HTTP 200 OK dans un délai de 10 secondes. Lorsqu’il reçoit cette réponse, il reconnaît que le service est disponible.
3. **30 secondes entre les contrôles**. Le contrôle d’intégrité du point de terminaison est répété toutes les 30 secondes.
4. **Service indisponible**. Le service devient indisponible. Traffic Manager n’est pas averti avant le prochain contrôle d’intégrité.
5. **Tente d’accéder au fichier de surveillance (4 essais)**. Le système de surveillance exécute une requête GET, mais ne reçoit pas de réponse au cours du délai d’expiration de 10 secondes (sinon, une réponse autre que 200 peut être reçue). Il effectue ensuite trois essais supplémentaires par intervalles de 30 secondes. Lorsque l’un des essais aboutit, le nombre d’essais est réinitialisé.
6. **État défini comme Détérioré**. Après un quatrième échec consécutif, le système de surveillance marque l’état du point de terminaison non disponible comme Détérioré.
7. **Le trafic est détourné vers les autres points de terminaison**. Les serveurs de noms DNS Traffic Manager sont mis à jour et Traffic Manager ne renvoie plus le point de terminaison en réponse aux requêtes DNS. Les nouvelles connexions sont dirigées vers les autres points de terminaison disponibles. Toutefois, les réponses DNS précédentes incluant ce point de terminaison peuvent toujours être mises en cache par des serveurs DNS et des clients DNS récursifs. Les clients continuent à utiliser le point de terminaison jusqu’à l’expiration du cache DNS. Lorsque les caches DNS expirent, les clients exécutent de nouvelles requêtes DNS et sont dirigés vers des points de terminaison différents. La durée du cache est contrôlée par le paramètre TTL dans le profil Traffic Manager (par exemple, 30 secondes).
8. **Les contrôles d’intégrité continuent**. Traffic Manager continue à contrôler l’intégrité du point de terminaison pendant qu’il se trouve dans l’état Détérioré. Traffic Manager détecte les points de terminaison dont l’intégrité a été restaurée.
9. **Le service est remis en ligne**. Le service devient disponible. Le point de terminaison conserve sont état Détérioré dans Traffic Manager jusqu’à ce que le système de surveillance effectue le contrôle d’intégrité suivant.
10. **Le trafic vers le service est rétabli**. Traffic Manager envoie une requête GET et reçoit une réponse d’état 200 OK. Le service a retrouvé son intégrité. Les serveurs de noms Traffic Manager sont mis à jour et commencent à distribuer le nom DNS du service dans les réponses DNS. Le trafic retourne au point de terminaison tandis que les réponses DNS mises en cache et renvoyées vers d’autres points de terminaison expirent et que les connexions existantes à d’autres points de terminaison s’arrêtent.

> [!NOTE]
> Étant donné que Traffic Manager fonctionne au niveau du DNS, il ne peut pas modifier les connexions existantes à un point de terminaison. Lorsqu’il dirige le trafic entre les points de terminaison (en modifiant les paramètres de profil, ou lors d’un basculement ou d’une restauration automatique), Traffic Manager dirige les nouvelles connexions vers les points de terminaison disponibles. Toutefois, les autres points de terminaison peuvent continuer à recevoir le trafic via les connexions existantes, jusqu’à ce que ces sessions s’arrêtent. Pour libérer le trafic à partir des connexions existantes, les applications doivent limiter la durée de session utilisée avec chaque point de terminaison.

## <a name="traffic-routing-methods"></a>Méthodes de routage du trafic

Lorsqu’un point de terminaison est détérioré, il n’est plus renvoyé en réponse aux requêtes DNS. Au lieu de cela, un autre point de terminaison est choisi et renvoyé. La méthode de routage du trafic configurée dans le profil détermine la manière dont le point de terminaison de remplacement est sélectionné.

* **Priorité**. Les points de terminaison forment une liste hiérarchisée. Le premier point de terminaison disponible sur la liste est toujours renvoyé. Si l’état d’un point de terminaison est Détérioré, alors le point de terminaison disponible suivant est renvoyé.
* **Pondération**. Le point de terminaison de remplacement est choisi de façon aléatoire parmi tous les points de terminaison disponibles en fonction des pondérations qui leur ont été affectées et des pondérations des autres points de terminaison disponibles.
* **Performances**. Le point de terminaison le plus proche de l’utilisateur final est renvoyé. Si ce point de terminaison est indisponible, un point de terminaison est choisi de façon aléatoire parmi tous les autres points de terminaison disponibles. En choisissant un point de terminaison aléatoire, le système évite une défaillance en cascade qui peut se produire lorsque le point de terminaison suivant le plus proche est surchargé. Vous pouvez configurer d’autres plans de basculement pour le routage du trafic à l’aide des [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).

Pour plus d’informations, consultez la rubrique relative aux [méthodes de routage du trafic dans Traffic Manager](traffic-manager-routing-methods.md).

> [!NOTE]
> Une exception au comportement normal de routage du trafic se produit lorsque tous les points de terminaison éligibles sont détériorés. Traffic Manager effectue alors un service de type « meilleur effort » et *réagit comme si tous les points de terminaison présentant un état Détérioré étaient en fait en ligne*. Ce comportement est préférable à l’autre solution, qui serait de ne renvoyer aucun point de terminaison dans la réponse DNS. Les points de terminaison désactivés ou arrêtés ne sont pas surveillés et ne sont par conséquent pas éligibles pour le trafic.
>
> Cette situation est généralement due à une configuration incorrecte du service, par exemple :
>
> * Une ACL bloque les contrôles d’intégrité de Traffic Manager
> * Une configuration incorrecte du chemin d’accès de surveillance dans le profil Traffic Manager
>
> La conséquence de ce comportement est la suivante : lorsque les contrôles d’intégrité de Traffic Manager ne sont pas configurés correctement, du point de vue du routage du trafic il semble que Traffic Manager *fonctionne* correctement. Toutefois, dans ce cas, le basculement de point de terminaison ne peut pas se produire, ce qui affecte la disponibilité globale de l’application. Il est donc important de vérifier que le profil affiche un état En ligne et non Détérioré. L’état En ligne indique que les contrôles d’intégrité de Traffic Manager fonctionnent comme prévu.

Pour plus d’informations sur le dépannage des contrôles d’intégrité, consultez la section [Résolution des problèmes liés à l’état Détérioré d’Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).

## <a name="faq"></a>Forum Aux Questions

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Traffic Manager est-il résistant aux défaillances des régions Azure ?

Traffic Manager est un composant clé de la distribution d’applications hautement disponibles dans Azure.
Pour garantir une haute disponibilité, Traffic Manager doit avoir un niveau très élevé de disponibilité et être résistant aux défaillances régionales.

Par défaut, les composants de Traffic Manager sont résistants à une panne complète de n’importe quelle région Azure. Cette tolérance s’applique à tous les composants de Traffic Manager : les serveurs de noms DNS, l’API, la couche de stockage et le service de surveillance des points de terminaison.

Dans l’éventualité peu probable d’une panne d’une région Azure entière, Traffic Manager est conçu pour continuer à fonctionner normalement. Les applications déployées dans plusieurs régions Azure peuvent se baser sur Traffic Manager pour diriger le trafic vers une instance disponible de leur application.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>En quoi le choix de l’emplacement du groupe de ressources affecte-t-il Traffic Manager ?

Traffic Manager est un service global unique. Il n’est pas régional. Le choix de l’emplacement du groupe de ressources na aucune influence sur les profils Traffic Manager déployés dans ce groupe de ressources.

Azure Resource Manager exige que tous les groupes de ressources spécifient un emplacement, qui détermine l’emplacement par défaut des ressources déployées dans ce groupe de ressources. Lorsque vous créez un profil Traffic Manager, il est créé dans un groupe de ressources. Tous les profils Traffic Manager utilisent la valeur **global** comme emplacement, à la place de la valeur par défaut du groupe de ressources.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Comment déterminer l’état d’intégrité actuel de chaque point de terminaison ?

L’état de surveillance en cours de chaque point de terminaison, en plus du profil global s’affichent dans le portail Azure. Ces informations sont également disponibles via [l’API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx), [les applets de commande PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) et [l’interface de ligne de commande Azure multiplateforme](../xplat-cli-install.md) de Traffic Monitor.

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

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md)

En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md) prises en charge par Traffic Manager

En savoir plus sur la [création d’un profil Traffic Manager](traffic-manager-manage-profiles.md)

[Résolution des problèmes liés à l’état Détérioré](traffic-manager-troubleshooting-degraded.md) sur un point de terminaison Traffic Manager



<!--HONumber=Nov16_HO3-->


