## <a name="configure-the-nodejs-simulated-device"></a>Configurer l’appareil simulé Node.js
1. Dans le tableau de bord de surveillance à distance, cliquez sur **+ Ajouter un appareil**, puis ajoutez un *appareil personnalisé*. Notez le nom d’hôte, l’ID de l’appareil et la clé de l’appareil IoT Hub. Vous en aurez besoin ultérieurement dans ce didacticiel lorsque vous préparerez l’application cliente de l’appareil remote_monitoring.js.
2. Assurez-vous que Node.js version 0.12.x ou ultérieure est installé sur votre ordinateur de développement. Exécutez `node --version` à l’invite de commande ou dans un interpréteur de commandes pour vérifier la version. Pour plus d’informations sur l’utilisation d’un gestionnaire de package pour installer Node.js sur Linux, consultez l’article [Installing Node.js via package manager][node-linux] (Installation de Node.js par le biais d’un gestionnaire de package).
3. Une fois que vous avez installé Node.js, clonez la dernière version du référentiel [azure-iot-sdk-node][lnk-github-repo] sur votre ordinateur de développement. Utilisez toujours la branche **maître** pour avoir la version la plus récente des bibliothèques et des exemples.
4. À partir de votre copie locale du référentiel [azure-iot-sdk-node][lnk-github-repo], copiez les deux fichiers ci-après du dossier node/device/samples vers un dossier vide de votre ordinateur de développement :
   
   * packages.json
   * remote_monitoring.js
5. Ouvrez le fichier remote_monitoring.js et recherchez la définition de variable suivante :
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. Remplacez **[chaîne de connexion d’un périphérique de IoT Hub]** par votre chaîne de connexion de périphérique. Utilisez les valeurs de nom d’hôte, ID de l’appareil et clé de l’appareil IoT Hub notées à l’étape 1. La chaîne de connexion du périphérique suit ce format :
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Si votre nom d’hôte IoT Hub est **contoso** et votre ID d’appareil **mydevice**, votre chaîne de connexion ressemble à l’extrait de code suivant :
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. Enregistrez le fichier . Exécutez les commandes suivantes dans un interpréteur de commandes ou une invite de commandes dans le dossier contenant ces fichiers pour installer les packages nécessaires, puis exécutez l’exemple d’application :
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Observer la télémétrie dynamique en action
Le tableau de bord affiche la télémétrie de température et d’humidité à partir des appareils simulés existants :

![Tableau de bord par défaut][image1]

Si vous sélectionnez l’appareil simulé Node.js que vous avez exécuté dans la section précédente, vous affichez la télémétrie de température, d’humidité et de température externe :

![Ajouter une température externe au tableau de bord][image2]

La solution de surveillance à distance détecte automatiquement le type supplémentaire de télémétrie de température externe et l’ajoute au graphique sur le tableau de bord.

[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdk-node
[image1]: media/iot-suite-send-external-temperature/image1.png
[image2]: media/iot-suite-send-external-temperature/image2.png

<!--HONumber=Dec16_HO3-->


