---
title: "Fonctionnement de Traffic Manager | Microsoft Docs"
description: "Cet article explique le fonctionnement d’Azure Traffic Manager"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: a6c9370d-e60d-440f-aa82-b6d3fa5416b0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 6ba1732840d094497a9e2cb7a7ea7825a7f4bc89

---

# <a name="how-traffic-manager-works"></a>Fonctionnement de Traffic Manager

Azure Traffic Manager vous permet de contrôler la répartition du trafic entre les points de terminaison de votre application. Un point de terminaison est tout service côté Internet hébergé à l’intérieur ou à l’extérieur d’Azure.

Traffic Manager offre deux principaux avantages :

1. Distribution du trafic selon l’une des [méthodes de routage du trafic](traffic-manager-routing-methods.md)
2. [Analyse continue de l’intégrité des points de terminaison](traffic-manager-monitoring.md) et basculement automatique en cas d’échec des points de terminaison

Quand un client tente de se connecter à un service, il doit d’abord résoudre le nom DNS du service à une adresse IP. Le client se connecte ensuite à l’adresse IP pour accéder au service.

**L’essentiel ici est de comprendre que Traffic Manager fonctionne au niveau DNS.**  Traffic Manager utilise DNS pour diriger les clients vers des points de terminaison de service spécifiques en fonction des règles de la méthode de routage du trafic. Les clients se connectent **directement** au point de terminaison sélectionné. Traffic Manager n’est pas un proxy ou une passerelle. Traffic Manager ne voit pas le trafic entre le client et le service.

## <a name="traffic-manager-example"></a>Exemple Traffic Manager

Contoso Corp a développé un nouveau portail pour ses partenaires. L’URL de ce portail est https://partners.contoso.com/login.aspx. L’application est hébergée dans trois régions Azure. Pour améliorer la disponibilité et optimiser la performance globale, les clients utilisent Traffic Manager pour router le trafic client vers le point de terminaison disponible le plus proche.

Pour obtenir cette configuration :

* Ils déploient trois instances de leur service. Les noms DNS de ces déploiements sont « contoso-us.cloudapp.net », « contoso-eu.cloudapp.net » et « contoso-asia.cloudapp.net ».
* Ils créent ensuite un profil Traffic Manager nommé « contoso.trafficmanager.net », et le configurent pour utiliser la méthode de routage du trafic « Performance » sur les trois points de terminaison.
* Pour finir, ils configurent leur nom de domaine personnel, « partners.contoso.com » pour pointer vers « contoso.trafficmanager.net », en utilisant un enregistrement CNAME DNS.

![Configuration DNS de Traffic Manager][1]

> [!NOTE]
> Lorsque vous utilisez un domaine personnel avec Azure Traffic Manager, vous devez utiliser un enregistrement CNAME pour pointer votre nom de domaine personnel vers votre nom de domaine Traffic Manager. Les normes DNS ne vous permettent pas de créer un enregistrement CNAME au « sommet » (ou à la racine) d’un domaine. Par conséquent, vous ne pouvez pas créer d’enregistrement CNAME pour « contoso.com » (parfois appelé un domaine « nu »). Vous pouvez uniquement créer un enregistrement CNAME pour un domaine sous « contoso.com », tel que « www.contoso.com ». Pour contourner cette limitation, nous vous recommandons d’utiliser une simple redirection HTTP pour diriger les demandes pour « contoso.com » vers un autre nom, tel que « www.contoso.com ».

## <a name="how-clients-connect-using-traffic-manager"></a>Connexion des clients à l’aide de Traffic Manager

Dans la continuation de l’exemple précédent, quand un client demande la page https://partners.contoso.com/login.aspx, il accomplit les étapes suivantes pour résoudre le nom DNS et établir une connexion :

![Établissement de la connexion à l’aide de Traffic Manager][2]

1. Le client envoie une requête DNS à son service DNS récursif configuré pour résoudre le nom « partners.contoso.com ». Un service DNS récursif, parfois appelé service « DNS local », n’héberge pas directement de domaines DNS. Au lieu de cela, le client déleste le travail de contacter les divers services DNS faisant autorité sur Internet, nécessaire pour résoudre un nom DNS.
2. Pour résoudre le nom DNS, le service DNS récursif recherche les serveurs de noms pour le domaine « contoso.com ». Il contacte ensuite ces serveurs de noms pour demander l’enregistrement DNS « partners.contoso.com ». Les serveurs DNS contoso.com retournent l’enregistrement CNAME qui pointe vers contoso.trafficmanager.net.
3. Ensuite, le service DNS récursif recherche les serveurs de noms pour le domaine « trafficmanager.net », qui sont fournis par le service Azure Traffic Manager. Il envoie alors une demande pour l’enregistrement DNS « contoso.trafficmanager.net » à ces serveurs DNS.
4. Les serveurs de noms Traffic Manager reçoivent la demande. Ils choisissent un point de terminaison en fonction des critères suivants :

    - l’état configuré de chaque point de terminaison (les points de terminaison désactivés ne sont pas retournés) ;
    - l’état actuel de chaque point de terminaison, tel qu’il ressort des contrôles d’intégrité de Traffic Manager (pour plus d’informations, voir la rubrique relative à la [surveillance des points de terminaison avec Traffic Manager](traffic-manager-monitoring.md)) ;
    - la méthode de routage du trafic choisie (pour plus d’informations, voir [Méthodes de routage de Traffic Manager](traffic-manager-routing-methods.md)).

5. Le point de terminaison choisi est retourné en tant qu’autre enregistrement CNAME DNS. Dans ce cas, supposons que contoso-us.cloudapp.net est retourné.
6. Ensuite, le service DNS récursif recherche les serveurs de noms pour le domaine « cloudapp.net ». Il contacte ces serveurs de noms pour demander l’enregistrement DNS « contoso-us.cloudapp.net ». Un enregistrement DNS « A » contenant l’adresse IP du point de terminaison de service basé aux États-Unis est retourné.
7. Le service DNS récursif consolide les résultats et renvoie une seule réponse DNS au client.
8. Le client reçoit les résultats DNS et se connecte à l’adresse IP donnée. Notez qu’il se connecte directement au point de terminaison du service d’application, et non via Traffic Manager. Puisqu’il s’agit d’un point de terminaison HTTPS, le client exécute la négociation SSL/TLS nécessaire, puis soumet une demande HTTP GET pour la page « /login.aspx ».

Le service DNS récursif met en cache les réponses DNS qu’il reçoit. Le programme de résolution DNS sur l’appareil client met également en cache le résultat. La mise en cache permet que les requêtes DNS suivantes reçoivent une réponse plus rapidement, en utilisant les données du cache au lieu d’interroger d’autres serveurs de noms. La durée du cache est déterminée par la propriété « time-to-live » (TTL) de chaque enregistrement DNS. Des valeurs plus courtes entraînent une expiration plus rapide du cache, et donc davantage d’allers-retours avec les serveurs de noms Traffic Manager. Des valeurs plus longues signifient que davantage de temps peut être nécessaire pour diriger le trafic à l’écart d’un point de terminaison défaillant. Traffic Manager vous permet de configurer la durée de vie utilisée dans les réponses DNS de Traffic Manager, ce qui vous permet de choisir la valeur qui réponde au mieux aux besoins de votre application.

## <a name="faq"></a>Forum Aux Questions

### <a name="what-ip-address-does-traffic-manager-use"></a>Quelle est l’adresse IP utilisée par Traffic Manager ?

Comme expliqué à la section Fonctionnement de Traffic Manager, Traffic Manager fonctionne au niveau du DNS. Il envoie des réponses DNS pour diriger les clients vers le point de terminaison de service approprié. Les clients se connectent ensuite directement au point de terminaison du service, et non via Traffic Manager.

Par conséquent, Traffic Manager ne fournit pas de point de terminaison ou d’adresse IP pour permettre aux clients de se connecter. Par conséquent, si vous souhaitez une adresse IP statique pour votre service, celle-ci doit être configurée au niveau du service, pas dans Traffic Manager.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Traffic Manager prend-il en charge les sessions « persistantes » ?

Comme expliqué [précédemment](#how-clients-connect-using-traffic-manager), Traffic Manager opère au niveau du DNS. Il utilise les réponses DNS pour diriger les clients vers le point de terminaison de service approprié. Les clients se connectent directement au point de terminaison du service, et non via Traffic Manager. Par conséquent, Traffic Manager ne voit pas le trafic HTTP entre le client et le serveur.

En outre, l’adresse IP source de la requête DNS reçue par Traffic Manager appartient au service DNS récursif, pas au client. N’ayant donc aucun moyen d’effectuer le suivi de clients individuels, Traffic Manager ne peut pas implémenter de sessions « persistantes ». Cette limitation est commune à tous les systèmes de gestion du trafic DNS et n’est pas spécifique de Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Pourquoi une erreur HTTP s’affiche-t-elle quand j’utilise Traffic Manager ?

Comme expliqué [précédemment](#how-clients-connect-using-traffic-manager), Traffic Manager opère au niveau du DNS. Il utilise les réponses DNS pour diriger les clients vers le point de terminaison de service approprié. Les clients se connectent ensuite directement au point de terminaison du service, et non via Traffic Manager. Traffic Manager ne voit pas le trafic HTTP entre le client et le serveur. C’est pourquoi, toute erreur HTTP que vous voyez doit provenir de votre application. Pour que le client se connecte à l’application, toutes les étapes de résolution DNS sont accomplies. Cela inclut toute interaction de Traffic Manager sur le flux de trafic de l’application.

Un examen approfondi de l’application doit donc être effectué.

L’en-tête d’hôte HTTP envoyé à partir du navigateur du client est la source de problèmes la plus courante. Assurez-vous que l’application est configurée pour accepter l’en-tête d’hôte correct pour le nom de domaine que vous utilisez. Pour les points de terminaison utilisant Azure App Service, voir [Configuration d’un nom de domaine personnalisé pour une application web dans Azure App Service utilisant Traffic Manager](../app-service-web/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Quel est l’impact de Traffic Manager sur les performances ?

Comme expliqué [précédemment](#how-clients-connect-using-traffic-manager), Traffic Manager opère au niveau du DNS. Étant donné que les clients se connectent directement à vos points de terminaison de service, il n’y a aucun impact sur les performances inhérent à l’utilisation de Traffic Manager une fois la connexion établie.

Étant donné que Traffic Manager s’intègre aux applications au niveau du DNS, cela nécessite l’insertion d’une recherche DNS supplémentaire dans la chaîne de résolution DNS (voir [Exemples Traffic Manager](#traffic-manager-example)). L’impact sur le temps de résolution DNS de Traffic Manager est minime. Traffic Manager utilise un réseau mondial de serveurs de noms, et une mise en réseau [anycast](https://en.wikipedia.org/wiki/Anycast) pour garantir que les requêtes DNS sont toujours routées vers le serveur de noms disponible le plus proche. En outre, la mise en cache des réponses DNS signifie que la latence DNS supplémentaire générée par l’utilisation de Traffic Manager s’applique uniquement à une petite partie des sessions.

La méthode de Performance route le trafic vers le point de terminaison disponible le plus proche. Le résultat est que l’impact sur les performances globales associé à cette méthode doit être minimal. Toute augmentation de la latence DNS doit être compensée par une diminution de latence du réseau vers le point de terminaison.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Quels protocoles d’application puis-je utiliser avec Traffic Manager ?

Comme expliqué [précédemment](#how-clients-connect-using-traffic-manager), Traffic Manager opère au niveau du DNS. Une fois la recherche DNS terminée, les clients se connectent au point de terminaison d’application directement, et non via Traffic Manager. Cette connexion peut par conséquent utiliser tout protocole d’application. Toutefois, les contrôles d’intégrité de point de terminaison de Traffic Manager requièrent un point de terminaison HTTP ou HTTPS. Le point de terminaison pour un contrôle d’intégrité peut être différent du point de terminaison d’application auquel les clients se connectent.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Puis-je utiliser Traffic Manager avec un nom de domaine « nu » ?

Non. Les normes DNS n’autorisent pas la coexistence d’enregistrements CNAME avec d’autres enregistrements DNS du même nom. Le sommet (ou racine) d’une zone DNS contient toujours deux enregistrements DNS existants : la SOA et les enregistrements NS faisant autorité. Cela signifie qu’un enregistrement CNAME ne peut pas être créé au sommet de la zone sans violer les normes DNS.

Comme expliqué dans l’[exemple Traffic Manager](#traffic-manager-example), Traffic Manager nécessite un enregistrement CNAME DNS pour mapper le nom DNS personnel. Par exemple, vous mappez www.contoso.com au nom DNS de profil Traffic Manager contoso.trafficmanager.net. En outre, le profil Traffic Manager renvoie un deuxième CNAME DNS pour indiquer le point de terminaison auquel le client doit se connecter.

Pour contourner ce problème, nous vous recommandons d’utiliser une redirection HTTP pour diriger le trafic du nom de domaine nu vers une autre URL, qui peut ensuite utiliser Traffic Manager. Par exemple, le domaine nu « contoso.com » peut rediriger les utilisateurs vers l’enregistrement CNAME « www.contoso.com » qui pointe vers le nom DNS de Traffic Manager.

La prise en charge complète des domaines nus dans Traffic Manager est suivie dans notre backlog de fonctionnalités. Vous pouvez inscrire votre prise en charge pour cette demande de fonctionnalité en [votant pour celle-ci sur le site de commentaires de notre communauté](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [basculement automatique et la surveillance des points de terminaison](traffic-manager-monitoring.md)de Traffic Manager.

En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md)de Traffic Manager.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png



<!--HONumber=Nov16_HO5-->


