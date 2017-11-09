> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-get-started.md)
> 
> 

Cet article fournit une description détaillée de [l’exemple de code Hello World][lnk-helloworld-sample] pour illustrer les composants fondamentaux de l’architecture [Azure IoT Edge][lnk-iot-edge]. L’exemple utilise Azure IoT Edge pour générer une passerelle simple qui enregistre un message « hello world » dans un fichier toutes les cinq secondes.

Cette procédure pas à pas inclut les étapes suivantes :

* **Architecture de l’exemple Hello World** : décrit la façon dont les [concepts architecturaux d’Azure IoT Edge][lnk-edge-concepts] s’appliquent à l’exemple Hello World, ainsi que la façon dont les composants s’imbriquent.
* **Comment créer l'exemple**: les étapes requises pour créer l'exemple.
* **Comment exécuter l'exemple**: les étapes requises pour exécuter l'exemple. 
* **Exemple de résultat**: un exemple du résultat attendu lorsque vous exécutez l'exemple.
* **Extraits de code** : collection d’extraits de code montrant comment l’exemple Hello World implémente les composants clés de la passerelle IoT Edge.


## <a name="hello-world-sample-architecture"></a>Exemple d'architecture Hello World
L'exemple Hello World illustre les concepts décrits dans la section précédente. L’exemple Hello World implémente une passerelle IoT Edge avec un pipeline composé de deux modules IoT Edge :

* Le module *Hello Wolrd* crée un message toutes les cinq secondes et le transmet au module enregistreur.
* Le module *enregistreur* inscrit les messages qu’il reçoit dans un fichier.

![Architecture de l’exemple Hello World conçu avec Azure IoT Edge][4]

Comme décrit dans la section précédente, le module Hello World ne transmet pas les messages directement vers le module enregistreur toutes les cinq secondes. Au lieu de cela, il publie un message sur le répartiteur toutes les cinq secondes.

Le module enregistreur reçoit le message du répartiteur et intervient en écrivant le contenu du message dans un fichier.

Le module enregistreur utilise uniquement les messages provenant du répartiteur et ne publie jamais de nouveaux messages sur le répartiteur.

![Comment le répartiteur achemine les messages entre les modules dans Azure IoT Edge][5]

La figure précédente montre l’architecture de l’exemple Hello World et les chemins d’accès relatifs aux fichiers sources qui implémentent différentes parties de l’exemple dans le [référentiel][lnk-iot-edge]. Explorez vous-même le code, ou utilisez les extraits de code indiqués dans cet article comme guide.

<!-- Images -->
[4]: media/iot-hub-iot-edge-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-iot-edge-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/iot-edge/tree/master/samples/hello_world
[lnk-iot-edge]: https://github.com/Azure/iot-edge
[lnk-edge-concepts]: ../articles/iot-hub/iot-hub-iot-edge-overview.md