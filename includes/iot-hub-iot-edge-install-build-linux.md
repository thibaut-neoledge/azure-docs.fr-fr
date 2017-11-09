## <a name="install-the-prerequisites"></a>Installer les composants requis

Les étapes de ce didacticiel supposent que vous exécutez Ubuntu Linux.

Pour installer les packages requis, ouvrez un interpréteur de commandes et exécutez les commandes suivantes :

```bash
sudo apt-get update
sudo apt-get install curl build-essential libcurl4-openssl-dev git cmake pkg-config libssl-dev uuid-dev valgrind libglib2.0-dev libtool autoconf
```

Dans l’interpréteur de commandes, exécutez la commande suivante pour cloner le référentiel GitHub d’Azure IoT Edge sur l’ordinateur local :

```bash
git clone https://github.com/Azure/iot-edge.git
```

## <a name="how-to-build-the-sample"></a>Comment créer l'exemple

Vous pouvez désormais créer le runtime et les exemples IoT Edge sur l’ordinateur local :

1. Ouvrez un interpréteur de commandes.

1. Accédez au dossier racine de votre copie locale du référentiel **iot-edge**.

1. Exécutez le script de génération comme suit :

    ```sh
    tools/build.sh --disable-native-remote-modules
    ```

Ce script utilise l’utilitaire **cmake** pour créer un dossier appelé **build** dans le dossier racine de votre copie locale du référentiel **iot-edge** et pour générer un makefile. Le script crée ensuite la solution, et ignore les tests unitaires et les tests de bout en bout. Pour générer et exécuter les tests unitaires, ajoutez le paramètre `--run-unittests`. Pour générer et exécuter les tests de bout en bout, ajoutez le paramètre `--run-e2e-tests`.

> [!NOTE]
> Chaque fois que vous exécutez le script **build.sh**, celui-ci supprime et recrée le dossier **build** dans le dossier racine de votre copie locale du référentiel **iot-edge**.