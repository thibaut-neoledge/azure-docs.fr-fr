<properties
   pageTitle="Fonctionnement de Traffic Manager | Microsoft Azure"
   description="Cet article vous aidera à comprendre le principe de fonctionnement d’Azure Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn"/>

<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/25/2016"
   ms.author="jonatul"/>

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

## Étapes suivantes

En savoir plus sur le [basculement automatique et la surveillance des points de terminaison](traffic-manager-monitoring.md) de Traffic Manager.

En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md) de Traffic Manager.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

<!---HONumber=AcomDC_0525_2016-->