---
title: "Forum Aux Questions (FAQ) sur Azure Relay | Microsoft Docs"
description: "Répond à certaines questions fréquemment posées sur Azure Relay."
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 4b54ad027a7de02cba7821f2a9b7fd06ef3a825b
ms.openlocfilehash: ca2767340cb232722def8f06277cc84d5c76c1bf


---
# <a name="relay-faq"></a>FAQ Relay

Cet article répond à certaines questions fréquemment posées sur [Microsoft Azure Relay](https://azure.microsoft.com/services/service-bus/). Vous pouvez également visiter le [FAQ du support Azure](http://go.microsoft.com/fwlink/?LinkID=185083) pour obtenir des informations de support et de tarification générale Azure.

## <a name="general-questions"></a>Questions générales
### <a name="what-is-azure-relay"></a>Qu’est-ce qu’Azure Relay ?
Le [service Azure Relay](relay-what-is-it.md) simplifie vos applications hybrides en offrant la possibilité d’exposer les services qui résident dans un réseau d’entreprise sur le cloud public en toute sécurité, sans avoir à ouvrir une connexion de pare-feu ni à exiger des modifications intrusives dans une infrastructure de réseau d’entreprise.

### <a name="what-is-a-relay-namespace"></a>Qu’est-ce qu’un espace de noms Relay ?
Un [espace de noms](relay-create-namespace-portal.md) fournit un conteneur d’étendue pour l’adressage des ressources Relay au sein de votre application. Il est nécessaire de créer un espace de noms pour utiliser Relay. C’est l’une des premières étapes à effectuer au démarrage.

### <a name="what-happened-to-the-previously-named-relay-service"></a>Qu’est devenu le service Relais ?
L’ancien service **Service Bus Relay** s’appelle désormais *WCF Relay*. Vous pouvez continuer à utiliser ce service comme vous en avez l’habitude. Le service Connexions hybrides est une version modifiée d’un service tiré de BizTalk. Les services Relais WCF et Connexions hybrides continueront d’être pris en charge.

## <a name="pricing"></a>Tarification
Cette section répond à certaines questions fréquemment posées sur la tarification de Relay. Vous pouvez également visiter le [Forum aux questions de Support Azure](http://go.microsoft.com/fwlink/?LinkID=185083) pour obtenir des informations de tarification générale Microsoft Azure. Pour obtenir toutes les informations sur la tarification de Relay, consultez [Détails de la tarification de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Comment sont facturés les services Connexions hybrides et Relais WCF ?
Pour obtenir toutes les informations sur la tarification de Relay, consultez [Détails de la tarification de Service Bus][Pricing overview]. Outre les prix indiqués, vous êtes facturé pour les transferts de données associés aux sorties à l’extérieur du centre de données dans lequel votre application est déployée.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Comment est facturé le service Connexions hybrides ?
Voici trois exemples de scénarios :

1. Si vous avez un seul écouteur, par exemple une instance du gestionnaire de connexions hybrides installée et exécutée en permanence tout le mois, et que vous envoyez 3 Go de données sur la connexion au cours de ce mois, le montant total facturé sera de 5 $.
2. Si vous avez un seul écouteur, par exemple une instance du gestionnaire de connexions hybrides installée et exécutée en permanence tout le mois et que vous envoyez 10 Go de données sur la connexion au cours de ce mois, le montant total facturé sera de 7,50 $ : 5 $ pour la connexion et les 5 premiers Go + 2,50 $ pour les 5 Go supplémentaires.
3. Si vous avez deux instances (A et B) du gestionnaire de connexions hybrides qui sont installées et exécutées en permanence tout le mois, et que vous envoyez 3 Go de données sur la connexion A et 6 Go sur la connexion B, le montant total facturé sera de 10,50 $ : 5 $ pour la connexion A + 5 $ pour la connexion B + 0,50 $ (pour le volume à partir du 6e Go sur la connexion B).

Notez que les tarifs mentionnés dans les exemples sont applicables uniquement pendant la période de la version préliminaire et sont susceptibles d’être modifiés au moment de la sortie publique des connexions hybrides.

### <a name="how-are-wcf-relay-hours-calculated"></a>Comment les heures Relay WCF sont-elles calculées ?
Les heures de relais sont facturées en fonction du temps cumulé pendant lequel chaque relais Service Bus Relay est « ouvert ». Un relais est explicitement instancié et ouvert à une adresse Service Bus (URL espaces de noms de service) lorsqu’un service WCF doté d’un relais, ou « écouteur de relais », se connecte à cette adresse pour la première fois. Il est fermé lorsque le dernier écouteur se déconnecte de cette adresse. Par conséquent, pour la facturation, un relais est considéré comme « ouvert » dès le moment où le premier écouteur de relais se connecte, jusqu’au moment où l’écouteur de relais se déconnecte de l’adresse Service Bus du relais.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-given-relay"></a>Que se passe-t-il si plusieurs écouteurs sont connectés à un relais donné ?
Dans certains cas, plusieurs écouteurs peuvent être connectés à un même relais. Un relais est considéré comme « ouvert » lorsqu’au moins un écouteur Relay y est connecté. L’ajout d’écouteurs supplémentaires à un relais ouvert entraîne des heures Relay supplémentaires. Le nombre d’expéditeurs Relay (clients qui appellent ou envoient des messages aux relais) connectés à un relais n’a aucun effet sur le calcul des heures Relay.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Comment le nombre de messages est-il calculé pour les relais WCF ?
**Cela s’applique uniquement aux relais WCF et cela ne représente pas un coût de connexions hybrides**

En général, les messages facturables des relais sont calculés suivant la méthode décrite ci-dessus pour les entités réparties (files d’attente, rubriques et abonnements). Toutefois, il existe plusieurs différences importantes :

L’envoi d’un message à un Service Bus Relay est traité comme un envoi « complet » à l’écouteur Relay qui reçoit le message, plutôt que comme un envoi au Service Bus Relay suivi d’une livraison à l’écouteur Relay. Par conséquent, un appel de service de type demande-réponse (jusqu’à 64 Ko) à un écouteur Relay entraîne deux messages facturables : un pour la demande et un autre pour la réponse (en supposant que la réponse est également \<= 64 Ko). Ceci diffère de l’utilisation d’une file d’attente servant d’intermédiaire entre un client et un service. Dans ce cas, le même modèle de demande-réponse nécessiterait un envoi de la demande à la file d’attente, puis un retrait de la file d’attente/une livraison depuis la file d’attente au service, suivi d’un envoi de réponse à une autre file d’attente et un retrait de la file d’attente/une livraison depuis cette file d’attente au client. Si l’on suit les mêmes hypothèses de taille (\<= 64 Ko) du début à la fin, le modèle de file d’attente par médiation générerait quatre messages facturables, soit le double du nombre facturé pour implémenter le même modèle avec Relay. Bien sûr, il existe des avantages à utiliser les files d’attente pour atteindre ce modèle, par exemple, la durabilité et le nivellement de charge. Ces avantages peuvent justifier les dépenses supplémentaires.

Les relais ouverts à l’aide de la liaison WCF netTCPRelay traitent les messages non pas en tant que messages individuels, mais en tant que flux de données transitant par le système. En d’autres termes, seuls l’expéditeur et l’écouteur peuvent voir la trame du message individuel envoyé/reçus avec cette liaison. Par conséquent, pour les relais par liaison netTCPRelay, toutes les données sont traitées comme un flux de données pour le calcul des messages facturables. Dans ce cas, Service Bus calcule la quantité totale de données envoyées ou reçues via chaque relais individuel sur une base de 5 minutes et divise ce total par 64 Ko afin de déterminer le nombre de messages facturables pour le relais en question durant cette période.

## <a name="quotas"></a>Quotas
| Nom du quota | Étendue | Type | Comportement en cas de dépassement | Valeur |
| --- | --- | --- | --- | --- |
| Écouteurs simultanés sur un relais |Entité |Statique |Les demandes de connexions supplémentaires suivantes sont rejetées et le code appelant reçoit une exception. |25 |
| Écouteurs Relay simultanés |À l’échelle du système |Statique |Les demandes de connexions supplémentaires suivantes sont rejetées et le code appelant reçoit une exception. |2 000 |
| Connexions Relay simultanées pour tous les points de terminaison Relay dans un espace de noms de service |À l’échelle du système |Statique |- |5 000 |
| Points de terminaison Relay par espace de noms de service |À l’échelle du système |Statique |- |10 000 |
| Taille de message pour les relais [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) et [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) |À l’échelle du système |Statique |Les messages entrants dont la taille dépasse ces quotas sont rejetés et le code appelant reçoit une exception. |64 Ko |
| Taille de message pour les relais [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) et [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) |À l’échelle du système |Statique |- |Illimité |

### <a name="does-relay-have-any-usage-quotas"></a>Relay a-t-il des quotas d’utilisation ?
Par défaut, pour n’importe quel service cloud, Microsoft définit un quota d’utilisation agrégée mensuel calculé avec tous les abonnements d’un client. Nous savons que vous pouvez avoir des besoins dépassant ces limites, veuillez contacter le service clientèle à tout moment pour que nous puissions déterminer vos besoins et ajuster ces limites en conséquence. Pour Service Bus, les quotas d’utilisation d’agrégation sont les suivants :

* 5 milliards de messages
* 2 millions d’heures Relay

Nous nous réservons le droit de désactiver un compte client ayant dépassé son quota d’utilisation pour un mois donné. Nous enverrons une notification par messagerie électronique pour contacter le client avant d’exécuter une action quelconque. Les clients qui dépassent ces quotas sont responsables des frais qui dépassent ces quotas.

### <a name="naming-restrictions"></a>Restrictions concernant l’attribution de noms
Un nom d’espace de noms Relay peut uniquement contenir entre 6 et 50 caractères.

## <a name="subscription-and-namespace-management"></a>Gestion des abonnements et des espaces de noms
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Migration d’un espace de noms vers un autre abonnement Azure
Vous pouvez utiliser les commandes PowerShell (voir [cet article](../service-bus-messaging/service-bus-powershell-how-to-provision.md)) pour déplacer un espace de noms d’un abonnement Azure à un autre. Pour exécuter cette opération, l’espace de noms doit être déjà actif. Il est également impératif que l’utilisateur qui exécute les commandes soit un administrateur des abonnements source et cible.

## <a name="troubleshooting"></a>Résolution de problèmes
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-their-suggested-actions"></a>Quelles sont les exceptions générées par les API Azure Relay et les actions recommandées ?
L’article [Exceptions Relay][Exceptions RelayRelay exceptions] décrit certaines de ces exceptions, ainsi que les actions recommandées.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Qu’est-ce qu’une signature d’accès partagé et quels langages prennent en charge la génération d’une signature ?
Les signatures d’accès partagé sont un mécanisme d’authentification basé sur des hachages sécurisés SHA-256 ou des URI. Pour plus d’informations sur la génération de vos propres signatures en Node, PHP, Java et C\#, consultez l’article [Signatures d’accès partagé][Shared Access Signatures].

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas-overview.md

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Est-il possible de mettre des points de terminaison Relay sur liste blanche ?
Oui. Le client Relay établit des connexions au service Relay à l’aide des noms de domaine complets. Cela permet aux clients d’ajouter une entrée pour `*.servicebus.windows.net` sur les pare-feu qui prennent en charge les listes blanches de DNS.

## <a name="next-steps"></a>Étapes suivantes
* [Créer un espace de noms](relay-create-namespace-portal.md)
* [Prise en main de .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Prise en main de Node](relay-hybrid-connections-node-get-started.md)



<!--HONumber=Feb17_HO1-->


