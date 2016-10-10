<properties 
    pageTitle="Tarification et facturation de Service Bus | Microsoft Azure"
    description="Présentation de la structure de prix de Service Bus."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/13/2016"
    ms.author="sethm" />

# Tarification et facturation de Service Bus

Service Bus est disponible en trois niveaux de service : de base, standard, et [premium](../service-bus-messaging/service-bus-premium-messaging.md). Vous pouvez choisir un niveau de service pour chaque espace de noms Service Bus que vous créez et cette sélection s’applique à l’ensemble des files d’attente, rubriques/abonnements, relais et Event Hubs créés dans cet espace de noms.

>[AZURE.NOTE] Pour obtenir des informations détaillées sur la tarification actuelle de Service Bus, consultez la page de [tarification Azure Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) et la [FAQ de Service Bus](service-bus-faq.md#service-bus-pricing).

Service Bus utilise les deux mesures suivantes pour les files d’attente et les rubriques/abonnements :

1. **Opérations de messagerie** : définies en tant qu’appels API par rapport aux points de terminaison du service de file d’attente ou de rubrique/abonnement. Cette mesure remplacera les messages envoyés ou reçus en tant qu’unités principales de l’utilisation facturable des files d’attente et des rubriques/abonnements.

2. **Connexions réparties** : définies comme le nombre maximal de connexions persistantes ouvertes par rapport aux files d’attente, rubriques/abonnements ou Event Hubs pendant une période d’échantillonnage d’une heure donnée. Cette mesure s’appliquera uniquement au niveau standard, dans lequel vous pouvez ouvrir des connexions supplémentaires (auparavant, les connexions étaient limitées à 100 par file d’attente/rubrique/abonnement) pour des frais minimaux par connexion.

Le niveau **standard** présente une tarification progressive pour les opérations exécutées avec les files d’attente et les rubriques/abonnements, ce qui entraîne des remises basées sur le volume de pouvant atteindre 80 %, pour les plus hauts niveaux d’utilisation. Il existe également des frais de base de niveau standard de 10 $ par mois, ce qui vous permet d'effectuer jusqu'à 12,5 millions d'opérations par mois sans frais supplémentaires.

Le niveau **premium** isole les ressources au niveau des couches processeur et mémoire, ce qui permet d'exécuter chaque charge de travail client de manière isolée. Ce conteneur de ressources est appelé *unité de messagerie*. Au moins une unité de messagerie est allouée à chaque espace de noms premium. Vous pouvez acheter une, deux ou quatre unités de messagerie pour chaque espace de noms Service Bus Premium. Une entité ou une charge de travail unique peut couvrir plusieurs unités de messagerie et le nombre d’unités de messagerie peut être modifié à volonté. En revanche, les frais qui vous sont facturés sont établis par période de 24 heures. Au final, les performances de votre solution Service Bus sont non seulement prévisibles et répétables, mais aussi supérieures. La messagerie Azure Service Bus Premium s'appuie sur le moteur de stockage introduit dans Azure Event Hubs. La messagerie Premium offre des performances de pointe bien supérieures à celle de la couche Standard.

Les frais de base standards sont facturés en une seule fois par mois et par abonnement Azure. En d’autres termes, après avoir créé un espace de noms Service Bus de niveau standard ou premium, vous serez en mesure de créer autant d’espaces de noms standard ou premium que possible dans ce même abonnement Azure, sans encourir de frais de base supplémentaires.

Tous les espaces de noms Service Bus existants créés avant le 1er novembre 2014 ont été automatiquement placés dans le niveau standard. Cette mesure garantit un accès à toutes les fonctionnalités actuellement disponibles avec Service Bus. Par la suite, vous pourrez utiliser le [portail Azure Classic][] pour redescendre votre version vers le niveau de base si vous le souhaitez.

Le tableau suivant résume les différences fonctionnelles entre les niveaux de base et standard/premium.

|Fonctionnalité|De base|Standard/Premium|
|---|---|---|
|Event Hubs|Oui|Oui|
|Files d’attente|Oui|Oui|
|Messages planifiés|Oui|Oui|
|Rubriques/abonnements|Non|Oui|
|Relais|Non|Oui|
|Transactions|Non|Oui|
|Déduplication|Non|Oui|
|Sessions|Non|Oui|
|Messages volumineux|Non|Oui|
|ForwardTo|Non|Oui|
|SendVia|Non|Oui|
|Connexions réparties (incluses)|100 par espace de noms Service Bus|1 000 par abonnement Azure|
|Connexions réparties (dépassement autorisé)|Non|Oui (facturable)|

## Opérations de messagerie

Dans le cadre du nouveau modèle de tarification, la facturation des files d’attente et des rubriques/abonnements évolue. Ces entités passent d'un modèle de facturation au message à une facturation à l'opération. Une « opération » désigne un appel d’API adressé au point de terminaison d’un service de file d’attente ou d’une rubrique/un abonnement. Cela inclut les opérations de gestion, d'envoi/réception et d'état de session.

|Type d'opération|Description|
|---|---|
|gestion|Création, lecture, mise à jour, suppression (CRUD) sur les files d’attente ou rubriques/abonnements.|
|Messagerie|Envoi et réception de messages avec files d’attente ou rubriques/abonnements.|
|État de la session|Récupération ou configuration de l’état de session d’une file d’attente ou d’une rubrique/un abonnement.|

Les prix suivants sont appliqués depuis le 1er novembre 2014 :

|De base|Coût|
|---|---|
|Opérations|0,05 $ par million d'opérations|

|Standard|Coût|
|---|---|
|Frais de base|10 $/mois|
|Premières 12,5 millions d'opérations par mois|Inclus|
|De 12,5 à 100 millions d'opérations par mois|0,80 $ par million d'opérations|
|De 100 à 2 500 millions d'opérations par mois|0,50 $ par million d'opérations|
|Plus de 2 500 millions d'opérations par mois|0,20 $ par million d'opérations|

>[AZURE.NOTE] Le niveau premium est actuellement en version préliminaire et le prix suivant reflète une remise de 50 %.

|Premium|Coût|
|---|---|
|Quotidien|Tarif fixe de 11,13 $ par unité de message|

## Connexions réparties

Les *connexions réparties* répondent aux modèles d’utilisation client qui impliquent un grand nombre d’expéditeurs/de destinataires « en permanence connectés » aux files d’attente, aux rubriques/abonnements ou à Event Hubs. Les expéditeurs/destinataires connectés de façon permanente sont ceux qui se connectent à l'aide d'AMQP ou de HTTP avec un délai d'expiration de réception non nul (par exemple, l'interrogation longue HTTP). Les expéditeurs et les destinataires HTTP qui utilisent un délai d'expiration immédiat ne génèrent pas de connexions réparties.

Auparavant, les files d’attente et les rubriques/abonnements avaient une limite de 100 connexions simultanées par URL. Le modèle de facturation actuel supprime la limite par URL des files d’attente et des rubriques/abonnements et implémente des quotas et des mesures de connexions réparties au niveau de l’espace de noms Service Bus et de l’abonnement Azure.

Le niveau de base inclut et est strictement limité à 100 connexions réparties par espace de noms Service Bus. Les connexions au-dessus de ce nombre sont rejetées dans le niveau de base. Le niveau standard supprime la limite par espace de noms et compte l’utilisation de connexions réparties de façon globale au sein de tout l’abonnement Azure. Au niveau standard, 1 000 connexions réparties par abonnement Azure seront proposées gratuitement (hors frais de base). L’utilisation de plus d’un total de 1 000 connexions réparties sur l’ensemble des espaces de noms Service Bus dans le service standard et dans un abonnement Azure sera facturée selon un calendrier progressif, comme indiqué dans le tableau suivant.

|Connexions réparties (niveau standard)|Coût|
|---|---|
|Premières 1 000/mois|Incluses dans les frais de base|
|De 1 000 à 100 000/mois|0,03 $ par connexion/mois|
|De 100 000 à 500 000/mois|0,025 $ par connexion/mois|
|Plus de 500 000/mois|0,015 $ par connexion/mois|

>[AZURE.NOTE] 1 000 connexions réparties sont incluses avec le niveau de messagerie standard (via les frais de base) et peuvent être partagées entre toutes les files d’attente, rubriques/abonnements et Event Hubs au sein de l’abonnement Azure associé.

>[AZURE.NOTE] La facturation est basée sur le nombre maximal de connexions simultanées et est calculée au prorata toutes les heures basées sur 744 heures par mois.

|Niveau premium
|---|
|Les connexions réparties ne sont pas facturées au niveau premium.|

Pour plus d’informations sur les connexions réparties, consultez la section [FAQ](#faq) plus loin dans cette rubrique.

## Relais

Les relais sont disponibles uniquement dans les espaces de noms de niveau standard. Dans le cas contraire, les quotas de tarification et de connexion destinés aux relais restent inchangés. Cela signifie que les relais continueront à être facturés selon le nombre de messages (pas d'opérations) et les heures de relais.

|Tarification des relais|Coût|
|---|---|
|Heures de relais|0,10 $ toutes les 100 heures de relais|
|Messages|0,01 $ tous les 10 000 messages|

## Forum Aux Questions

### Comment le nombre d’heures de relais est-il calculé ?

Voir [cette rubrique](../service-bus-messaging/service-bus-faq.md#how-is-the-relay-hours-meter-calculated).

### Que sont les connexions réparties et combien me coûtent-elles ?

Une connexion répartie est définie comme suit :

1. Une connexion AMQP d’un client à une rubrique/un abonnement, une file d’attente ou un Event Hub Service Bus.

2. Un appel HTTP pour recevoir le message d’une rubrique ou d’une file d’attente Service Bus dont le délai d’expiration de réception est supérieur à zéro.

Service Bus facture le nombre maximal de connexions simultanées réparties qui dépassent la quantité incluse (1 000 au niveau standard). Des pics sont mesurés toutes les heures, au prorata en divisant les valeurs par 744 heures par mois et en les ajoutant à la période de facturation mensuelle. La quantité incluse (1 000 connexions réparties par mois) est appliquée à la fin de la période de facturation par rapport à la somme des pics horaires calculés au prorata.

Par exemple :

1. Chacun des 10 000 appareils se connecte via une connexion AMQP unique et reçoit des commandes à partir d’une rubrique Service Bus. Les appareils envoient des événements de télémétrie à un Event Hub. Si tous les appareils se connectent 12 heures par jour, les frais de connexion suivants s’appliquent (en plus des frais liés à une autre rubrique Service Bus) : 10 000 connexions * 12 heures * 31 jours / 744 = 5 000 connexions réparties. Après le volume mensuel de 1 000 connexions réparties, vous serez facturé pour les 4 000 connexions réparties, au tarif de 0,03 $ par connexion répartie, pour un total de 120 $.

2. 10 000 appareils reçoivent des messages d'une file d'attente Service Bus via HTTP, en spécifiant un délai d'expiration différent de zéro. Si tous les appareils se connectent 12 heures par jour, les frais de connexion suivants s’appliquent (en plus des autres frais liés à Service Bus) : 10 000 connexions de réception HTTP * 12 heures par jour * 31 jours / 744 heures = 5 000 connexions réparties.

### Des frais de connexion répartie s’appliquent-ils aux files d’attente et rubriques/abonnements ?

Oui. Il n'y a aucun frais de connexion pour l'envoi d'événements à l'aide de HTTP, quel que soit le nombre de systèmes ou de périphériques d’envoi. La réception d'événements avec le protocole HTTP et à l'aide d'un délai d'expiration supérieur à zéro, parfois appelé « interrogation longue », génère des frais de connexion répartie. Les connexions AMQP génèrent des frais de connexion répartie peu importe si les connexions sont utilisées pour l'envoi ou pour la réception. Notez que 100 connexions réparties sont autorisées gratuitement dans un espace de noms de base. Il s’agit également du nombre maximal de connexions réparties autorisées pour l’abonnement Azure. Les 1 000 premières connexions réparties entre tous les espaces de noms standard d’un abonnement Azure sont incluses sans frais supplémentaires (hors frais de base). Étant donné que ces volumes couvrent de nombreux scénarios de messagerie de service à service, des frais de connexion répartie sont habituellement pertinents si vous prévoyez d'utiliser l'interrogation longue AMQP ou HTTP avec un grand nombre de clients. Par exemple, pour obtenir un flux d'événements plus efficace ou permettre une communication bidirectionnelle avec de nombreux appareils ou instances d'application.

## Étapes suivantes

- Pour plus d’informations sur la tarification Service Bus, consultez la page [sur la tarification de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

- Pour plus d’informations sur Service Bus, consultez la section [FAQ sur la tarification et la facturation de Service Bus](../service-bus-messaging/service-bus-faq.md#service-bus-pricing).

[portail Azure Classic]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0928_2016-->