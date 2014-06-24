# Prise en charge d'AMQP 1.0 dans Service Bus

> Azure Service Bus et Service Bus pour Windows Server (Service Bus 1.1)
> prennent en charge le protocole AMQP (Advanced Message Queueing
> Protocol) 1.0. Le protocole AMQP vous permet de développer des
> applications hybrides interplateforme à l'aide d'un protocole open
> standard. Vous pouvez générer des applications à l'aide de composants
> créés avec plusieurs langages et infrastructures, exécutés sur
> différents systèmes d'exploitation. Tous ces composants peuvent se
> connecter à Service Bus et échanger efficacement et sans difficulté
> des messages professionnels d'une fidélité optimale.
## Introduction : qu'est-ce qu'AMQP 1.0 ? En quoi ce protocole est-il important ?

Traditionnellement, les produits intergiciels (middleware) utilisent des
protocoles propriétaires pour la communication entre les applications
clientes et les services Broker. Cela signifie qu'une fois que vous avez
sélectionné un service Broker de messagerie d'un fournisseur
particulier, vous devez utiliser les bibliothèques de ce fournisseur
pour connecter vos applications clientes à ce service Broker. Cela
aboutit à un niveau de dépendance vis-à-vis de ce fournisseur, puisque
le portage d'une application vers un produit différent nécessite le
recodage de toutes les applications connectées.

De plus, il est compliqué de connecter les services Broker de messagerie
de différents fournisseurs. Cela requiert en général le pontage au
niveau de l'application pour le déplacement des messages d'un système à
l'autre et pour leur conversion entre les différents formats de messages
propriétaires. Cela est une exigence courante, par exemple, lors de la
fourniture d'une nouvelle interface unifiée à plusieurs anciens systèmes
ou de l'intégration de systèmes informatiques suite à une fusion.

Le secteur du logiciel évolue rapidement ; les nouveaux langages de
programmation et les nouvelles infrastructures d'application sont
parfois inventés à vitesse grand V. De même, les besoins des systèmes
informatiques évoluent au fil du temps et les développeurs souhaitent
profiter des langages et infrastructures les plus récents. Cependant, le
fournisseur de messagerie sélectionné ne prend parfois pas en charge ces
plateformes. Ces protocoles étant propriétaires, il n'est pas possible
pour les autres de fournir des bibliothèques pour ces nouvelles
plateformes. Vous êtes donc limité à la création de passerelles, de
ponts et à d'autres approches.

Ce sont ces problèmes qui ont motivé le développement d'AMQP (Advanced
Message Queuing Protocol) 1.0. Il a vu le jour chez JP Morgan Chase,
qui, comme la plupart des entreprises proposant des services financiers,
est un grand consommateur d'intergiciels orientés messagerie. L'objectif
était simple : créer un protocole de messagerie open standard
permettant le développement d'applications basées sur des messages à
l'aide de composants créés avec plusieurs langages, infrastructures et
systèmes d'exploitation, utilisant tous les meilleurs composants
disponibles chez divers fournisseurs.
## Fonctionnalités techniques d'AMQP 1.0

AMQP 1.0 est un protocole de messagerie << wire-level >> efficace et
fiable qui peut être utilisé pour créer des applications de messagerie
interplateforme robustes. Il a un objectif simple : définir les
mécanismes du transfert sécurisé, fiable et efficace des messages entre
deux tiers. Les messages sont eux-mêmes codés à l'aide d'une
représentation portable de données qui permet l'échange haute-fidélité
de messages professionnels structurés entre des expéditeurs et des
destinataires hétérogènes. La liste suivante décrit les principales
caractéristiques :

* **Efficacité** : le protocole orienté connexion AMQP 1.0 utilise un
  codage binaire pour les instructions de protocole et les messages
  professionnels transférés par son biais. Il intègre des schémas de
  contrôle de flux sophistiqués pour optimiser l'utilisation du réseau
  et des composants connectés. Le protocole a été conçu pour garantir
  l'équilibre entre efficacité, flexibilité et interopérabilité.
* **Fiabilité** : le protocole AMQP 1.0 permet d'échanger des messages
  avec diverses garanties de fiabilité (autonomie, fiabilité, remise
  EOD, etc.).
* **Flexibilité** : AMQP 1.0 est un protocole flexible qui permet de
  prendre en charge différentes topologies. Vous pouvez utiliser le même
  protocole pour les communications de client à client, de client à
  service Broker et de service Broker à service Broker.
* **Modèle de service Broker indépendant** : la spécification AMQP 1.0
  n'applique aucune condition préalable sur le modèle de messagerie
  utilisé par un service Broker. Cela signifie qu'il est possible
  d'ajouter facilement AMQP 1.0 à des services Broker de messagerie
  existants.
## AMQP 1.0 est une Norme (avec une majuscule N)

AMQP 1.0 est développé depuis 2008 par un groupe de plus de
20 entreprises, aussi bien fournisseurs de technologie que sociétés
utilisatrices. Les dernières ont ainsi pu couvrir leurs besoins
professionnels concrets, tandis que les premiers ont fait évoluer le
protocole pour les satisfaire. Tout au long du processus, les
fournisseurs ont participé à des ateliers pour valider
l'interopérabilité entre leurs implémentations.

En octobre 2011, le travail de développement a été confié à un comité
technique au sein de l'OASIS (Organization for the Advancement of
Structured Information Standards) et la norme OASIS AMQP 1.0 a été
publiée en octobre 2012. Les entreprises suivantes ont participé au
comité technique dans le cadre du développement de la norme :

* **Fournisseurs de technologie** : Axway Software, Huawei
  Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre
  Corporation, Primeton Technologies, Progress Software, Red Hat, SITA,
  Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Sociétés utilisatrices** : Bank of America, Crédit Suisse, Deutsche
  Boerse, Goldman Sachs, JPMorgan Chase.

Les normes ouvertes présentent généralement les avantages suivants :

* Moins de risque de dépendance vis-à-vis d'un fournisseur
* Interopérabilité
* Large disponibilité des bibliothèques et outils
* Protection contre l'obsolescence
* Disponibilité du personnel qualifié
* Risque inférieur et gérable
## AMQP 1.0 et Service Bus

Grâce à l'ajout d'AMQP 1.0, vous pouvez désormais tirer parti des
fonctionnalités de messagerie répartie de Service Bus de mise en file
d'attente et de publication/d'abonnement à partir de diverses
plateformes, à l'aide d'un protocole binaire efficace. De plus, vous
pouvez créer des applications constituées de composants créés à l'aide
de divers langages, structures et systèmes d'exploitation.

Le diagramme ci-dessous montre un exemple de déploiement dans lequel des
clients Java exécutés sous Linux, écrits à l'aide de l'API standard JMS
(Java Message Service) et des clients .NET exécutés sous Windows,
échangent des messages via Service Bus à l'aide d'AMQP 1.0.

![](./media/service-bus-amqp-overview/Example1.png)

**Figure 1 : exemple de scénario de déploiement illustrant la
messagerie interplateforme avec Service Bus et AMQP 1.0.**

Les bibliothèques clientes suivantes fonctionnent actuellement avec
Service Bus :

<table>
  <tr>
    <th>Langage</th>

    <th>Bibliothèque</th>

  </tr>

  <tr>
    <td>Java</td>

    <td>Client JMS (Java Message Service) Apache Qpid<br  />
 Client Java IIT Software SwiftMQ</td>

  </tr>

  <tr>
    <td>C</td>

    <td>Apache Qpid Proton-C</td>

  </tr>

  <tr>
    <td>PHP</td>

    <td>Apache Qpid Proton-PHP</td>

  </tr>

  <tr>
    <td>Python</td>

    <td>Apache Qpid Proton-Python</td>

  </tr>


</table>

**Figure 2 : tableau des bibliothèques clientes d'AMQP 1.0**

Pour plus d'informations sur l'obtention et l'utilisation de ces
bibliothèques avec Service Bus, consultez le guide du développeur AMPQ
Service Bus. Pour plus d'informations, consultez la section Références
ci-dessous.
## Résumé

* AMQP 1.0 est un protocole de messagerie efficace et ouvert qui peut
  être utilisé pour créer des applications hybrides interplateforme.
  AMQP 1.0 est une norme OASIS.
* La prise en charge d'AMQP 1.0 est désormais disponible dans Azure
  Service Bus et Service Bus pour Windows Server (Service Bus 1.1). Les
  tarifs sont les mêmes que pour les protocoles existants.
## Références

* [Utilisation d'AMQP 1.0 avec l'API Service Bus .NET][1]
* [Utilisation de l'API Java Message Service (JMS) avec Service Bus et
  AMQP 1.0][2]
* [Guide du développeur AMQP 1.0 Service Bus][3]
* [Spécification OASIS Advanced Message Queuing Protocol (AMQP)
  Version 1.0][4]



[1]: http://aka.ms/lym3vk
[2]: http://aka.ms/ll1fm3
[3]: http://msdn.microsoft.com/en-us/library/windowsazure/jj841071.aspx
[4]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
