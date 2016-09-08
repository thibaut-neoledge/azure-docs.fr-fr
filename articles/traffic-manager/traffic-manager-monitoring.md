<properties
   pageTitle="Surveillance et basculement des points de terminaison Traffic Manager | Microsoft Azure"
   description="Cet article explique comment Traffic Manager utilise la surveillance des points de terminaison et le basculement automatique des points de terminaison pour aider les clients Azure à déployer des applications haute disponibilité"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/01/2016"
   ms.author="sewhee" />

# Surveillance et basculement des points de terminaison Traffic Manager

Azure Traffic Manager inclut la surveillance intégrée des points de terminaison et leur basculement automatique. Cette fonctionnalité vous aide à fournir des applications haute disponibilité résistantes aux défaillances des points de terminaison, notamment les défaillances des régions Azure.

Traffic Manager soumet des requêtes régulières à chaque point de terminaison, puis vérifie la réponse. Si un point de terminaison ne peut pas fournir une réponse valide, Traffic Manager indique son état comme Détérioré. Le point de terminaison n’est plus inclus dans les réponses DNS, qui renverront à la place un point de terminaison alternatif disponible. Ainsi, le trafic utilisateur est renvoyé loin des points de terminaison défaillants et vers ceux qui sont disponibles.

Les contrôles d’intégrité des points de terminaison détériorés continuent. Ainsi, ils sont automatiquement remis en service une fois leur intégrité rétablie.

## Configuration de la surveillance des points de terminaison

Pour configurer la surveillance des points de terminaison, vous devez spécifier les paramètres suivants sur votre profil Traffic Manager :

- **Protocole**. Choisissez HTTP ou HTTPS. Notez que la surveillance HTTPS ne vérifie pas si votre certificat SSL est valide, mais uniquement qu’il est présent.
- **Port**. Choisissez le port utilisé pour la requête. Les ports HTTP standard et HTTPS font partie des options.
- **Chemin d’accès**. Indiquez le chemin d’accès relatif et le nom du fichier ou de la page web auxquels le contrôle d’intégrité de la surveillance tentera d’accéder. Notez qu’une barre oblique (/) est une entrée valide pour le chemin d’accès relatif et qu’elle implique que le fichier se trouve dans le répertoire racine (par défaut).

Pour vérifier l’intégrité de chaque point de terminaison, Traffic Manager soumet une requête GET au point de terminaison à l’aide du protocole, du port et du chemin d’accès relatif donné.

Une pratique courante consiste à implémenter une page personnalisée dans votre application, par exemple, /health.aspx. Configurez cette page en tant que chemin d’accès de surveillance du point de terminaison Traffic Manager. Dans cette page, vous pouvez effectuer tous les contrôles nécessaires spécifiques à l’application, comme le contrôle de la disponibilité d’une base de données, avant de renvoyer HTTP 200 (si le service est sain) ou un code d’état différent dans le cas contraire.

Les paramètres de surveillance des points de terminaison sont configurés au niveau du profil Traffic Manager, et non au niveau de chaque point de terminaison. Par conséquent, les mêmes paramètres sont utilisés pour vérifier l’intégrité de tous les points de terminaison. Si vous devez utiliser différents paramètres de surveillance pour les différents points de terminaison, vous pouvez le faire en utilisant des [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## État des points de terminaison et des profils

Vous pouvez activer et désactiver les points de terminaison et les profils Traffic Manager. Toutefois, un changement d’état du point de terminaison peut survenir également comme conséquence des paramètres et des processus automatisés de Traffic Manager. Les paramètres suivants décrivent le fonctionnement.

### État du point de terminaison

L’état du point de terminaison est un paramètre contrôlé par l’utilisateur, qui vous permet d’activer ou de désactiver facilement un point de terminaison spécifique. Cela n’affecte pas l’état du service sous-jacent (qui peut toujours être en cours d’exécution). Au lieu de cela, il contrôle la disponibilité d’un point de terminaison spécifique respectivement à Traffic Manager. Lorsque l’état d’un point de terminaison est Désactivé, Traffic Manager ne vérifie pas son intégrité et le point de terminaison n’est pas inclus dans une réponse DNS.

### État du profil

L’état du profil est un paramètre contrôlé par l’utilisateur. Vous pouvez l’utiliser pour activer ou désactiver facilement un profil. Alors que l’état du point de terminaison affecte un seul point de terminaison, l’état du profil affecte le profil complet, donc l’ensemble des points de terminaison. L’intégrité des points de terminaison dans un profil dont l’état est Désactivé n’est pas vérifiée et aucun point de terminaison n’est inclus dans une réponse DNS (une réponse NXDOMAIN est retournée).

### État de surveillance des points de terminaison

L’état de surveillance des points de terminaison est un paramètre généré par Traffic Manager qui affiche l’état actuel du point de terminaison. Vous ne pouvez pas modifier ce paramètre manuellement. L’état de surveillance des points de terminaison reflète la surveillance des points de terminaison en cours, en plus d’autres informations, telles que l’état du point de terminaison. Les valeurs possibles de l’état de surveillance des points de terminaison sont affichées dans le tableau suivant. (Pour savoir comment l’état de surveillance des points de terminaison est calculé pour les points de terminaison imbriqués, consultez la section relative aux [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md).)

|État du profil|État du point de terminaison|État de surveillance des points de terminaison (API et portail)|Remarques|
|---|---|---|---|
|Désactivé|Activé|Inactif|Le profil a été désactivé par l’utilisateur. Même si l’état du point de terminaison peut toujours être Activé, l’état du profil (Désactivé) a la priorité. Les points de terminaison dans les profils désactivés ne sont pas surveillés et aucun point de terminaison n’est inclus dans les réponses DNS (une réponse NXDOMAIN est retournée).|
|&lt;tout&gt;|Désactivé|Désactivé|Le point de terminaison a été désactivé par l’utilisateur. Un point de terminaison avec l’état Désactivé n’est pas surveillé. Il n’est pas disponible pour être inclus dans les réponses DNS, et par conséquent, il ne reçoit pas de trafic.|
|Activé|Activé|En ligne|Le point de terminaison est surveillé et sain. Il est disponible pour être inclus dans les réponses DNS, et par conséquent, il peut recevoir le trafic.|
|Activé|Activé|Détérioré|Les contrôles d’intégrité de surveillance des points de terminaison sont défaillants. Le point de terminaison n’est pas disponible pour être inclus dans les réponses DNS, et par conséquent, il ne reçoit pas de trafic.|
|Activé|Activé|CheckingEndpoint|Le point de terminaison est surveillé, mais les résultats de la première analyse n’ont pas encore été reçus. Il s’agit d’un état temporaire qui survient généralement lorsque vous venez d’ajouter un nouveau point de terminaison au profil ou que vous venez d’activer un point de terminaison ou un profil. Un point de terminaison avec cet état est disponible pour être inclus dans les réponses DNS, et par conséquent, il peut recevoir le trafic.|
|Activé|Activé|Arrêté|Le service cloud ou l’application web vers lequel/laquelle le point de terminaison pointe ne s’exécute pas. Vérifiez les paramètres du service cloud ou de l’application web. Un point de terminaison avec l’état Arrêté n’est pas surveillé. Il n’est pas disponible pour être inclus dans les réponses DNS, et par conséquent, il ne reçoit pas de trafic.|

### État de surveillance du profil

L’état de surveillance du profil est une combinaison des valeurs de l’état de surveillance de l’ensemble des points de terminaison dans le profil et de l’état du profil que vous avez configuré. Les valeurs possibles sont décrites dans le tableau suivant.

|État du profil (conformément à la configuration)|État de surveillance des points de terminaison|État de surveillance du profil (API et portail)|Remarques|
|---|---|---|---|
|Désactivé|&lt;tout&gt; ou profil sans points de terminaison définis.|Désactivé|Le profil a été désactivé par l’utilisateur.|
|Activé|Un ou plusieurs points de terminaison ont l’état Détérioré.|Détérioré|Indique qu’une action du client est requise. Passez en revue les valeurs d’état de chaque point de terminaison pour déterminer quels points de terminaison doivent être examinés.|
|Activé|Un ou plusieurs points de terminaison ont l’état En ligne. Aucun point de terminaison n’a l’état Détérioré.|En ligne|Le service accepte le trafic et aucune action du client n’est requise.|
|Activé|Un ou plusieurs points de terminaison ont l’état CheckingEndpoint. Aucun point de terminaison n’a l’état En ligne ou Détérioré.|CheckingEndpoints|État de transition. Cet état survient généralement lorsqu’un profil vient d’être créé ou activé et que l’intégrité du point de terminaison est contrôlée pour la première fois.|
|Activé|Tous les points de terminaison définis dans le profil ont l’état Désactivé ou Arrêté, ou le profil n’inclut aucun point de terminaison défini.|Inactif|Aucun point de terminaison n’est actif, mais le profil est toujours Activé.|

## Basculement et restauration automatique du point de terminaison

Imaginez un scénario dans lequel un point de terminaison précédemment sain échoue. Traffic Manager détecte l’échec et le point de terminaison est mis hors service. Ultérieurement, le point de terminaison récupère. Traffic Manager détecte cette récupération et le point de terminaison est remis en service.

>[AZURE.NOTE] Traffic Manager considère qu’un point de terminaison a l’état En ligne uniquement lorsqu’il reçoit un message 200 OK. Si l’un des éléments suivants se produit, Traffic Manager considère que le contrôle d’intégrité du point de terminaison a échoué :

>- Réception d’une réponse autre que 200 (y compris un code 2xx différent ou une redirection 301/302)
>- Demande d’authentification du client
>- Délai d’expiration (le seuil de délai d’expiration est de 10 secondes)
>- Impossible de se connecter

>Pour plus d’informations sur le dépannage des vérifications en échec, consultez [Résolution des problèmes liés à l’état Détérioré d’Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).

Le chronologie suivante décrit en détail la séquence d’étapes qui se produit.

![Séquence de basculement et de restauration automatique des points de terminaison Traffic Manager](./media/traffic-manager-monitoring/timeline.png)

1. **GET**. Le système de surveillance de Traffic Manager exécute une requête GET sur le chemin d’accès et le fichier que vous avez spécifiés dans les paramètres de surveillance. Cette opération est répétée pour chaque point de terminaison.
2. **200 OK**. Le système de surveillance attend un message HTTP 200 OK dans un délai de 10 secondes. Lorsqu’il reçoit cette réponse, il reconnaît que le service est disponible.
3. **30 secondes entre les contrôles**. Le contrôle d’intégrité du point de terminaison est répété toutes les 30 secondes.
4. **Service indisponible**. Le service devient indisponible. Traffic Manager n’est pas averti avant le prochain contrôle d’intégrité.
5. **Tente d’accéder au fichier de surveillance (4 essais)**. Le système de surveillance exécute une requête GET, mais ne reçoit pas de réponse au cours du délai d’expiration de 10 secondes (sinon, une réponse autre que 200 peut être reçue). Il effectue ensuite trois essais supplémentaires par intervalles de 30 secondes. Lorsque l’un des essais aboutit, le nombre d’essais est réinitialisé.
6. **État défini comme Détérioré**. Après un quatrième échec consécutif, le système de surveillance marque l’état du point de terminaison non disponible comme Détérioré.
7. **Le trafic est détourné vers les autres points de terminaison**. Les serveurs de noms DNS Traffic Manager sont mis à jour et Traffic Manager ne renvoie plus le point de terminaison en réponse aux requêtes DNS. Les nouvelles connexions sont dirigées vers les autres points de terminaison disponibles. Toutefois, les réponses DNS précédentes incluant ce point de terminaison peuvent toujours être mises en cache par des serveurs DNS et des clients DNS récursifs. Ce qui pourrait amener certains clients à tenter de se connecter à ce point de terminaison. À mesure que ces caches expirent, les clients exécutent de nouvelles requêtes DNS et sont dirigés vers différents points de terminaison. La durée du cache est contrôlée par le paramètre TTL dans le profil Traffic Manager (par exemple, 30 secondes).
8. **Les contrôles d’intégrité continuent**. Traffic Manager continue à contrôler l’intégrité du point de terminaison pendant qu’il se trouve dans l’état Détérioré. Ainsi il peut détecter le moment auquel le point de terminaison retrouve son intégrité.
9. **Le service est remis en ligne**. Le service devient disponible. Le point de terminaison conserve sont état Détérioré dans Traffic Manager jusqu’à ce que le système de surveillance effectue le contrôle d’intégrité suivant.
10. **Le trafic vers le service est rétabli**. Traffic Manager envoie une requête GET et reçoit une réponse d’état 200 OK. Cela indique que le service a retrouvé un état sain. Les serveurs de noms Traffic Manager sont mis à jour et commencent à distribuer le nom DNS du service dans les réponses DNS. Le trafic retourne au point de terminaison tandis que les réponses DNS mises en cache et renvoyées vers d’autres points de terminaison expirent et que les connexions existantes à d’autres points de terminaison s’arrêtent.

>[AZURE.NOTE] Étant donné que Traffic Manager fonctionne au niveau du DNS, il ne peut pas modifier les connexions existantes à un point de terminaison. Lorsqu’il dirige le trafic entre les points de terminaison (en modifiant les paramètres de profil, ou lors d’un basculement ou d’une restauration automatique), Traffic Manager dirige les nouvelles connexions vers les points de terminaison disponibles. Toutefois, les autres points de terminaison peuvent continuer à recevoir le trafic via les connexions existantes, jusqu’à ce que ces sessions s’arrêtent. Pour libérer le trafic à partir des connexions existantes, les applications doivent limiter la durée de session utilisée avec chaque point de terminaison.

## Points de terminaison détériorés

Lorsqu’un point de terminaison a l’état Détérioré, il n’est plus renvoyé en réponse aux requêtes DNS (pour une exception à cette règle, voir la remarque à la fin de cette section). Au lieu de cela, un autre point de terminaison est choisi et renvoyé. Le choix d’autres points de terminaison dépend de la méthode de routage du trafic configurée :

- **Priorité**. Les points de terminaison forment une liste hiérarchisée. Le premier point de terminaison disponible sur la liste est toujours renvoyé. Si l’état d’un point de terminaison est Détérioré, alors le point de terminaison disponible suivant est renvoyé.
- **Pondération**. Tous les points de terminaison disponibles peuvent être renvoyés, choisis de façon aléatoire en fonction des pondérations qui leur ont été affectées et des pondérations des autres points de terminaison disponibles. Si l’état d’un point de terminaison est Détérioré, un point de terminaison est choisi dans le pool restant de points de terminaison disponibles.
- **Performances**. Le point de terminaison le plus proche de l’utilisateur final est renvoyé (à l’exception des points de terminaison avec l’état Désactivé ou Arrêté). Si ce point de terminaison est indisponible, le point de terminaison renvoyé est choisi de façon aléatoire parmi tous les autres points de terminaison disponibles. (Cela a pour but d’éviter un échec en cascade qui pourrait se produire en cas de surcharge du point de terminaison le plus proche. Vous pouvez configurer d’autres plans de basculement pour le routage du trafic à l’aide des [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).)

Pour plus d’informations, consultez la rubrique relative aux [méthodes de routage du trafic dans Traffic Manager](traffic-manager-routing-methods.md).

>[AZURE.NOTE] Que se passe-t-il si tous les points de terminaison Traffic Manager (à l’exception des points de terminaison avec l’état Désactivé ou Arrêté) sont marqués comme Détériorés à l’issue des contrôles d’intégrité ?

>Cela se produit généralement suite à une erreur de configuration du service (par exemple, une liste de contrôle de l’accès [ACL] bloquant les contrôles d’intégrité de Traffic Manager) ou une erreur de configuration du profil Traffic Manager (par exemple, un chemin d’accès de surveillance incorrect).

>Dans ce cas, Traffic Manager applique un meilleur effort et *réagit comme si tous les points de terminaison avec l’état Détérioré étaient en fait en ligne*. Cette méthode est préférable à l’autre solution, qui serait de ne renvoyer aucun point de terminaison dans la réponse DNS.

>La conséquence de ce comportement est la suivante : lorsque les contrôles d’intégrité de Traffic Manager ne sont pas configurés correctement, du point de vue du routage du trafic il semble que Traffic Manager *fonctionne* correctement. Toutefois, dans ce cas, aucun basculement de point de terminaison ne se produit en cas de défaillance d’un point de terminaison, ce qui a un impact sur la disponibilité globale de l’application. Pour s’assurer que cela ne se produise pas, il est important de vérifier que le profil affiche un état En ligne plutôt que Détérioré. L’état En ligne indique que les contrôles d’intégrité de Traffic Manager fonctionnent comme prévu.

Pour plus d’informations sur la résolution des problèmes d’échec de contrôles d’intégrité, consultez [Résolution des problèmes liés à l’état Détérioré d’Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).

## Forum Aux Questions

### Traffic Manager est-il résistant aux défaillances des régions Azure ?

Traffic Manager, qui intègre des contrôles d’intégrité et un basculement automatique entre les régions, est un composant clé de la fourniture d’applications haute disponibilité dans Azure, y compris d’applications résistantes aux pannes Azure à l’échelle régionale.

Par conséquent, Traffic Manager doit lui-même fournir un niveau de disponibilité extrêmement élevé, notamment une résistance aux défaillances régionales.

Si certains composants de Traffic Manager s’exécutent dans Azure, ils sont répartis entre les régions et conçus pour résister à une défaillance complète de région Azure, quelle qu’elle soit. Cette tolérance s’applique à tous les composants de Traffic Manager : les serveurs de noms DNS, l’API, la couche de stockage et le service de surveillance des points de terminaison.

Par conséquent, même en cas de panne totale d’une région Azure, Traffic Manager doit continuer à fonctionner normalement. Les clients ayant déployé des applications dans plusieurs régions Azure peuvent se fier à Traffic Manager pour diriger le trafic vers une instance disponible de leur application.

### En quoi le choix de l’emplacement du groupe de ressources affecte-t-il Traffic Manager ?

Traffic Manager est un service global unique. Il n’est pas régional. Le choix de l’emplacement du groupe de ressources na aucune influence sur les profils Traffic Manager déployés dans ce groupe de ressources.

Azure Resource Manager exige que tous les groupes de ressources spécifient un emplacement, qui détermine l’emplacement par défaut des ressources déployées dans ce groupe de ressources. Vous serez invité à préciser cet emplacement si vous créez un nouveau groupe de ressources lors de la création d’un profil Traffic Manager via le portail Azure.

Tous les profils Traffic Manager utilisent la valeur **global** comme emplacement. Comme il s’agit de la seule valeur acceptée, ce paramètre n’est pas accessible dans le portail Azure, PowerShell ou l’interface de ligne de commande Azure. Elle remplace la valeur par défaut du groupe de ressources.

### Comment déterminer l’état d’intégrité actuel de chaque point de terminaison ?

L’état de surveillance en cours de chaque point de terminaison, en plus du profil global s’affichent dans le portail Azure. Ces informations sont également disponibles via [l’API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx), [les applets de commande PowerShell](https://msdn.microsoft.com/library/mt125941.aspx) et [l’interface de ligne de commande Azure multiplateforme](../xplat-cli-install.md) de Traffic Monitor.

Il n’est pas possible d’afficher les informations d’historique relatives à l’intégrité passée des points de terminaison, ni de configurer des alertes sur les modifications de l’intégrité des points de terminaison.

### Puis-je surveiller les points de terminaison HTTPS ?

Oui. Traffic Manager prend en charge la détection sur HTTPS. Configurez simplement **HTTPS** comme protocole dans la configuration de la surveillance. Notez les points suivants :

- Le certificat côté serveur n’est pas validé.
- Les certificats SNI côté serveur ne sont pas pris en charge.
- Les certificats clients ne sont pas pris en charge.

### Quel en-tête hôte est utilisé pour les contrôles d’intégrité des points de terminaison ?

L’en-tête hôte utilisé dans les contrôles d’intégrité HTTP et HTTPS est le nom DNS associé avec chaque point de terminaison. Ces informations sont exposées en tant que cible du point de terminaison dans le portail Azure, les [applets de commande PowerShell](https://msdn.microsoft.com/library/mt125941.aspx), [l’interface de ligne de commande Azure multiplateforme](../xplat-cli-install.md) et [l’API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx).

Cette valeur fait partie de la configuration du point de terminaison. La valeur utilisée dans l’en-tête hôte ne peut pas être spécifiée séparément de la propriété cible.


## Étapes suivantes

En savoir plus sur le [fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md)

En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md) prises en charge par Traffic Manager

En savoir plus sur la [création d’un profil Traffic Manager](traffic-manager-manage-profiles.md)

[Résolution des problèmes liés à l’état Détérioré](traffic-manager-troubleshooting-degraded.md) sur un point de terminaison Traffic Manager

<!---HONumber=AcomDC_0824_2016-->