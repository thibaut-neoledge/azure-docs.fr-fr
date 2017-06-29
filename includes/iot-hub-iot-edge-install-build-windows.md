## <a name="install-the-prerequisites"></a>Installer les composants requis

1. Installez [Visual Studio 2015 ou 2017](https://www.visualstudio.com). Vous pouvez utiliser l’édition Community gratuite si vous remplissez les conditions de licence. Veillez à inclure Visual C++ et le gestionnaire de package NuGet.

1. Installez [git](http://www.git-scm.com) et vérifiez que vous pouvez exécuter le fichier git.exe à partir de la ligne de commande.

1. Installez [CMake](https://cmake.org/download/) et vérifiez que vous pouvez exécuter le fichier cmake.exe à partir de la ligne de commande. CMake version 3.7.2 ou supérieure est recommandé. Le programme d’installation **.msi** est l’option la plus simple sous Windows. Ajoutez CMake au chemin d’accès au moins pour l’utilisateur actif lorsque le programme d’installation vous y invite.

1. Installez [Python 2.7](https://www.python.org/downloads/release/python-27). Veillez à ajouter Python à votre variable d’environnement `PATH` dans **Panneau de configuration -> Système -> Paramètres système avancés -> Variables d’environnement**.

1. À l’invite de commande, exécutez la commande suivante pour cloner le référentiel GitHub d’Azure IoT Edge sur l’ordinateur local :

    ```cmd
    git clone https://github.com/Azure/iot-edge.git
    ```

## <a name="how-to-build-the-sample"></a>Comment créer l'exemple

Vous pouvez désormais créer le runtime et les exemples IoT Edge sur l’ordinateur local :

1. Ouvrez une **invite de commandes développeur pour VS 2015** ou une **invite de commandes développeur pour VS 2017**.

1. Accédez au dossier racine de votre copie locale du référentiel **iot-edge**.

1. Exécutez le script de génération comme suit :

    ```cmd
    tools\build.cmd --disable-native-remote-modules
    ```

Ce script crée un fichier de solution Visual Studio et génère la solution. Vous trouverez la solution Visual Studio dans le dossier **build** de votre copie locale du référentiel **iot-edge**. Pour générer et exécuter les tests unitaires, ajoutez le paramètre `--run-unittests`. Pour générer et exécuter les tests de bout en bout, ajoutez le paramètre `--run-e2e-tests`.

> [!NOTE]
> Chaque fois que vous exécutez le script **build.cmd**, celui-ci supprime et recrée le dossier **build** dans le dossier racine de votre copie locale du référentiel **iot-edge**.