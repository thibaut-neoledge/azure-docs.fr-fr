## <a name="build-iot-edge"></a>Générer IoT Edge

Ce didacticiel utilise des modules IoT Edge personnalisés pour communiquer avec la solution préconfigurée de surveillance à distance. Vous devez donc générer les modules IoT Edge à partir du code source personnalisé. Les sections suivantes décrivent l’installation de IoT Edge et la génération du module IoT Edge personnalisé.

### <a name="install-iot-edge"></a>Installer IoT Edge

Les étapes suivantes décrivent l’installation du logiciel IoT Edge précompilé sur Intel NUC :

1. Configurez les référentiels de packages intelligents requis en exécutant les commandes suivantes sur Intel NUC :

    ```bash
    smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
    smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
    ```

    Entrez `y` quand la commande vous demande si vous souhaitez **inclure ce canal**.

1. Mettez à jour le gestionnaire de package intelligent en exécutant la commande suivante :

    ```bash
    smart update
    ```

1. Installez le package Azure IoT Edge en exécutant la commande suivante :

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```

1. Vérifiez l’installation en exécutant l’exemple « Hello World ». Cet exemple écrit un message hello world au fichier log.txt toutes les cinq secondes. Les commandes suivantes exécutent l’exemple « Hello World » :

    ```bash
    cd /usr/share/azureiotgatewaysdk/samples/hello_world/
    ./hello_world hello_world.json
    ```

    Ignorez les éventuels messages **argument non valide** lorsque vous arrêtez l’exemple.

    Utilisez la commande suivante pour afficher le contenu du fichier journal :

    ```bash
    cat log.txt | more
    ```

### <a name="troubleshooting"></a>Résolution des problèmes

Essayez de redémarrer Intel NUC si vous recevez l’erreur : « Aucun package ne fournit util-linux-dev ».
