<properties
   pageTitle="Fonctionnement de Traffic Manager | Microsoft Azure"
   description="Cet article vous aidera à comprendre le principe de fonctionnement d’Azure Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn"/>

<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/07/2016"
   ms.author="sewhee"/>

# Fonctionnement de Traffic Manager

Azure Traffic Manager vous permet de contrôler la distribution du trafic entre les points de terminaison de votre application. Un point de terminaison peut être n’importe quel point de terminaison sur Internet hébergé dans Azure ou en dehors d’Azure.

Traffic Manager offre deux principaux avantages :

1. Distribution du trafic selon l’une des [méthodes de routage du trafic](traffic-manager-routing-methods.md)
2. [Analyse continue de l’intégrité des points de terminaison](traffic-manager-monitoring.md) et basculement automatique en cas d’échec des points de terminaison

Lorsqu’un utilisateur final tente de se connecter à un point de terminaison de service, son client (PC, téléphone, etc.) doit tout d’abord résoudre le nom DNS de ce point de terminaison sur une adresse IP. Le client se connecte ensuite à l’adresse IP pour accéder au service.

**L’essentiel ici est de comprendre que Traffic Manager fonctionne au niveau DNS.** Traffic Manager utilise DNS pour diriger les utilisateurs finaux vers des points de terminaison de service particuliers, selon la méthode de routage du trafic choisie et l’état du point de terminaison actuel. Les clients se connectent ensuite **directement** aux points de terminaison sélectionnés. Traffic Manager n’est pas un proxy et ne voit pas le trafic qui transite entre le client et le service.

## Exemple Traffic Manager

Contoso Corp a développé un nouveau portail pour ses partenaires. L’URL de ce portail sera https://partners.contoso.com/login.aspx. L’application est hébergée dans Azure et, pour améliorer la disponibilité et optimiser les performances globales, la société souhaite déployer l’application dans 3 régions du monde et utiliser Traffic Manager pour distribuer les utilisateurs finaux sur le point de terminaison disponible le plus proche.

Pour obtenir cette configuration :

- Ils déploient 3 instances de leur service. Les noms DNS de ces déploiements sont « contoso-us.cloudapp .net », « contoso-eu.cloudapp .net » et « contoso-asia.cloudapp .net ».
- Ils créent ensuite un profil Traffic Manager nommé « contoso.trafficmanager.net », qui est configuré pour utiliser la méthode de routage du trafic « Performance » sur les 3 points de terminaison indiqués ci-dessus.
- Pour finir, ils configurent leur domaine personnel, « partners.contoso.com », pour pointer vers « contoso.trafficmanager.net », à l’aide d’un enregistrement DNS CNAME.

![Configuration DNS de Traffic Manager][1]

> [AZURE.NOTE] Lorsque vous utilisez un domaine personnel avec Azure Traffic Manager, vous devez utiliser un enregistrement CNAME pour pointer votre nom de domaine personnel vers votre nom de domaine Traffic Manager.

> En raison d’une restriction des normes DNS, un enregistrement CNAME ne peut pas être créé au « sommet » (ou à la racine) d’un domaine. Par conséquent, vous ne peut pas créer un enregistrement CNAME pour « contoso.com » (il s’agit de ce que l’on appelle parfois un domaine « nu »). Vous pouvez uniquement créer un enregistrement CNAME pour un domaine se trouvant sous « contoso.com », tel que « www.contoso.com ».

> Vous ne pouvez donc pas utiliser Traffic Manager directement avec un domaine nu. Pour contourner ce problème, nous vous recommandons d’utiliser une simple redirection HTTP pour diriger les demandes pour « contoso.com » vers un autre nom, par exemple « www.contoso.com ».

## Connexion des clients à l’aide de Traffic Manager

Lorsqu’un utilisateur demande la page https://partners.contoso.com/login.aspx (comme décrit dans l’exemple ci-dessus), le client effectue les étapes suivantes pour résoudre le nom DNS et établir une connexion.

![Établissement de la connexion à l’aide de Traffic Manager][2]

1.	Le client (PC, téléphone, etc.) initie une requête DNS pour « partners.contoso.com » auprès de son service DNS récursif configuré (un service DNS récursif, parfois appelé service « DNS local », n’héberge pas directement de domaines DNS. Le client l’utilise pour éviter d’avoir à contacter sur Internet les divers services DNS faisant autorité nécessaires afin de résoudre le nom DNS).
2.	Le service DNS récursif résout maintenant le nom DNS « partners.contoso.com ». Tout d’abord, le service DNS récursif recherche les serveurs de noms pour le domaine « contoso.com ». Il contacte ensuite les serveurs de noms pour demander l’enregistrement DNS « partners.contoso.com ». L’enregistrement CNAME sur contoso.trafficmanager.net est renvoyé.
3.	Le service DNS récursif recherche maintenant les serveurs de noms pour le domaine « trafficmanager.net », lesquels sont fournis par le service Azure Traffic Manager. Il contacte ces serveurs de noms pour demander l’enregistrement DNS « contoso.trafficmanager.net ».
4.	Les serveurs de noms Traffic Manager reçoivent la demande. Ils choisissent ensuite le point de terminaison qui doit être retourné, selon : a. l’état activé/désactivé de chaque point de terminaison (les points de terminaison désactivés ne sont pas retournés) ; b. l’état actuel de chaque point de terminaison, tel qu’il ressort des contrôles d’intégrité de Traffic Manager. Pour plus d’informations, consultez la rubrique relative à la surveillance des points de terminaison avec Traffic Manager ; c. la méthode de routage du trafic choisie. Pour plus d’informations, consultez la rubrique relative aux méthodes de routage du trafic dans Traffic Manager.
5.	Le point de terminaison choisi est retourné sous la forme d’un autre enregistrement CNAME DNS ; dans ce cas, nous supposons que contoso-us.cloudapp.net est retourné.
6.	Le service DNS récursif recherche maintenant les serveurs de noms pour le domaine « cloudapp.net ». Il contacte ces serveurs de noms pour demander l’enregistrement DNS « contoso-us.cloudapp.net ». Un enregistrement DNS « A » contenant l’adresse IP du point de terminaison de service basé aux États-Unis est retourné.
7.	Le service DNS récursif retourne au client les résultats consolidés de la séquence de résolutions de noms ci-dessus.
8.	Le client reçoit les résultats DNS du service DNS récursif et se connecte à l’adresse IP donnée. Notez qu’il se connecte directement au point de terminaison du service d’application, et non via Traffic Manager. Puisqu’il s’agit d’un point de terminaison HTTPS, il exécute la négociation SSL/TLS nécessaire avant de soumettre une demande HTTP GET pour la page « /login.aspx ».

Notez que le service DNS récursif mettra en cache les réponses DNS qu’il reçoit, ainsi que le client DNS sur l’appareil de l’utilisateur final. Les requêtes DNS suivantes recevront donc plus rapidement une réponse, en utilisant les données du cache sans interroger d’autres serveurs de noms. La durée du cache est déterminée par la propriété « time-to-live » (TTL) de chaque enregistrement DNS. Des valeurs plus courtes entraînent une expiration plus rapide du cache et, par conséquent, davantage d’allers-retours sur les serveurs de noms Traffic Manager ; avec des valeurs plus longues, la redirection du trafic suite à l’échec d’un point de terminaison peut prendre davantage de temps. Traffic Manager vous permet de configurer la durée de vie utilisée dans les réponses DNS de Traffic Manager, ce qui vous permet de choisir la valeur qui réponde au mieux aux besoins de votre application.

## Forum Aux Questions

### Quelle est l’adresse IP utilisée par Traffic Manager ?

Comme expliqué à la section Fonctionnement de Traffic Manager, Traffic Manager fonctionne au niveau du DNS. Il utilise les réponses DNS pour diriger les clients vers le point de terminaison de service approprié. Les clients se connectent ensuite directement au point de terminaison du service, et non via Traffic Manager.

Par conséquent, Traffic Manager ne fournit pas de point de terminaison ou d’adresse IP pour permettre aux clients de se connecter. Si par exemple une adresse IP statique est requise, elle doit être configurée au niveau du service et non dans Traffic Manager.

### Traffic Manager prend-il en charge les sessions « rémanentes » ?

Comme expliqué [ci-dessus](#how-clients-connect-using-traffic-manager), Traffic Manager fonctionne au niveau du DNS. Il utilise les réponses DNS pour diriger les clients vers le point de terminaison de service approprié. Les clients se connectent ensuite directement au point de terminaison du service, et non via Traffic Manager. Par conséquent, Traffic Manager ne voit pas le trafic HTTP entre le client et le serveur, y compris les cookies.

En outre, l’adresse IP source de la requête DNS reçue par Traffic Manager correspond à l’adresse IP du service DNS récursif, et non à l’adresse IP du client.

Traffic Manager n’a donc aucun moyen d’identifier ou d’effectuer le suivi des clients individuels, et ne peut par conséquent pas implémenter de sessions « rémanentes ». Cela est courant pour tous les systèmes de gestion du trafic DNS, il ne s’agit pas d’une restriction d’utilisation de Traffic Manager.

### J’obtiens une erreur HTTP lors de l’utilisation de Traffic Manager... Pourquoi ?

Comme expliqué [ci-dessus](#how-clients-connect-using-traffic-manager), Traffic Manager fonctionne au niveau du DNS. Il utilise les réponses DNS pour diriger les clients vers le point de terminaison de service approprié. Les clients se connectent ensuite directement au point de terminaison du service, et non via Traffic Manager.

Par conséquent, Traffic Manager ne voit pas le trafic HTTP entre le client et le serveur et ne peut pas générer d’erreurs au niveau HTTP. Si vous constatez une erreur HTTP, c’est qu’elle provient probablement de votre application. Dans la mesure où le client se connecte à l’application, cela signifie également que la résolution DNS comprenant le rôle Traffic Manager a été effectuée.

Un examen approfondi de l’application doit donc être effectué.

L’un des problèmes courants lorsque vous utilisez Traffic Manager est que l’en-tête HTTP « d’hôte » transmise par le navigateur à l’application affiche le nom de domaine utilisé dans le navigateur. Cela peut être le nom de domaine Traffic Manager (par exemple, myprofile.trafficmanager.net) si vous utilisez ce nom de domaine au cours du test, ou le domaine personnel que CNAME a configuré pour pointer vers le nom de domaine Traffic Manager. Dans les deux cas, vérifiez que l’application est configurée pour accepter cet en-tête d’hôte.

Si votre application est hébergée dans Azure App Service, consultez l’article [Configuration d’un nom de domaine personnalisé pour une application web dans Azure App Service utilisant Traffic Manager](../app-service-web/web-sites-traffic-manager-custom-domain-name.md).

### Quel est l’impact de Traffic Manager sur les performances ?

Comme expliqué [ci-dessus](#how-clients-connect-using-traffic-manager), Traffic Manager fonctionne au niveau du DNS. Il utilise les réponses DNS pour diriger les clients vers le point de terminaison de service approprié. Les clients se connectent ensuite directement au point de terminaison du service, et non via Traffic Manager.

Étant donné que les clients se connectent directement à vos points de terminaison de service, il n’y a aucun impact sur les performances inhérent à l’utilisation de Traffic Manager une fois la connexion établie.

Étant donné que Traffic Manager s’intègre aux applications au niveau du DNS, cela nécessite l’insertion d’une recherche DNS supplémentaire dans la chaîne de résolution DNS (voir [Exemples Traffic Manager](#traffic-manager-example)). L’impact sur le temps de résolution DNS de Traffic Manager est minime. Traffic Manager utilise un réseau mondial de serveurs de noms et utilise la mise en réseau Anycast pour garantir que les requêtes DNS sont toujours acheminées vers le serveur de noms disponible le plus proche. En outre, la mise en cache des réponses DNS signifie que la latence DNS supplémentaire générée par l’utilisation de Traffic Manager s’applique uniquement à une petite partie des sessions.

Le résultat est que l’impact sur les performances globales associé à l’incorporation de Traffic Manager à votre application est généralement minime.

De plus, lorsque la [méthode de routage du trafic « Performance »](traffic-manager-routing-methods.md#performance-traffic-routing-method) est utilisée, l’augmentation de la latence DNS doit être bien plus que simplement compensée par l’amélioration des performances obtenue par le routage des utilisateurs finaux vers leur point de terminaison disponible le plus proche.

### Quels protocoles d’application puis-je utiliser avec Traffic Manager ?
Comme expliqué [ci-dessus](#how-clients-connect-using-traffic-manager), Traffic Manager fonctionne au niveau du DNS. Une fois la recherche DNS terminée, les clients se connectent au point de terminaison d’application directement, et non via Traffic Manager. Cette connexion peut par conséquent utiliser tout protocole d’application.

Toutefois, les vérifications d’intégrité de point de terminaison de Traffic Manager requièrent un point de terminaison HTTP ou HTTPS. Celui-ci peut être distinct du point de terminaison d’application auquel les clients se connectent, en spécifiant un port TCP ou un chemin d’accès URI différent dans les paramètres de vérification d’intégrité du profil Traffic Manager.

### Puis-je utiliser Traffic Manager avec un nom de domaine « nu » (sans www) ?

Pas actuellement.

Le type d’enregistrement DNS CNAME est utilisé pour créer un mappage d’un nom DNS vers un autre nom. Comme expliqué dans [l’exemple Traffic Manager](#traffic-manager-example), Traffic Manager nécessite un enregistrement DNS CNAME pour mapper le nom DNS personnel (par exemple, www.contoso.com) sur le nom DNS du profil Traffic Manager (par exemple, contoso.trafficmanager.net). En outre, le profil Traffic Manager renvoie lui-même un deuxième DNS CNAME pour indiquer le point de terminaison auquel le client doit se connecter.

Les normes DNS n’autorisent pas les enregistrements CNAME à coexister avec d’autres enregistrements DNS du même type. Étant donné que l’extrémité (ou la racine) d’une zone DNS contient toujours deux enregistrements DNS préexistants (le SOA et les enregistrements NS faisant autorité), cela signifie qu’un enregistrement CNAME ne peut pas être créé à l’extrémité de la zone sans violer les normes DNS.

Pour contourner ce problème, nous recommandons que les services utilisant un domaine nu (sans www) et souhaitant avoir recours à Traffic Manager utilisent une redirection HTTP pour diriger le trafic du domaine nu vers une URL différente, ce qui permettra ensuite d’utiliser Traffic Manager. Par exemple, le domaine nu « contoso.com » peut rediriger les utilisateurs vers « www.contoso.com » pour qu’ils puissent ensuite utiliser Traffic Manager.

La prise en charge complète des domaines nus dans Traffic Manager est suivie dans notre backlog de fonctionnalités. Si vous êtes intéressé par cette fonctionnalité, faites-nous part de votre soutien en [votant sur notre site de commentaires de la communauté](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

## Étapes suivantes

En savoir plus sur le [basculement automatique et la surveillance des points de terminaison](traffic-manager-monitoring.md) de Traffic Manager.

En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md) de Traffic Manager.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

<!---HONumber=AcomDC_0824_2016-->