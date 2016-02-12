# Azure et IoT

Bienvenue dans Microsoft Azure et l'Internet des objets (IoT) Cet article présente une architecture de solution IoT typique qui décrit les caractéristiques communes d'une solution IoT que vous pouvez déployer à l'aide de services Azure. Une solution IoT typique nécessite une sécurisation de la communication bidirectionnelle entre les appareils, pouvant se chiffrer par millions, et un serveur principal de solution, par exemple, qui utilise des analyses prédictives et automatisées pour obtenir des informations de votre flux d’événements « appareil-à-cloud ».

## Architecture de solution IoT

Le diagramme suivant montre une architecture de solution IoT classique. Notez qu’il n’inclut le nom d’aucun service Azure, mais qu’il décrit les éléments clés dans une architecture de solution IoT générique. Les sections suivantes fournissent plus d’informations sur les éléments de cette solution.

![Architecture de solution IoT][img-solution-architecture]

### Connectivité des appareils

Dans un scénario IoT classique, les appareils envoient les données de télémétrie, comme par exemple des lectures de température, vers un point de terminaison du cloud pour qu’elles soient traitées et stockées. Les appareils peuvent également recevoir des commandes « cloud-à-appareil », et y répondre, en lisant les messages issus d’un point de terminaison du cloud. Par exemple, un appareil peut extraire une commande qui indique de modifier la fréquence à laquelle il échantillonne les données.

L’une des plus grandes difficultés dans les projets IoT consiste à pouvoir connecter de manière fiable et sécurisée des appareils au serveur principal de la solution. Généralement, les appareils IoT ont des caractéristiques différentes de celles d’autres clients tels que des navigateurs et des applications mobiles. Les appareils IoT :

- sont souvent des systèmes intégrés, qui ne font appel à aucun opérateur humain ;
- peuvent être situés sur des sites distants avec un accès physique très coûteux ;
- sont accessibles uniquement via le serveur principal de la solution ;
- peuvent avoir des performances et/ou des ressources de traitement limitées ;
- peuvent avoir une connectivité réseau intermittente, lente ou coûteuse ;
- peuvent nécessiter l’utilisation des protocoles d’application personnalisés, propriétaires ou spécifiques à un secteur ;
- peuvent être créés à l’aide d’un large éventail de plateformes matérielles et logicielles populaires.

Outre les exigences ci-dessus, n’importe quelle solution IoT doit également offrir des possibilités d’évolution, la sécurité et la fiabilité. Il en résulte un ensemble d’exigences de connectivité complexe, long à mettre en place avec des technologies traditionnelles telles que des conteneurs web et des courtiers de messagerie.

Un appareil peut communiquer directement avec un point de terminaison de passerelle du cloud, ou, si l’appareil ne peut pas utiliser les protocoles de communication que la passerelle de cloud prend en charge, il peut se connecter via une passerelle intermédiaire qui effectue la conversion de protocole.

### Traitement des données et analyse

Dans le cloud, un serveur principal de solution IoT :

- reçoit les données de télémétrie à l'échelle de vos appareils et détermine comment traiter et stocker ces données ; 
- peut également vous permettre d'envoyer des commandes à un appareil spécifique à partir du cloud ;
- fournit des fonctionnalités d’inscription vous permettant d’approvisionner des appareils et de contrôler ceux qui sont autorisés à se connecter à votre infrastructure ;
- vous permet de suivre l’état de vos appareils et de surveiller leurs activités.

Les solutions IoT peuvent inclure des boucles de rétroaction automatique. Par exemple, un module d’apprentissage automatique dans le serveur principal peut identifier, à partir de données de télémétrie, que la température d’un appareil spécifique est supérieure aux niveaux de fonctionnement normaux et donc envoyer une commande à l’appareil afin qu’il corrige cette situation.

### Présentation

De nombreuses solutions IoT permettent aux utilisateurs d’afficher et d’analyser les données collectées à partir de leurs appareils. Ces vues peuvent prendre la forme de tableaux de bord ou de rapports d’aide à la décision.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png

[lnk-machinelearning]: http://azure.microsoft.com/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot

<!---HONumber=AcomDC_0204_2016-->