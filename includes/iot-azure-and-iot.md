
# <a name="azure-and-internet-of-things"></a>Azure et IoT

Bienvenue dans Microsoft Azure et l'Internet des objets (IoT) Cet article présente les caractéristiques communes d’une solution IoT que vous pouvez déployer à l’aide des services Azure. Les solutions IoT nécessitent de sécuriser la communication bidirectionnelle entre un grand nombre d’appareils et un backend de solution. Le backend de solution peut utiliser l’analyse prédictive automatisée pour découvrir des informations à partir de votre flux d’événements appareil-à-cloud.

[Azure IoT Hub][lnk-iot-hub] est la clé de voûte de toute solution IoT qui utilise des services Azure. IoT Hub est un service entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un backend de solution. 

[Azure IoT Suite][lnk-iot-suite] assure des implémentations complètes de bout en bout de cette architecture pour des scénarios IoT spécifiques. Par exemple :

* La solution de *surveillance à distance* vous permet de surveiller l’état des appareils tels que les distributeurs automatiques.
* La solution de *maintenance prédictive* permet d’anticiper les besoins de maintenance des appareils, comme les pompes des stations de pompage à distance, et d’éviter les temps d’arrêt non planifiés.
* La solution d’*usine connectée* vous permet de vous connecter et de surveiller vos appareils industriels.

## <a name="iot-solution-architecture"></a>Architecture de solution IoT

Le diagramme suivant montre une architecture de solution IoT classique. Il n’inclut le nom d’aucun service Azure, mais il décrit les éléments clés dans une architecture de solution IoT générique. Dans cette architecture, les appareils IoT collectent des données qu’ils envoient à une passerelle cloud. La passerelle cloud met les données à la disposition d’autres services backend pour traitement. Le backend de solution remet les données à des applications métier ou à des opérateurs humains par le biais d’un tableau de bord ou d’un rapport.

![Architecture de solution IoT][img-solution-architecture]

> [!NOTE]
> Pour une étude approfondie de l’architecture IoT, consultez le document [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Microsoft Azure IoT : Architecture de référence).

### <a name="device-connectivity"></a>Connectivité des appareils

Dans cette solution IoT, les appareils envoient les données de télémétrie, comme les lectures de capteur d’une station de pompage, vers un point de terminaison cloud pour stockage et traitement. Dans un scénario de maintenance prédictive, le serveur principal de solution peut utiliser le flux des données de capteur pour déterminer si une pompe spécifique nécessite une maintenance. Les appareils peuvent également recevoir des messages « cloud-à-appareil », et y répondre, en lisant les messages issus d’un point de terminaison cloud. Par exemple, dans le scénario de maintenance prédictive, le serveur principal de solution peut envoyer des messages aux autres pompes de la station de pompage pour lancer le réacheminement des flux juste avant le début planifié de la maintenance. Cette procédure garantit que l’ingénieur de maintenance peut commencer à résoudre le problème immédiatement.

L’une des plus grandes difficultés dans les projets IoT consiste à pouvoir connecter de manière fiable et sécurisée des appareils au serveur principal de la solution. Les appareils IoT ont des caractéristiques différentes de celles d’autres clients tels que des navigateurs et des applications mobiles. Les appareils IoT :

* sont souvent des systèmes intégrés, qui ne font appel à aucun opérateur humain ;
* peuvent être déployés sur des sites distants avec un accès physique coûteux ;
* sont accessibles uniquement via le serveur principal de la solution ; Il n’existe aucun autre moyen d’interagir avec l’appareil.
* peuvent avoir des performances et/ou des ressources de traitement limitées ;
* peuvent avoir une connectivité réseau intermittente, lente ou coûteuse ;
* peuvent nécessiter l’utilisation des protocoles d’application personnalisés, propriétaires ou spécifiques à un secteur.
* peuvent être créés à l’aide d’un large éventail de plateformes matérielles et logicielles populaires.

Outre les exigences ci-dessus, n’importe quelle solution IoT doit également offrir des possibilités d’évolution, la sécurité et la fiabilité. Il en résulte un ensemble d’exigences de connectivité complexe, long à mettre en place avec des technologies traditionnelles telles que des conteneurs web et des courtiers de messagerie. Azure IoT Hub et les Kits Azure IoT device SDK facilitent l’implémentation de solutions qui répondent à ces exigences.

Un appareil peut communiquer directement avec un point de terminaison de passerelle cloud. Si l’appareil ne peut pas utiliser les protocoles de communication que la passerelle de cloud prend en charge, il peut se connecter par le biais d’une passerelle intermédiaire. Par exemple, la [passerelle de protocole Azure IoT][lnk-protocol-gateway] peut traduire le protocole si les appareils ne peuvent pas utiliser les protocoles pris en charge par IoT Hub.

### <a name="data-processing-and-analytics"></a>Traitement et analyse des données

Dans le cloud, un backend de solution IoT regroupe la plupart des opérations de traitement des données. Le serveur principal de solution IoT :

* reçoit les données de télémétrie à l'échelle de vos appareils et détermine comment traiter et stocker ces données ; 
* peut vous permettre d’envoyer des commandes à des appareils spécifiques à partir du cloud ;
* fournit des fonctionnalités d’inscription vous permettant d’approvisionner des appareils et de contrôler ceux qui peuvent se connecter à votre infrastructure ;
* vous permet de suivre l’état de vos appareils et de surveiller leurs activités.

Dans le scénario de maintenance prédictive, le serveur principal de solution stocke les données de télémétrie historiques. Le backend de solution peut utiliser ces données pour identifier les modèles qui indiquent un besoin de maintenance sur une pompe spécifique.

Les solutions IoT peuvent inclure des boucles de rétroaction automatique. Par exemple, un module d’analyse dans le serveur principal de solution peut déterminer, à partir de données de télémétrie, que la température d’un appareil spécifique est supérieure aux niveaux de fonctionnement normaux. La solution peut alors envoyer une commande vers l’appareil, lui indiquant de prendre des mesures correctives.

### <a name="presentation-and-business-connectivity"></a>Présentation et connectivité d’entreprise

Grâce à la couche de présentation et de connectivité d’entreprise, les utilisateurs finaux peuvent interagir avec la solution IoT et les appareils. Les utilisateurs peuvent ainsi afficher et analyser les données collectées à partir de leurs appareils. Ces vues peuvent prendre la forme de tableaux de bord ou de rapports qui affichent les données historiques ou les données quasiment en temps réel. Par exemple, un opérateur peut vérifier l’état d’une station de pompage spécifique et voir les alertes éventuellement déclenchées par le système. Cette couche permet également d’intégrer le serveur principal de solution IoT aux applications métier existantes pour une adaptation optimale aux flux de travail et processus d’entreprise. Par exemple, la solution de maintenance prédictive peut intégrer un système de planification. Quand la solution identifie un besoin de maintenance sur une pompe, le système de planification programme la visite d’un ingénieur sur la station de pompage.

![Tableau de bord de solution IoT][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-suite/iot-suite-overview.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
