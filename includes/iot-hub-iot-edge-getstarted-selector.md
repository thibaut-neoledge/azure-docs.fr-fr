> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-get-started.md)
> 
> 

Cet article fournit une description détaillée de [l’exemple de code Hello World][lnk-helloworld-sample] pour illustrer les composants fondamentaux de l’architecture [Azure IoT Edge][lnk-iot-edge]. L’exemple utilise Azure IoT Edge pour générer une passerelle simple qui enregistre un message « hello world » dans un fichier toutes les cinq secondes.

Cette procédure pas à pas inclut les étapes suivantes :

* **Concepts** : vue d’ensemble conceptuelle des composants de toute passerelle créée avec IoT Edge.
* **Architecture de l'exemple Hello World**: décrit comment les concepts s'appliquent à l'exemple Hello World et comment les composants s'imbriquent.
* **Comment créer l'exemple**: les étapes requises pour créer l'exemple.
* **Comment exécuter l'exemple**: les étapes requises pour exécuter l'exemple. 
* **Exemple de résultat**: un exemple du résultat attendu lorsque vous exécutez l'exemple.
* **Extraits de code** : collection d’extraits de code montrant comment l’exemple Hello World implémente les composants clés de la passerelle IoT Edge.

## <a name="azure-iot-edge-concepts"></a>Concepts relatifs à Azure IoT Edge
Avant d’examiner l’exemple de code ou de créer votre propre passerelle de champ à l’aide de IoT Edge, vous devez comprendre les concepts fondamentaux qui étayent l’architecture de IoT Edge.

### <a name="iot-edge-modules"></a>Modules IoT Edge
Vous créez une passerelle avec Azure IoT Edge en créant et en assemblant des *modules IoT Edge*. Les modules s’échangent des données par le biais de *messages* . Un module reçoit un message, exécute une action sur celui-ci, le transforme éventuellement en un nouveau message, puis le publie sur d'autres modules pour un traitement ultérieur. Certains modules peuvent uniquement produire de nouveaux messages et ne jamais traiter les messages entrants. Une chaîne de modules crée un pipeline de traitement des données dans lequel chaque module exécute une transformation de données en un point unique sur ce pipeline.

![Chaîne de modules dans la passerelle créée avec Azure IoT Edge][1]

IoT Edge contient les éléments suivants :

* Des modules pré-écrits qui exécutent des fonctions de passerelles courantes.
* Les interfaces qu’un développeur peut utiliser pour écrire des modules personnalisés.
* L'infrastructure nécessaire pour déployer et exécuter un ensemble de modules.

Le SDK fournit une couche d'abstraction qui vous permet de créer des passerelles qui s'exécutent sur différents systèmes d'exploitation et plates-formes.

![Couche d’abstraction Azure IoT Edge][2]

### <a name="messages"></a>Messages
L’approche consistant à utiliser des modules pour s’échanger des messages représente un moyen pratique de conceptualiser le fonctionnement d'une passerelle, mais elle ne reflète pas exactement ce qui se produit. Les modules IoT Edge utilisent un répartiteur pour communiquer entre eux. Ils publient des messages sur le répartiteur (bus, pubsub ou tout autre modèle de messagerie), puis le laissent acheminer le message vers les modules connectés.

Un module utilise la fonction **Broker_Publish** pour publier un message sur le répartiteur. Le répartiteur remet les messages à un module en appelant une fonction de rappel. Un message se compose d'un ensemble de propriétés de clés/valeurs et d’un contenu transmis sous forme d’un bloc de mémoire.

![Rôle du répartiteur dans Azure IoT Edge][3]

### <a name="message-routing-and-filtering"></a>Routage et filtrage des messages
Deux méthodes permettent d’acheminer des messages vers les modules IoT Edge adéquats. Il est possible de transmettre un ensemble de liens au répartiteur pour permettre à ce dernier de connaître la source et le récepteur de chaque module. L’autre méthode consiste configurer le module pour appliquer un filtre sur les propriétés du message. Un module doit agir sur un message uniquement si le message lui est destiné. Le filtrage des liens et des messages est à la base même d’un pipeline de messages.

## <a name="hello-world-sample-architecture"></a>Exemple d'architecture Hello World
L'exemple Hello World illustre les concepts décrits dans la section précédente. L’exemple Hello World implémente une passerelle IoT Edge avec un pipeline composé de deux modules IoT Edge :

* Le module *Hello Wolrd* crée un message toutes les cinq secondes et le transmet au module enregistreur.
* Le module *enregistreur* inscrit les messages qu’il reçoit dans un fichier.

![Architecture de l’exemple Hello World conçu avec Azure IoT Edge][4]

Comme décrit dans la section précédente, le module Hello World ne transmet pas les messages directement vers le module enregistreur toutes les cinq secondes. Au lieu de cela, il publie un message sur le répartiteur toutes les cinq secondes.

Le module enregistreur reçoit le message du répartiteur et intervient en écrivant le contenu du message dans un fichier.

Le module enregistreur utilise uniquement les messages provenant du répartiteur et ne publie jamais de nouveaux messages sur le répartiteur.

![Comment le répartiteur achemine les messages entre les modules dans Azure IoT Edge][5]

La figure ci-dessus montre l’architecture de l’exemple Hello World et les chemins d’accès relatifs aux fichiers sources qui implémentent différentes parties de l’exemple dans le [référentiel][lnk-iot-edge]. Explorez vous-même le code, ou utilisez les extraits de code ci-dessous comme guide.

<!-- Images -->
[1]: media/iot-hub-iot-edge-getstarted-selector/modules.png
[2]: media/iot-hub-iot-edge-getstarted-selector/modules_2.png
[3]: media/iot-hub-iot-edge-getstarted-selector/messages_1.png
[4]: media/iot-hub-iot-edge-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-iot-edge-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/iot-edge/tree/master/samples/hello_world
[lnk-iot-edge]: https://github.com/Azure/iot-edge