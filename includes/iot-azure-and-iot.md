# Microsoft Azure et l’Internet des objets (IoT)

Une solution IoT typique nécessite de sécuriser la communication bidirectionnelle entre les appareils, pouvant se chiffrer par millions, et un service principal d’applications. Le traitement des événements « appareil vers cloud » pour découvrir des informations à l’aide des analyses prédictive et automatisée est un exemple de fonctionnalité de service principal d’applications.

Microsoft fournit un ensemble de bibliothèques, qui prennent en charge plusieurs langages et plateformes matérielles, que vous pouvez utiliser pour développer des applications clientes destinées à être exécutées sur un appareil IoT. Pour implémenter votre application de service principal IoT, vous pouvez combiner plusieurs services Azure ou utiliser une des solutions préconfigurées disponibles dans [Azure IoT Suite][]. Pour mieux comprendre comment Azure permet cette infrastructure IoT, il est utile d’envisager une architecture de solution IoT classique.

## Architecture de solution IoT

Le diagramme suivant montre une architecture de solution IoT classique. Notez qu’il n’inclut le nom d’aucun service Azure, mais qu’il décrit les éléments clés dans une architecture de solution IoT générique. Les sections suivantes fournissent plus d’informations sur les éléments de cette solution.

![Architecture de solution IoT][img-solution-architecture]

### Connectivité des appareils

Dans un scénario IoT classique, les appareils envoient des données de télémétrie « appareil vers cloud », telles que les lectures de température, vers un point de terminaison du cloud pour qu’elles soient traitées et stockées. Les appareils peuvent également recevoir des commandes « cloud vers appareil », et y répondre, en lisant les messages issus d’un point de terminaison du cloud. Par exemple, un appareil peut extraire une commande qui indique de modifier la fréquence à laquelle il échantillonne les données.

Un appareil ou une source de données dans une solution IoT peut aller d’un détecteur simple connecté au réseau à un appareil informatique puissant et autonome. Un appareil peut disposer de ressources limitées en termes de capacité de traitement, de mémoire, de bande passante de communication et de prise en charge du protocole de communication.

Un appareil peut communiquer avec un point de terminaison d’une passerelle de cloud directement à l’aide d’un protocole de communication, tel qu’AMQP ou HTTP, ou indirectement au moyen d’un dispositif tel qu’une passerelle de champ qui fournit un service de traduction de protocole.

### Traitement des données et analyse

Dans le cloud, un processeur d’événements de flux reçoit des messages « appareil vers cloud » à grande échelle en provenance de vos appareils et détermine comment les traiter et les stocker. Une solution pour les appareils connectés permet d’envoyer des données « cloud vers appareil » à des appareils spécifiques sous la forme de commandes. L’inscription d’appareils avec la solution IoT permet d’approvisionner les appareils et de contrôler ceux qui sont autorisés à se connecter à votre infrastructure. Vous pouvez suivre l’état de vos appareils et surveiller leurs activités au moyen du service principal.

Les solutions IoT peuvent inclure des boucles de rétroaction automatique. Par exemple, un module d’apprentissage automatique peut identifier à partir de données de télémétrie « appareil vers cloud » que la température d’un appareil spécifique est supérieure aux niveaux de fonctionnement normal et donc envoyer une commande à l’appareil afin qu’il corrige cette situation.

### Présentation

De nombreuses solutions IoT permettent aux utilisateurs d’afficher et d’analyser les données collectées à partir de leurs appareils. Ces vues peuvent prendre la forme de tableaux de bord ou de rapports d’aide à la décision.

[img-solution-architecture]: media/iot-azure-and-iot/iot-reference-architecture.png

[lnk-machinelearning]: http://azure.microsoft.com/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot

<!---HONumber=Oct15_HO3-->