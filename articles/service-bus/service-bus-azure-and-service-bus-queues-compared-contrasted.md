<properties 
   pageTitle="Files d'attente Azure et files d'attente Service Bus : comparaison et différences"
   description="Analyse les différences et les similitudes entre les deux types de files d'attente proposés par Azure."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/11/2015"
   ms.author="sethm" />

# Files d'attente Azure et files d'attente Service Bus : comparaison et différences

Cet article analyse les différences et les ressemblances entre deux types de files d’attente offertes aujourd’hui par Microsoft Azure : files d’attente Azure et Service Bus. À l'aide de ces informations, vous pouvez comparer les technologies respectives et être en mesure de prendre une décision éclairée concernant la solution adaptée à vos besoins.

## Introduction

Microsoft Azure prend en charge deux types de mécanismes de file d'attente : **files d'attente Azure** et **files d'attente Service Bus**.

Les **files d'attente Azure**, qui font partie de l'infrastructure de [stockage Azure](http://azure.microsoft.com/services/storage/), inclut une simple interface Get/Put/Peek basée sur REST, fournissant une messagerie fiable et persistante au sein des services et entre les différents services.

Les **files d'attente Service Bus** font partie d'une infrastructure de [messagerie Azure](http://azure.microsoft.com/services/messaging/) plus large prenant en charge la mise en file d'attente, ainsi que la publication/l'abonnement, l'accès distant au service Web et les modèles d'intégration. Pour plus d'informations sur les files d'attente Service Bus, les rubriques/abonnements et les relais, consultez [Présentation des modèles de messagerie Service Bus](https://msdn.microsoft.com/library/hh410103.aspx).

Bien que les deux technologies de file d'attente coexistent, les files d'attente Azure ont été introduites en premier, en tant que mécanisme de stockage de file d'attente dédié basé sur les services de stockage Azure. Les files d’attente Service Bus sont basées sur l’infrastructure de « messagerie répartie » plus large conçue pour intégrer des applications ou des composants d’applications qui peuvent s’étendre sur plusieurs protocoles de communication, contrats de données, domaines de confiance et/ou environnements réseau.

Cet article compare les deux technologies de file d'attente proposées par Azure en traitant des différences de comportement et de l'implémentation des fonctionnalités offertes par chacune. L'article vous fournit également des conseils pour choisir les fonctionnalités qui correspondent le mieux à vos besoins de développement d'application.

## Considérations relatives à la sélection de la technologie

Les files d'attente Azure et Service Bus sont des implémentations du service de mise en file d'attente des messages actuellement proposé avec Microsoft Azure. Chaque technologie possède un ensemble de fonctionnalités légèrement différent, ce qui signifie que vous pouvez choisir l'une ou l'autre, ou utiliser les deux, selon les besoins de votre solution spécifique ou du problème d'entreprise/technique à résoudre.

Pour déterminer quelle technologie de file d'attente est adaptée à une solution donnée, les développeurs et les architectes de solutions doivent prendre en compte les recommandations ci-dessous. Pour plus d'informations, consultez la section suivante.

En tant que développeur/architecte de solutions, **vous devez envisager d'utiliser les files d'attente Azure** lorsque :

- Votre application doit stocker plus de 80 Go de messages dans une file d'attente, où les messages ont une durée de vie inférieure à 7 jours.

- Votre application veut suivre la progression du traitement d'un message dans la file d'attente. Cela est utile si le processus traitant un message se bloque. Un processus suivant peut alors utiliser ces informations pour continuer là où le processus précédent s'était arrêté.

- Vous avez besoin de journaux côté serveur de toutes les transactions exécutées sur les files d'attente.

En tant que développeur/architecte de solutions, **vous devez envisager d'utiliser les files d'attente Service Bus** lorsque :

- Votre solution doit être en mesure de recevoir des messages sans devoir interroger la file d'attente. Avec Service Bus, ceci peut être réalisé avec l'utilisation de l'opération de réception à interrogation longue à l'aide des protocoles TCP pris en charge par Service Bus.

- Votre solution nécessite la file d'attente pour fournir une livraison organisée selon la méthode Premier entré, premier sortie.

- Vous souhaitez créer une expérience symétrique dans Azure et Windows Server (cloud privé). Pour plus d'informations, consultez [Service Bus pour Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).

- Votre solution doit pouvoir prendre en charge la détection automatique des doublons.

- Vous voulez que votre application traite les messages sous forme de flux de longue durée parallèles (les messages sont associés à un flux à l'aide de la propriété [SessionId](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) du message). Dans ce modèle, chaque nœud de l'application consommatrice entre en concurrence pour les flux, contrairement aux messages. Lorsqu'un flux est donné à un nœud consommateur, le nœud peut examiner l'état du flux de l'application à l'aide de transactions.

- Votre solution nécessite un comportement transactionnel et l'atomicité lors de l'envoi ou de la réception de plusieurs messages à partir d'une file d'attente.

- La caractéristique de durée de vie (TTL) de la charge de travail spécifique à l'application peut dépasser la période de 7 jours.

- Votre application gère des messages qui peuvent dépasser 64 Ko, mais qui n'atteindront sans doute pas la limite de 256 Ko.

- Vous êtes confronté à l'exigence de fournir un modèle d'accès basé sur les rôles aux files d'attente et des droits/autorisations différents pour les expéditeurs et les destinataires.

- La taille de la file d'attente ne sera pas supérieure à 80 Go.

- Vous souhaitez utiliser le service Broker pour les messages basé sur les normes AMQP 1.0. Pour plus d'informations sur AMQP, consultez [Présentation d'AMQP Service Bus](https://msdn.microsoft.com/library/jj841072.aspx).

- Vous pouvez prévoir une migration éventuelle de la communication point à point basée sur la file d'attente vers un modèle d'échange de messages qui permet une intégration transparente de récepteurs (abonnés) supplémentaires, chacun d'entre eux recevant des copies indépendantes de certains ou de tous les messages envoyés à la file d'attente. Ce dernier point fait référence à la fonctionnalité publication/abonnement en mode natif fournie par Service Bus.

- Votre solution de messagerie doit être en mesure de prendre en charge la garantie de remise "Une fois au maximum" sans que vous ayez besoin de créer des composants d'infrastructure supplémentaires.

- Vous souhaitez être en mesure de publier et d'utiliser des lots de messages.

- Vous avez besoin d'une intégration complète avec la pile de communication de Windows Communication Foundation (WCF) dans .NET Framework.

## Comparaison des files d'attente Azure et des files d'attente Service Bus

Les tables dans les sections suivantes fournissent un regroupement logique des fonctionnalités de file d'attente et vous permettent de comparer, en un coup d'œil, les fonctionnalités disponibles dans les files d'attente Azure et les files d'attente Service Bus.

## Fonctions de base

Cette section compare certaines des fonctionnalités de base fournies par les files d'attente Azure et les files d'attente Service Bus.

|Critères de comparaison|Files d'attente Azure|Files d'attente Service Bus|
|---|---|---|
|Garantie de classement|**Non** <br/><br>Pour plus d'informations, consultez la première remarque dans la section "Informations supplémentaires".</br>|**Oui - Méthode Premier entré, premier sorti (FIFO)**<br/><br>(par le biais de l'utilisation de sessions de messagerie)|
|Garantie de livraison|**Au moins une fois**|**Au moins une fois**<br/><br/>**Une fois au maximum**|
|Prise en charge des transactions|**Non**|**Oui**<br/><br/>(par le biais de l'utilisation de transactions locales)|
|Comportement de réception|**Non bloquant**<br/><br/>(se termine immédiatement si aucun nouveau message n'est trouvé)|**Blocage avec ou sans délai d'attente**<br/><br/>(offre une interrogation longue ou la ["technique Comet"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>** Non bloquant**<br/><br/>(par le biais d'une API gérée par .NET uniquement)|
|API style Push|**Non**|**Oui**<br/><br/> API .NET [OnMessage](https://msdn.microsoft.com/library/jj908682.aspx) et [sessions OnMessage](https://msdn.microsoft.com/library/dn790528.aspx).|
|Mode de réception|**Aperçu et attribution**|**Aperçu et verrouillage**<br/><br/>**Réception et suppression**|
|Mode d'accès exclusif|**Basé sur attribution**|**Basé sur verrouillage**|
|Durée attribution/verrouillage|**30 secondes (valeur par défaut)**<br/><br/>**7 jours (maximum)** (Vous pouvez renouveler ou libérer l'attribution d'un message à l'aide de l'API [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx).)|**60 secondes (valeur par défaut)**<br/><br/>Vous pouvez renouveler le verrouillage d'un message à l'aide de l'API [RenewLock](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx).|
|Précision attribution/verrouillage|**Au niveau du message**<br/><br/>(chaque message peut avoir une valeur de délai d'attente différente, que vous pouvez ensuite mettre à jour en fonction des besoins lors du traitement du message, à l'aide de l'API [UpdateMessage](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx))|**Au niveau de la file d'attente**<br/><br/>(pour chaque file d'attente, une précision de verrouillage est appliquée à tous les messages, mais vous pouvez renouveler le verrouillage à l'aide de l'API [RenewLock](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx).)|
|Réception par lots|**Oui**<br/><br/>(spécifiant explicitement le nombre de messages lors de la récupération des messages, avec un maximum de 32 messages)|**Oui**<br/><br/>(activant implicitement une propriété de pré-récupération ou explicitement par le biais de l'utilisation de transactions)|
|Envoi par lots|**Non**|**Oui**<br/><br/>(par le biais de l'utilisation de transactions ou du traitement par lots côté client)|

### informations supplémentaires

- Les messages dans les files d'attente Azure se voient en général appliquer la méthode Premier entré, premier sorti. Mais ils peuvent parfois être dans le désordre. Par exemple, lorsque le délai de visibilité d'un message expire (par exemple, à cause du blocage d'une application cliente pendant le traitement). Lorsque le délai de visibilité expire, le message est de nouveau visible dans la file d'attente et un autre processus peut le retirer de la file d'attente. À ce stade, le message nouvellement visible peut être placé dans la file d'attente (pour en être de nouveau retiré) après un message qui se trouvait à l'origine dans la file d'attente après lui.

- Si vous utilisez déjà des Tables ou des objets BLOB de stockage Azure et que vous commencez à utiliser les files d'attente, vous êtes assuré d'une disponibilité de 99,9 %. Si vous utilisez des Tables ou des objets BLOB de stockage avec des files d'attente Service Bus, la disponibilité sera inférieure.

- Le modèle Premier entré, premier sorti garanti dans les files d'attente Service Bus requiert l'utilisation de sessions de messagerie. Dans le cas où l'application se bloque lors du traitement d'un message reçu en mode **Aperçu et verrouillage**, la prochaine fois qu'un destinataire de file d'attente acceptera une session de messagerie, celle-ci démarrera avec le message ayant échoué après que sa durée de vie ait expiré.

- Les files d'attente Azure sont conçues pour prendre en charge des scénarios de files d'attente standard, tels que le découplage des composants d'application pour augmenter l'évolutivité et la tolérance aux pannes, le nivellement de charge et la création des workflows de processus.

- Les files d'attente Service Bus prennent en charge la garantie de livraison *Au moins une fois*. En outre, la sémantique *Une fois au maximum* peut être prise en charge à l'aide de l'état de session pour stocker l'état de l'application et à l'aide de transactions pour recevoir atomiquement des messages et mettre à jour l'état de la session. Le service de workflow Azure utilise cette technique pour garantir une livraison Une fois au maximum.

- Les files d'attente Azure fournissent un modèle de programmation uniforme et cohérent entre les files d'attente, les tables et les objets BLOB, pour les développeurs et les équipes d'exploitation.

- Les files d'attente Service Bus prennent en charge les transactions locales dans le contexte d'une file d'attente unique.

- Le mode *Réception et suppression* pris en charge par Service Bus offre la possibilité de réduire le nombre d'opérations de messagerie (et le coût associé) en échange d'une garantie de livraison réduite.

- Les files d'attente Azure fournissent des attributions avec la possibilité d'étendre les attributions pour les messages. Ainsi, les processus peuvent maintenir des attributions de courte durée pour les messages. Par conséquent, si un processus se bloque, le message peut être rapidement traité à nouveau par un autre processus. En outre, un processus peut étendre l'attribution d'un message s'il doit le traiter pendant une période plus longue que la durée de l'attribution.

- Les files d'attente Azure offrent un délai de visibilité que vous pouvez définir lors de la mise en file d'attente ou du retrait de la file d'attente d'un message. En outre, vous pouvez mettre à jour un message avec des valeurs d'attribution différentes lors de l'exécution et mettre à jour des valeurs différentes entre des messages dans la même file d'attente. Les délais de verrouillage Service Bus sont définis dans les métadonnées de la file d'attente. Toutefois, vous pouvez renouveler le verrouillage en appelant la méthode [RenewLock](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx).

- Le délai d'attente maximal pour une opération de réception de blocage dans les files d'attente Service Bus est de 24 jours. Toutefois, les délais d'attente basés sur REST ont une valeur maximale de 55 secondes.

- Le traitement par lots côté client fourni par Service Bus permet à un client de file d'attente de traiter par lots plusieurs messages dans une seule opération d'envoi. Le traitement par lots n'est disponible que pour les opérations d'envoi asynchrones.

- Certaines fonctionnalités telles que la limite de 200 To pour les files d'attente Azure (davantage lorsque vous virtualisez des comptes) et un nombre illimité de files d'attente en font une plateforme idéale pour les fournisseurs SaaS.

- Les files d'attente Azure fournissent un mécanisme de contrôle d'accès délégué performant et flexible.

## Fonctionnalités avancées

Cette section compare les fonctionnalités avancées fournies par les files d'attente Azure et les files d'attente Service Bus.

|Critères de comparaison|Files d'attente Azure|Files d'attente Service Bus|
|---|---|---|
|Remise planifiée|**Oui**|**Oui**|
|Lettre morte automatique|**Non**|**Oui**|
|Augmenter la valeur de durée de vie de la file d'attente|**Oui**<br/><br/>(par le biais de la mise à jour sur place du délai de visibilité)|**Oui**<br/><br/>(par le biais d'une fonction API dédiée)|
|Prise en charge des messages incohérents|**Oui**|**Oui**|
|Mise à jour sur place|**Oui**|**Oui**|
|Journal des transactions côté serveur|**Oui**|**Non**|
|Métriques de stockage|**Oui**<br/><br/>**Métriques par minute** : fournit des métriques en temps réel pour la disponibilité, TPS, nombre d'appels API, nombre d'erreurs, etc., le tout en temps réel (métriques agrégées par minute et consignées en l'espace de quelques minutes à partir de ce qui vient de se passer en production). Pour plus d'informations, consultez la page [À propos des mesures Storage Analytics](https://msdn.microsoft.com/library/hh343258.aspx).|**Oui**<br/><br/>(requêtes en bloc en appelant [GetQueues](https://msdn.microsoft.com/library/hh293128.aspx))|
|Gestion de l'état|**Non**|**Oui**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx)|
|Transfert automatique des messages|**Non**|**Oui**|
|Fonction de purge de la file d'attente|**Oui**|**Non**|
|Groupes de messages|**Non**|**Oui**<br/><br/>(par le biais de l'utilisation de sessions de messagerie)|
|État de l'application par groupe de messages|**Non**|**Oui**|
|Détection des doublons|**Non**|**Oui**<br/><br/>(configurable du côté expéditeur)|
|Intégration WCF|**Non**|**Oui**<br/><br/>(offre des liaisons WCF prêtes à l'emploi)|
|Intégration WF|**Personnalisée**<br/><br/>(nécessite la création d'une activité WF personnalisée)|**Native**<br/><br/>(offre des activités WF prêtes à l'emploi)|
|Consultation des groupes de messages|**Non**|**Oui**|
|Récupération des sessions de messagerie par ID|**Non**|**Oui**|

### informations supplémentaires

- Les deux technologies de mise en file d'attente permettent de planifier la remise d'un message à une date ultérieure.

- Le transfert automatique de file d'attente permet à des milliers de files d'attente de transférer automatiquement leurs messages à une seule file d'attente, à partir de laquelle l'application réceptrice consomme le message. Vous pouvez utiliser ce mécanisme pour garantir la sécurité, contrôler le flux et isoler le stockage entre chaque serveur de publication de message.

- Les files d'attente Azure prennent en charge la mise à jour du contenu du message. Vous pouvez utiliser cette fonctionnalité pour les informations d'état persistantes et les mises à jour incrémentielles de progression dans le message, afin que celui-ci puisse être traité à partir du dernier point de contrôle connu, au lieu de recommencer depuis le début. Avec les files d'attente Service Bus, vous pouvez activer le même scénario à l'aide des sessions de messagerie. Les sessions vous permettent d'enregistrer et de récupérer l'état de traitement de l'application (à l'aide de [SetState](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) et [GetState](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx)).

- Le concept de Lettre morte, qui est uniquement pris en charge par les files d'attente Service Bus, peut être utile pour isoler les messages qui ne peuvent pas être traités correctement par l'application réceptrice ou lorsque les messages n'atteignent pas leur destination en raison d'une propriété de durée de vie (TTL) expirée. La valeur de durée de vie spécifie la durée pendant laquelle un message reste dans la file d'attente. Avec Service Bus, le message est déplacé vers une file d'attente spéciale appelée $DeadLetterQueue lorsque la durée de vie expire.

- Pour trouver les messages « incohérents » dans les files d'attente Azure, lors du retrait de la file d'attente d'un message l'application examine la propriété DequeueCount du message. Si la propriété DequeueCount dépasse un seuil donné, l'application déplace le message vers une file d'attente de type « lettre morte » définie par l'application.

- Les files d'attente Azure vous permettent d'obtenir un journal détaillé de toutes les transactions exécutées sur la file d'attente, ainsi que des métriques agrégées. Ces deux options sont utiles pour déboguer et comprendre comment votre application utilise les files d'attente Azure. Elles sont également utiles pour régler les performances de votre application et réduire les coûts d'utilisation des files d'attente.

- Le concept de « sessions de messagerie » pris en charge par Service Bus permet aux messages appartenant à un certain groupe logique d'être associés à un destinataire spécifique, ce qui ensuite crée une affinité de type session entre les messages et leurs récepteurs respectifs. Vous pouvez activer cette fonctionnalité avancée dans Service Bus en définissant la propriété [SessionID](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) d'un message. Les récepteurs peuvent ensuite écouter par le biais d'un ID de session spécifique et recevoir les messages qui partagent l'identificateur de session spécifié.

- La fonctionnalité de détection des doublons prise en charge par les files d'attente Service Bus supprime automatiquement les messages en double envoyés à une file d'attente ou une rubrique, selon la valeur de la propriété [MessageID](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx).

## Capacité et quotas

Cette section compare les files d'attente Azure et les files d'attente Service Bus du point de vue de la capacité et des quotas qui peuvent s'appliquer.

|Critères de comparaison|Files d'attente Azure|Files d'attente Service Bus|
|---|---|---|
|Taille de file d'attente maximale|**200 To**<br/><br/>(limitée à une capacité de compte de stockage unique)|**1 Go à 80 Go**<br/><br/>(définie lors de la création d'une file d'attente et [activation du partitionnement](https://msdn.microsoft.com/library/dn520246.aspx) – consultez la section « Informations supplémentaires »)|
|Taille de message maximale|**64 Ko**<br/><br/>(48 Ko lors de l'utilisation du codage **Base64**)<br/><br/>Azure prend en charge les messages volumineux en combinant des files d'attente et des objets BLOB. Dans ce cas, vous pouvez placer jusqu'à 200 Go en file d'attente pour un seul élément.|**256 Ko**<br/><br/>(y compris l'en-tête et le corps, taille maximale d'en-tête : 64 Ko)|
|Durée de vie maximale des messages|**7 jours**|**Illimitée**|
|Nombre maximal de files d'attente|**Illimité**|**10 000**<br/><br/>(par espace de noms de service, peut être augmenté)|
|Nombre maximal de clients simultanés|**Illimité**|**Illimité**<br/><br/>(la limite de 100 connexions simultanées s'applique uniquement à la communication basée sur le protocole TCP)|

### Informations supplémentaires

- Service Bus applique les limites en termes de taille de file d'attente. La taille de file d'attente maximale est spécifiée lors de la création de la file d'attente et peut avoir une valeur comprise entre 1 Go et 80 Go. Si la valeur de taille de la file d'attente définie lors de la création de celle-ci est atteinte, les messages entrants supplémentaires seront rejetés et une exception sera reçue par le code appelant. Pour plus d'informations sur les quotas dans Service Bus, consultez [Quotas Service Bus](https://msdn.microsoft.com/library/ee732538.aspx).

- Vous pouvez créer des files d'attente Service Bus avec des tailles de 1, 2, 3, 4 ou 5 Go (la valeur par défaut est 1 Go). Avec le partitionnement activé (qui est la valeur par défaut), Service Bus crée 16 partitions pour chaque Go que vous spécifiez. Par conséquent, si vous créez une file d'attente de 5 Go, avec 16 partitions la taille maximale de la file d'attente est (5 * 16) = 80 Go. Vous pouvez voir la taille maximale de votre file d'attente ou rubrique partitionnée en examinant son entrée sur le portail de gestion Azure.

- Avec les files d'attente Azure, si le contenu du message n'est pas sécurisé pour XML, il doit être encodé au format **Base64**. Si vous encodez le message au format **Base64**, la charge utilisateur peut atteindre 48 Ko, au lieu de 64 Ko.

- Avec les files d'attente Service Bus, chaque message stocké dans une file d'attente est composé de deux parties : un en-tête et un corps. La taille totale du message ne peut pas dépasser 256 Ko.

- Lorsque des clients communiquent avec des files d'attente Service Bus au moyen du protocole TCP, le nombre maximal de connexions simultanées à une file d'attente Service Bus unique est limité à 100. Ce nombre est partagé entre les expéditeurs et les destinataires. Si ce quota est atteint, les requêtes suivantes pour des connexions supplémentaires sont rejetées et une exception sera reçue par le code appelant. Cette limite n'est pas appliquée aux clients qui se connectent aux files d'attente à l'aide d'une API REST.

- Si vous avez besoin de plus de 10 000 files d'attente dans un seul espace de noms Service Bus, vous pouvez contacter l'équipe de support Azure et demander l'augmentation du nombre de files d'attente. Pour aller au-delà de 10 000 files d'attente avec Service Bus, vous pouvez également créer des espaces de noms supplémentaires à l'aide du portail de gestion Azure.

## Gestion et opérations

Cette section compare les fonctionnalités de gestion fournies par les files d'attente Azure et les files d'attente Service Bus.

|Critères de comparaison|Files d'attente Azure|Files d'attente Service Bus|
|---|---|---|
|Protocole de gestion|**REST sur HTTP/HTTPS**|**REST sur HTTPS**|
|Protocole d'exécution|**REST sur HTTP/HTTPS**|**REST sur HTTPS**<br/><br/>**Norme AMQP 1.0 (TCP avec TLS)**| |API gérée par .NET|**Oui**<br/><br/>(API de client de stockage gérée par .NET)|**Oui**<br/><br/>(API de messagerie répartie gérée par .NET)|
|C++ natif|**Oui**|**Non**|
|API Java|**Oui**|**Oui**|
|API PHP|**Oui**|**Oui**|
|API Node.js|**Oui**|**Oui**|
|Prise en charge des métadonnées arbitraires|**Oui**|**Non**|
|Règles d'affectation des noms aux files d'attente|**Jusqu'à 63 caractères**<br/><br/>(les lettres dans un nom de file d'attente doivent être en minuscules)|**Jusqu'à 260 caractères**<br/><br/>(les noms des files d'attente ne respectent pas la casse)|
|Fonction d'obtention de la longueur de la file d'attente|**Oui**<br/><br/>(valeur approximative si les messages expirent au-delà de la durée de vie sans être supprimés)|**Oui**<br/><br/>(valeur exacte, à un moment donné)|
|Fonction Peek (aperçu)|**Oui**|**Oui**|

### Informations supplémentaires

- Les files d'attente Azure prennent en charge les attributs arbitraires qui peuvent s'appliquer à la description de la file d'attente, sous la forme de paires nom/valeur.

- Les deux technologies de file d'attente offrent la possibilité d'obtenir un aperçu d'un message sans avoir à le verrouiller, ce qui peut être utile lors de la mise en œuvre d'un outil de type explorateur/navigateur de file d'attente.

- Les API de messagerie répartie .NET de Service Bus exploitent les connexions TCP en duplex intégral pour améliorer les performances par rapport au protocole REST sur HTTP, et elles prennent en charge le protocole standard AMQP 1.0.

- Les noms des files d'attente Azure peuvent compter entre 3 et 63 caractères, ils peuvent contenir des lettres minuscules, des nombres et des traits d'union. Pour plus d'informations, consultez [Affectation de noms pour les files d'attente et les métadonnées](https://msdn.microsoft.com/library/dd179349.aspx).

- Les noms des files d'attente Service Bus peuvent compter jusqu'à 260 caractères. Les règles d'affectation de noms sont moins restrictives. Les noms des files d'attente Service Bus peuvent contenir des lettres, des nombres, des points (.), des traits d'union (-) et des traits de soulignement (\_).

## Performances

Cette section compare les files d'attente Azure et les files d'attente Service Bus du point de vue des performances.

|Critères de comparaison|Files d'attente Azure|Files d'attente Service Bus|
|---|---|---|
|Débit maximal|**Jusqu'à 2 000 messages par seconde**<br/><br/>(en fonction du test d'évaluation avec des messages de 1 Ko)|**Jusqu'à 2 000 messages par seconde**<br/><br/>(en fonction du test d'évaluation avec des messages de 1 Ko)|
|Latence moyenne|**10 ms**<br/><br/>(avec [TCP Nagle](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx) désactivé)|**20-25 ms**|
|Comportement de limitation|**Rejet avec code HTTP 503**<br/><br/>(les requêtes limitées ne sont pas traitées comme facturables)|**Rejet avec exception/HTTP 503**<br/><br/>(les requêtes limitées ne sont pas traitées comme facturables)|

### Informations supplémentaires

- Une seule file d'attente Azure peut traiter jusqu'à 2 000 transactions par seconde. Une transaction est une opération **Put**, **Get** ou **Delete**. Le fait d'envoyer un message unique à une file d'attente (**Put**) est considéré comme une seule transaction, mais la réception d'un message correspond souvent à un processus en deux étapes impliquant la récupération (**Get**), suivie d'une requête de suppression du message de la file d'attente (**Delete**). Par conséquent, une opération de suppression de la file d'attente réussie implique généralement deux transactions. La récupération de plusieurs messages dans un lot peut en réduire l'impact, car vous pouvez **récupérer** jusqu'à 32 messages en une seule transaction, puis **supprimer** chacun d'eux. Pour un meilleur débit, vous pouvez créer plusieurs files d'attente (un compte de stockage peut avoir un nombre illimité de files d'attente).

- Lorsque l'application atteint le débit maximal pour une file d'attente Azure, une réponse « Serveur occupé HTTP 503 » est généralement renvoyée à partir du service de file d'attente. Dans ce cas, l'application doit déclencher la logique de nouvelle tentative avec un délai de temporisation exponentiel.

- La latence des files d'attente Azure est de 10 millisecondes en moyenne lors du traitement des messages de petite taille (inférieure à 10 Ko) à partir d'un service hébergé situé dans le même emplacement (région) que le compte de stockage.

- Les files d'attente Azure et les files d'attente Service Bus appliquent le comportement de limitation en rejetant les requêtes pour une file d'attente qui est limitée. Toutefois, aucune de ces files d'attente ne traite les requêtes limitées comme facturables.

- Les tests d'évaluation sur les files d'attente Service Bus ont démontré qu'une seule file d'attente peut atteindre un débit de 2 000 messages par seconde avec une taille de message d'environ 1 Ko. Pour obtenir un débit plus élevé, utilisez plusieurs files d'attente. Pour plus d'informations sur l'optimisation des performances avec Service Bus, consultez [Meilleures pratiques relatives aux améliorations de performances à l'aide de la messagerie répartie Service Bus](https://msdn.microsoft.com/library/hh528527.aspx).

- Lorsque le débit maximal est atteint pour une file d'attente Service Bus, une réponse [ServerBusyException](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx) (avec l'utilisation de l'API de messagerie répartie .NET) ou HTTP 503 (avec l'utilisation de l'API REST) est renvoyée au client de file d'attente, indiquant que la file d'attente est limitée.

## Authentification et autorisation

Cette section traite des fonctionnalités d'authentification et d'autorisation prises en charge par les files d'attente Service Bus et les files d'attente Azure.

|Critères de comparaison|Files d'attente Azure|Files d'attente Service Bus|
|---|---|---|
|Authentification|**Clé symétrique**|**Clé symétrique**|
|Modèle de sécurité|Accès délégué via des jetons SAS.|SAS|
|Fédération de fournisseur d’identité|**Non**|**Oui**|

### Informations supplémentaires

- Chaque requête à l'une des technologies de file d'attente doit être authentifiée. Les files d'attente publiques avec accès anonyme ne sont pas prises en charge. À l'aide de SAS, vous pouvez résoudre ce scénario en publiant un SAS en écriture seule, un SAS en lecture seule ou un SAS à accès total.

- Le schéma d'authentification fourni par les files d'attente Azure implique l'utilisation d'une clé symétrique, qui est un code d'authentification de message basé sur hachage (HMAC), calculée avec l'algorithme SHA-256 et encodée comme une chaîne **Base64**. Pour plus d'informations sur le protocole respectif, consultez [Authentification de l'accès à votre compte de stockage](https://msdn.microsoft.com/library/hh225339.aspx). Les files d'attente Service Bus prennent en charge un modèle similaire utilisant des clés symétriques. Pour plus d'informations, consultez [Authentification par signature d'accès partagé avec Service Bus](https://msdn.microsoft.com/library/dn170477.aspx).

## Coût

Cette section compare les files d'attente Azure et les files d'attente Service Bus du point de vue du coût.

|Critères de comparaison|Files d'attente Azure|Files d'attente Service Bus|
|---|---|---|
|Coût de transaction de file d'attente|**0,0036 $**<br/><br/>(pour 100 000 transactions)|**Niveau de base** : **0,05 $**<br/><br/>(par million d'opérations)|
|Opérations facturables|**Toutes**|**Envoi/réception uniquement**<br/><br/>(aucun frais pour les autres opérations)|
|Transactions inactives|**Facturables**<br/><br/>(l'interrogation d'une file d'attente vide est comptabilisée comme une transaction facturable)|**Facturables**<br/><br/>(une réception sur une file d'attente vide est considérée comme un message facturable)|
|Coût de stockage|**0,07 $**<br/><br/>(par Go/mois)|**0,00 $**|
|Coûts de transfert de données sortantes|**0,12 $ - 0,19 $**<br/><br/>(selon la géographie)|**0,12 $ - 0,19 $**<br/><br/>(selon la géographie)|

### Informations supplémentaires

- Les transferts de données sont facturés en fonction de la quantité totale de données quittant les centres de données Azure par le biais d'Internet pendant une période de facturation donnée.

- Les transferts de données entre des services Azure situés dans la même région ne sont pas soumis à facturation.

- Actuellement, tous les transferts de données entrantes ne sont pas soumis à facturation.

- Étant donné la prise en charge de l'interrogation longue, l'utilisation de files d'attente Service Bus peut être rentable dans les situations où une remise à latence faible est requise.

>[AZURE.NOTE]Tous les coûts sont susceptibles d'être modifiés. Ce tableau reflète la tarification actuelle au moment de la rédaction de cet article et n'inclut pas les offres promotionnelles qui peuvent être actuellement disponibles. Pour obtenir des informations récentes sur la tarification Azure, consultez la page de [tarification Azure](http://azure.microsoft.com/pricing/). Pour plus d’informations sur la tarification Service Bus, consultez [Tarification Service Bus](http://azure.microsoft.com/pricing/details/service-bus/).

## Conclusion

En ayant une meilleure compréhension des deux technologies, vous serez en mesure de prendre une décision plus informée sur les technologies de file d'attente à utiliser et quand. La décision d'utiliser des files d'attente Azure ou des files d'attente Service Bus dépend de plusieurs facteurs. Ces facteurs peuvent reposer largement sur les besoins spécifiques de votre application et de son architecture. Si votre application utilise déjà les principales fonctionnalités de Microsoft Azure, vous voudrez peut-être choisir les files d'attente Azure, surtout si vous avez besoin d'une communication et d'une messagerie de base entre les services ou si vous avez besoin de files d'attente qui peuvent être supérieures à 80 Go.

Étant donné que les files d'attente Service Bus fournissent plusieurs fonctionnalités avancées, comme les sessions, les transactions, la détection de doublons, la lettre morte automatique et des fonctions de publication/d'abonnement durables, elles peuvent constituer un meilleur choix si vous créez une application hybride ou si votre application nécessite ces fonctionnalités.

## Étapes suivantes

Les articles suivants fournissent davantage de conseils et d'informations sur l'utilisation de files d'attente Azure ou Service Bus.

- [Utilisation des files d'attente Service Bus](service-bus-dotnet-how-to-use-queues.md)
- [Utilisation du service de stockage de files d'attente](../storage-dotnet-how-to-use-queues.md)
- [Meilleures pratiques relatives aux améliorations de performances à l'aide de la messagerie répartie Service Bus](https://msdn.microsoft.com/library/hh528527.aspx)
- [Présentation des files d'attente et des rubriques dans Azure Service Bus](http://www.code-magazine.com/article.aspx?quickid=1112041)
- [Guide du développeur pour Service Bus](http://www.cloudcasts.net/devguide/)
- [« Immersion dans les tables et les files d'attente Azure »](http://www.microsoftpdc.com/2009/SVC09)
- [Architecture de stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
- [Utilisation du service de mise en file d'attente dans Azure ](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)
- [Présentation de la facturation du stockage Azure - bande passante, transactions et capacité](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)
 

<!---HONumber=Sept15_HO3-->