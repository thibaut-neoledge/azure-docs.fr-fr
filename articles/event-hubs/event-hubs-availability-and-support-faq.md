<properties 
   pageTitle="Disponibilité et support des hubs d’événements | Microsoft Azure"
   description="Forum Aux Questions sur la disponibilité et le support des hubs d’événements."
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="sethm" />

# Forum Aux Questions sur la disponibilité et le support des hubs d’événements

Les hubs d'événements permettent l’admission , la persistance et le traitement à grande échelle de données d’événements provenant de sources de données à débit élevé et/ou de millions d'appareils. Combinés aux files d’attente et aux rubriques Service Bus, les hubs d'événements offrent des déploiements persistants de commande et de contrôle des scénarios Internet des objets.

Cet article traite de la disponibilité et répond à certaines questions fréquemment posées :

## Informations sur la tarification

Pour des informations complètes sur la tarification des hubs d’événements, consultez la rubrique [Tarification des hubs d’événements](https://azure.microsoft.com/pricing/details/event-hubs/).

## Comment les événements d'entrée des hubs d'événements sont-ils calculés ?

Chaque événement envoyé à un hub d'événements est considéré comme un message facturable. Un *événement d'entrée* est défini comme une unité de données inférieure ou égale à 64 Ko. Tout événement dont la taille est inférieure ou égale à 64 Ko est considéré comme un événement facturable. Si l'événement est supérieur à 64 Ko, le nombre d'événements facturables est calculé en fonction de la taille de l'événement, par multiples de 64 Ko. Par exemple, un événement de 8 Ko envoyé au hub d'événements est facturé comme un événement, mais un message de 96 Ko envoyé au hub d'événements est facturé comme deux événements.

Les événements utilisés à partir d'un hub d'événements, ainsi que les opérations de gestion et les appels de contrôle comme les points de contrôle, ne sont pas comptabilisés comme des événements d'entrée facturables, mais s’ajoutent à l'allocation d'unité de débit.

## Que sont les unités de débit des hubs d'événements ?

Les unités de débit des Event Hubs sont explicitement sélectionnées par l’utilisateur via le portail Azure Classic ou les API de gestion Event Hub. Les unités de débit s'appliquent à tous les hubs d'événements dans un espace de noms Service Bus, et chaque unité de débit fournit à l'espace de noms les fonctionnalités suivantes :

- Jusqu’à 1 Mo par seconde d'événements d'entrée (événements envoyés à un hub d'événements), mais pas plus de 1 000 événements d'entrée, opérations de gestion ou appels d’API de contrôle par seconde.

- Jusqu'à 2 Mo par seconde d'événements de sortie (événements utilisés à partir d'un hub d'événements).

- Jusqu'à 84 Go de stockage d'événements (suffisant pour la période de rétention de 24 heures par défaut).

Les unités de débit des hubs d'événements sont facturées par heure, en fonction du nombre maximal d'unités sélectionnées pendant l'heure donnée.

## Comment les limites des unités de débit des hubs d'événements sont-elles appliquées ?

Si le débit d'entrée total ou le taux d'entrée total de tous les hubs d'événements d’un espace de noms dépasse les allocations d'unité de débit cumulées, les expéditeurs sont limités et reçoivent des erreurs indiquant que le quota d'entrée a été dépassé.

Si le débit de sortie total ou le taux de sortie total de tous les hubs d'événements d’un espace de noms dépasse les allocations d'unité de débit cumulées, les récepteurs sont limités et reçoivent des erreurs indiquant que le quota de sortie a été dépassé. Les quotas d'entrée et de sortie sont appliqués séparément afin qu'aucun expéditeur n’entraîne un ralentissement de la consommation d'événements, ni qu’un récepteur n’empêche l’envoi d'événements à un hub d'événements.

Notez que la sélection des unités de débit est indépendante du nombre de partitions des hubs d'événements. Bien que chaque partition offre un débit maximal de 1 Mo par seconde en entrée (avec un maximum de 1 000 événements par seconde) et 2 Mo par seconde en sortie, il n'existe aucun coût fixe pour les partitions elles-mêmes. Les frais s’appliquent aux unités de débit agrégées sur tous les hubs d'événements d’un espace de noms Service Bus. Avec ce modèle, vous pouvez créer suffisamment de partitions pour prendre en charge la charge maximale anticipée de leurs systèmes, sans encourir de frais d'unité de débit jusqu'à ce que la charge des événements sur le système nécessite réellement des valeurs de débit plus élevées, sans avoir à modifier la structure et l'architecture de vos systèmes à mesure que la charge sur le système augmente.

## Y a-t-il une limite au nombre d'unités de débit qui peuvent être sélectionnées ?

Par défaut, il existe un quota de 20 unités de débit par espace de noms. Vous pouvez demander un quota d'unités de débit supérieur en soumettant un ticket de support. Au-delà de la limite de 20 unités de débit, des regroupements 20 et 100 unités de débit sont disponibles. Notez que l'utilisation de plus de 20 unités de débit empêche toute modification du nombre d'unités de débit sans soumission d’un ticket de support.

## Existe-t-il des frais pour la rétention de plus de 24 heures des événements de hubs d'événements ?

Le niveau Standard des hubs d’événements permet de conserver les messages au-delà de 24 heures, avec un maximum de 30 jours. Si la taille de la quantité totale d'événements stockés dépasse l'allocation de stockage pour le nombre d'unités de débit sélectionnées (84 Go par unité de débit), la taille excédentaire est facturée au tarif de stockage Blob Azure publié. L'allocation de stockage dans chaque unité de débit couvre tous les frais de stockage pour les périodes de rétention de 24 heures (par défaut), même si l'unité de débit est utilisée pour l'allocation d'entrée maximale.

## Quelle est la période de rétention maximale ?

Le niveau Standard des hubs d'événements prend actuellement en charge une période de rétention maximale de 7 jours. Notez que les hubs d'événements ne sont pas destinés à servir de magasin de données permanent. Les périodes de rétention supérieures à 24 heures sont destinées aux scénarios dans lesquels il est utile de pouvoir reproduire un flux d'événements sur les mêmes systèmes ; par exemple, pour tester ou vérifier un nouveau modèle d’apprentissage de machine sur des données existantes.

## Comment la taille de stockage des hubs d'événements est-elle calculée et facturée ?

La taille totale de tous les événements stockés, y compris de toute surcharge interne pour les en-têtes d'événements ou les structures de stockage sur disque dans tous les hubs d'événements, est mesurée tout au long de la journée. À la fin de la journée, la taille maximale de stockage est calculée. L'allocation de stockage quotidienne est calculée en fonction du nombre minimal d'unités de débit qui ont été sélectionnées pendant la journée (chaque unité de débit fournit une allocation de 84 Go). Si la taille totale dépasse l'allocation de stockage quotidienne calculée, l'excédent de stockage est facturé selon les taux de stockage Azure Blob (au taux **Stockage localement redondant**).

## Puis-je utiliser une connexion AMQP unique pour l’envoi et la réception à partir de hubs d’événements et de files d'attente/rubriques Service Bus ?

Oui, à condition que la totalité des hubs d'événements, files d'attente et rubriques se trouve dans le même espace de noms Service Bus. Par conséquent, vous pouvez implémenter une connectivité bidirectionnelle et répartie vers de nombreux appareils, avec des latences inférieures à la seconde, de façon économique et hautement évolutive.

## Des frais de connexion répartie s'appliquent-ils aux hubs d'événements ?

Pour les expéditeurs, des frais de connexion s'appliquent uniquement lorsque le protocole AMQP est utilisé. Il n'y a aucun frais de connexion pour l'envoi d'événements à l'aide de HTTP, quel que soit le nombre de systèmes ou d’appareils d’envoi. Si vous prévoyez d'utiliser AMQP (par exemple, pour améliorer le flux d'événements ou activer la communication bidirectionnelle sur des scénarios de commande et de contrôle Internet des objets), reportez-vous à la page [Informations de tarification Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) pour savoir ce qui constitue une connexion répartie et comment elle est mesurée.

## Quelle est la différence entre les niveaux De Base et Standard pour les hubs d’événements ?

Le niveau Standard des hubs d'événements fournit des fonctionnalités au-delà de ce qui est disponible au niveau De base, ainsi que dans certains systèmes concurrents. Ces fonctionnalités incluent des périodes de rétention de plus de 24 heures et la possibilité d'utiliser une connexion AMQP unique pour envoyer des commandes à un grand nombre d’appareils avec des latences de moins d’une seconde, ainsi que pour envoyer la télémétrie de ces appareils vers les hubs d'événements. Pour consulter la liste des fonctionnalités, voir [Tarification des concentrateurs d’événements](https://azure.microsoft.com/pricing/details/event-hubs/).

## Disponibilité géographique

La fonctionnalité Hubs d’événements est disponible dans les régions suivantes :

|Zone géographique|Régions|
|---|---|
|États-Unis|Centre des États-Unis, est des États-Unis, est des États-Unis 2, centre sud des États-Unis, ouest des États-Unis|
|Europe|Europe du Nord, Europe de l’Ouest|
|Asie-Pacifique|Asie orientale, Asie du Sud-Est|
|Japon|Est du Japon, ouest du Japon|
|Brésil|Sud du Brésil|
|Australie|Est de l’Australie, sud-est de l’Australie|

## Prise en charge et contrats SLA

Un support technique pour les hubs d'événements est disponible via les [forums de la communauté](https://social.msdn.microsoft.com/forums/azure/home). La gestion de la facturation et des abonnements est fournie gratuitement.

Pour en savoir plus sur notre contrat SLA, rendez-vous sur la page [Contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/).

## Étapes suivantes

Pour plus d’informations sur les hubs d’événements, consultez les articles suivants :

- [Vue d’ensemble des hubs d’événements].
- Un [exemple d'application complet qui utilise des hubs d’événements].
- Une [solution de messages de file d'attente] utilisant les files d'attente Service Bus.

[Vue d’ensemble des hubs d’événements]: event-hubs-overview.md
[exemple d'application complet qui utilise des hubs d’événements]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[solution de messages de file d'attente]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

<!---HONumber=AcomDC_0128_2016-->