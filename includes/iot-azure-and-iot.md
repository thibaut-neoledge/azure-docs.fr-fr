
# Azure et IoT

Bienvenue dans Microsoft Azure et l'Internet des objets (IoT) Cet article présente une architecture de solution IoT qui décrit les caractéristiques communes d’une solution IoT que vous pouvez déployer à l’aide de services Azure. Les solutions IoT nécessitent de sécuriser la communication bidirectionnelle entre les appareils, pouvant se chiffrer par millions, et un serveur principal de solution. Par exemple, un serveur principal de solution peut utiliser l’analyse prédictive automatisée pour découvrir des informations à partir de votre flux d’événements de l’appareil vers le cloud.

Azure IoT Hub est la clé de voûte de l’implémentation de cette architecture de solution IoT au moyen de services Azure. IoT Suite assure des implémentations complètes de bout en bout de cette architecture pour des scénarios IoT spécifiques. Par exemple :

- La solution de *surveillance à distance* vous permet de surveiller l’état des appareils tels que les distributeurs automatiques.
- La solution de *maintenance prédictive* vous permet d’anticiper les besoins de maintenance des appareils tels que les pompes des stations de pompage à distance et d’éviter les temps d’arrêt non planifiés.

## Architecture de solution IoT

Le diagramme suivant montre une architecture de solution IoT classique. Il n’inclut le nom d’aucun service Azure, mais il décrit les éléments clés dans une architecture de solution IoT générique. Dans cette architecture, les appareils IoT collectent des données qu’ils envoient à une passerelle cloud. La passerelle cloud met les données à la disposition d’autres services principaux, qui les traitent avant de les fournir à d’autres applications métier ou opérateurs humains par le biais d’un tableau de bord ou autre appareil de présentation.

![Architecture de solution IoT][img-solution-architecture]

> [AZURE.NOTE] Pour une étude approfondie de l’architecture IoT, consultez le document [Microsoft Azure IoT services: Reference Architecture][lnk-refarch] (Services Microsoft Azure IoT : Architecture de référence).

### Connectivité des appareils

Dans cette architecture de solution IoT, les appareils envoient les données de télémétrie, comme les lectures de capteur d’une station de pompage, vers un point de terminaison cloud pour qu’elles soient traitées et stockées. Dans un scénario de maintenance prédictive, le serveur principal peut analyser le flux des données de capteur pour déterminer si une pompe spécifique nécessite une maintenance. Les appareils peuvent également recevoir des commandes « cloud-à-appareil », et y répondre, en lisant les messages issus d’un point de terminaison cloud. Par exemple, dans le scénario de maintenance prédictive, le serveur principal de solution peut envoyer des commandes aux autres pompes de la station de pompage pour lancer le réacheminement des flux juste avant le début planifié de la maintenance. Ainsi, l’ingénieur de maintenance peut commencer la maintenance lors de son arrivée sur le site.

L’une des plus grandes difficultés dans les projets IoT consiste à pouvoir connecter de manière fiable et sécurisée des appareils au serveur principal de la solution. Les appareils IoT ont des caractéristiques différentes de celles d’autres clients tels que des navigateurs et des applications mobiles. Les appareils IoT :

- sont souvent des systèmes intégrés, qui ne font appel à aucun opérateur humain ;
- peuvent être déployés sur des sites distants avec un accès physique coûteux ;
- sont accessibles uniquement via le serveur principal de la solution ; Il n’existe aucun autre moyen d’interagir avec l’appareil.
- peuvent avoir des performances et/ou des ressources de traitement limitées ;
- peuvent avoir une connectivité réseau intermittente, lente ou coûteuse ;
- peuvent nécessiter l’utilisation des protocoles d’application personnalisés, propriétaires ou spécifiques à un secteur.
- peuvent être créés à l’aide d’un large éventail de plateformes matérielles et logicielles populaires.

Outre les exigences ci-dessus, n’importe quelle solution IoT doit également offrir des possibilités d’évolution, la sécurité et la fiabilité. Il en résulte un ensemble d’exigences de connectivité complexe, long à mettre en place avec des technologies traditionnelles telles que des conteneurs web et des courtiers de messagerie. Azure IoT Hub et les Kits SDK IoT Device facilitent l’implémentation de solutions qui répondent à ces exigences.

Un appareil peut communiquer directement avec un point de terminaison de passerelle du cloud, ou, si l’appareil ne peut pas utiliser les protocoles de communication que la passerelle de cloud prend en charge, il peut se connecter via une passerelle intermédiaire. Par exemple, la [passerelle de protocole IoT Hub][lnk-protocol-gateway] peut traduire le protocole si les appareils ne peuvent pas utiliser les protocoles pris en charge par IoT Hub.

### Traitement et analyse des données

Dans le cloud, le serveur principal de solution IoT est l’endroit où se déroule la quasi-totalité du traitement des données, notamment le filtrage et l’agrégation des données de télémétrie et leur routage vers d’autres services. Le serveur principal de solution IoT :

- reçoit les données de télémétrie à l'échelle de vos appareils et détermine comment traiter et stocker ces données ;
- peut vous permettre d’envoyer des commandes à un appareil spécifique à partir du cloud ;
- fournit des fonctionnalités d’inscription vous permettant d’approvisionner des appareils et de contrôler ceux qui sont autorisés à se connecter à votre infrastructure ;
- vous permet de suivre l’état de vos appareils et de surveiller leurs activités.

Dans le scénario de maintenance prédictive, le serveur principal de solution stocke les données de télémétrie historiques. Le serveur principal peut utiliser ces données pour identifier les modèles qui indiquent un besoin de maintenance sur une pompe spécifique.

Les solutions IoT peuvent inclure des boucles de rétroaction automatique. Par exemple, un module d’analyse dans le serveur principal peut déterminer, à partir de données de télémétrie, que la température d’un appareil spécifique est supérieure aux niveaux de fonctionnement normaux. La solution peut alors envoyer une commande vers l’appareil, lui indiquant de prendre des mesures correctives.

### Présentation et connectivité d’entreprise

Grâce à la couche de présentation et de connectivité d’entreprise, les utilisateurs finaux peuvent interagir avec la solution IoT et les appareils. Les utilisateurs peuvent ainsi afficher et analyser les données collectées à partir de leurs appareils. Ces vues peuvent prendre la forme de tableaux de bord ou de rapports BI qui affichent les données historiques ou les données quasiment en temps réel. Par exemple, un opérateur peut vérifier l’état d’une station de pompage spécifique et voir les alertes éventuellement déclenchées par le système. Cette couche permet également d’intégrer le serveur principal de solution IoT aux applications métier existantes pour une adaptation optimale aux flux de travail et processus d’entreprise. Par exemple, la solution de maintenance prédictive peut intégrer un système de planification qui prévoit la visite d’un ingénieur sur une station de pompage quand elle identifie un besoin de maintenance sur une pompe.

![Tableau de bord de solution IoT][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]: ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf

<!---HONumber=AcomDC_1005_2016-->