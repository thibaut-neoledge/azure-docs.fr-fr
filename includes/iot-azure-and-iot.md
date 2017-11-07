
# <a name="azure-and-the-internet-of-things"></a>Azure et l’Internet des objets

Bienvenue dans Microsoft Azure et l'Internet des objets (IoT) Cet article décrit les caractéristiques communes d’une solution IoT dans le cloud. Les solutions IoT nécessitent de sécuriser la communication bidirectionnelle entre les appareils, pouvant se chiffrer par millions, et un serveur principal de solution. Par exemple, une solution peut utiliser l’analyse prédictive automatisée pour découvrir des informations à partir de votre flux d’événements de l’appareil vers le cloud.

## <a name="iot-solution-architecture"></a>Architecture de solution IoT

Le diagramme suivant montre les éléments clés d’une architecture de solution IoT classique. Le diagramme est indépendant des détails de mise en œuvre spécifiques tels que les services Azure utilisés et systèmes d’exploitation des appareils. Dans cette architecture, les appareils IoT collectent des données qu’ils envoient à une passerelle cloud. La passerelle cloud met les données à la disposition d’autres services backend pour traitement. Ces services back-end peuvent fournir des données à :

* D’autres applications métier.
* Des opérateurs via un tableau de bord ou un autre appareil de présentation.

![Architecture de solution IoT][img-solution-architecture]

> [!NOTE]
> Pour une étude approfondie de l’architecture IoT, consultez le document [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Microsoft Azure IoT : Architecture de référence).

### <a name="device-connectivity"></a>Connectivité des appareils

Dans une architecture de solution IoT, les appareils envoient généralement des données de télémétrie au cloud pour stockage et traitement. Par exemple, dans un scénario de maintenance prédictive, le serveur principal de solution peut utiliser le flux des données de capteur pour déterminer si une pompe spécifique nécessite une maintenance. Les appareils peuvent également recevoir des messages « cloud-à-appareil », et y répondre, en lisant les messages issus d’un point de terminaison cloud. Dans le même exemple, le serveur principal de solution peut envoyer des messages aux autres pompes de la station de pompage pour lancer le réacheminement des flux juste avant le début planifié de la maintenance. Cette procédure garantit que l’ingénieur de maintenance peut commencer la maintenance lors de son arrivée sur le site.

La connexion sécurisée et fiable des appareils est souvent le défi le plus important dans les solutions IoT. Cela est dû au fait que les appareils IoT ont des caractéristiques différentes de celles d’autres clients tels que des navigateurs et des applications mobiles. Plus précisément, les appareils IoT :

* sont souvent des systèmes intégrés, qui ne font appel à aucun opérateur humain (contrairement à un téléphone) ;
* peuvent être déployés sur des sites distants avec un accès physique coûteux ;
* sont accessibles uniquement via le serveur principal de la solution ; Il n’existe aucun autre moyen d’interagir avec l’appareil.
* peuvent avoir des performances et/ou des ressources de traitement limitées ;
* peuvent avoir une connectivité réseau intermittente, lente ou coûteuse ;
* peuvent nécessiter l’utilisation des protocoles d’application personnalisés, propriétaires ou spécifiques à un secteur.
* peuvent être créés à l’aide d’un large éventail de plateformes matérielles et logicielles populaires.

En plus des contraintes précédentes, une solution IoT doit également être évolutive, sécurisée et fiable.

En fonction du protocole de communication et de la disponibilité du réseau, un appareil peut communiquer avec le cloud directement ou via une passerelle intermédiaire. Les architectures IoT combinent souvent ces deux modèles de communication.

### <a name="data-processing-and-analytics"></a>Traitement et analyse des données

Dans les solutions IoT modernes, le traitement des données peut se faire dans le cloud ou du côté de l’appareil. Le traitement côté appareil est appelé *bord informatique*. Le choix de l’emplacement de traitement des données dépend de facteurs tels que :

* Contraintes de réseau. Si la bande passante entre les appareils et le cloud est limitée, il serait intéressant d’effectuer plus de traitements de bord.
* Temps de réponse. S’il est nécessaire d’agir sur un appareil en temps quasi réel, il peut être préférable de traiter la réponse dans l’appareil. Par exemple, un bras de robot qui doit être arrêté en cas d’urgence.
* Environnement réglementaire. Certaines données ne peuvent pas être envoyées vers le cloud.

En général, le traitement des données à la fois dans le bord et dans le cloud combine les fonctionnalités suivantes :

* Recevoir des données de télémétrie à l'échelle de vos appareils et déterminer comment traiter et stocker ces données.
* Analyser les données de la télémétrie pour fournir des informations, que ce soit en temps réel ou différé.
* Envoyer des commandes à partir du cloud ou d’un appareil de passerelle à un appareil spécifique.

En outre, un back end cloud d’IoT doit fournir :

* Des fonctionnalités d’inscription d’appareils qui vous permettent de :
    * Mettre des appareils en service.
    * Contrôler les appareils qui sont autorisés à se connecter à votre infrastructure.
* Une gestion des appareils pour surveiller l’état de vos appareils et leurs activités.

Par exemple, dans le scénario de maintenance prédictive, le serveur principal cloud stocke les données de télémétrie historiques. La solution utilise ces données pour identifier un comportement anormal potentiel au niveau de pompes spécifiques avant qu’un problème réel ne puisse survenir. À l’aide de l’analyse des données, on peut identifier que la solution préventive consiste à envoyer une commande à l’appareil pour effectuer une action corrective. Ce processus génère une boucle de rétroaction automatisée entre l’appareil et le cloud qui augmente considérablement l’efficacité de la solution.

### <a name="presentation-and-business-connectivity"></a>Présentation et connectivité d’entreprise

Grâce à la couche de présentation et de connectivité d’entreprise, les utilisateurs finaux peuvent interagir avec la solution IoT et les appareils. Les utilisateurs peuvent ainsi afficher et analyser les données collectées à partir de leurs appareils. Ces vues peuvent prendre la forme de tableaux de bord ou de rapports BI qui affichent les données historiques ou les données quasiment en temps réel. Par exemple, un opérateur peut vérifier l’état d’une station de pompage spécifique et voir les alertes éventuellement déclenchées par le système. Cette couche permet également d’intégrer le serveur principal de solution IoT aux applications métier existantes pour une adaptation optimale aux flux de travail et processus d’entreprise. Par exemple, une solution de maintenance prédictive peut intégrer un système de planification qui prévoit une visite d’un ingénieur sur une station de pompage quand elle identifie un besoin de maintenance sur une pompe.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-suite/iot-suite-overview.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
