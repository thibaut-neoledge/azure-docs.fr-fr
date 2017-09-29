---
title: "Prise en main de l’interface de ligne de commande Azure Service Fabric"
description: "Découvrez comment utiliser l’interface de ligne de commande Azure Service Fabric. Découvrez comment se connecter à un cluster et gérer des applications."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: f246ee8aaecf3a398182debdea07832c75c1bd9c
ms.contentlocale: fr-fr
ms.lasthandoff: 09/22/2017

---
# <a name="azure-service-fabric-cli"></a>Interface de ligne de commande Azure Service Fabric

L’interface de ligne de commande Azure Service Fabric est un utilitaire de ligne de commande pour interagir avec des entités Service Fabric, et les gérer. L’interface de ligne de commande Service Fabric peut être utilisée avec des clusters Windows ou Linux. L’interface de ligne de commande Service Fabric s’exécute sur toute plateforme prenant en charge Python.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="prerequisites"></a>Prérequis

Avant l’installation, vérifiez que Python et pip sont installés dans votre environnement. Pour plus d’informations, consultez la [documentation de démarrage rapide de pip](https://pip.pypa.io/en/latest/quickstart/) et la [documentation d’installation officielle de Python](https://wiki.python.org/moin/BeginnersGuide/Download).

Bien que les Python 2.7 et 3.6 soient tous deux pris en charge, nous vous recommandons d’utiliser Python 3.6. La section suivante couvre l’installation de tous les prérequis et de l’interface de ligne de commande.

## <a name="install-pip-python-and-the-service-fabric-cli"></a>Installer pip, Python et l’interface de ligne de commande Service Fabric

 Il existe différentes façons d’installer pip et Python sur votre plateforme. Voici quelques étapes pour configurer rapidement Python 3.6 et pip sur les principaux systèmes d’exploitation.

### <a name="windows"></a>Windows

Pour Windows 10, Windows Server 2016 et Windows Server 2012 R2, utilisez les instructions d’installation officielles standard. Par défaut, le programme d’installation de Python installe aussi pip.

1. Accédez à la [page officielle des téléchargements de Python](https://www.python.org/downloads/) pour télécharger la dernière version de Python 3.6.

2. Démarrez le programme d’installation.

3. Au bas de l’invite, sélectionnez **Ajouter Python 3.6 au chemin d’accès**.

4. Sélectionnez **Installer maintenant**et terminez l’installation.

Vous pouvez maintenant ouvrir une nouvelle fenêtre de commande et obtenir la version de Python et celle de pip.

```bat
python --version
pip --version
```

Exécutez ensuite la commande suivante pour installer l’interface de ligne de commande Service Fabric :

```
pip install sfctl
sfctl -h
```

Si vous rencontrez une erreur indiquant que `sfctl` est introuvable, exécutez les commandes suivantes :

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .bashrc
```

### <a name="ubuntu"></a>Ubuntu

Pour Ubuntu 16.04 Desktop, vous pouvez installer Python 3.6 à l’aide d’une archive de package personnel tiers (PPA).

À partir du terminal, exécutez les commandes suivantes :

```bash
sudo add-apt-repository ppa:jonathonf/python-3.6
sudo apt-get update
sudo apt-get install python3.6
sudo apt-get install python3-pip
```

Ensuite, pour installer l’interface de ligne de commande Service Fabric uniquement pour votre installation de Python 3.6, exécutez la commande suivante :

```bash
python3.6 -m pip install sfctl
sfctl -h
```

Si vous rencontrez une erreur indiquant que `sfctl` est introuvable, exécutez les commandes suivantes :

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .bashrc
```

Ces étapes n’affectent pas l’installation du système de Python 3.5 et 2.7. N’essayez pas de modifier ces installations, à moins de connaître Ubuntu.

### <a name="macos"></a>MacOS

Pour MacOS, nous vous recommandons d’utiliser le [Gestionnaire de package HomeBrew](https://brew.sh). Si HomeBrew n’est pas déjà installé, installez-le en exécutant la commande suivante :

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Ensuite, à partir du terminal, installez Python 3.6, pip et l’interface de ligne de commande Service Fabric en exécutant les commandes suivantes :

```bash
brew install python3
pip3 install sfctl
sfctl -h
```


Si vous rencontrez une erreur indiquant que `sfctl` est introuvable, exécutez les commandes suivantes :

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .bashrc
```


Ces étapes ne modifient pas l’installation système de Python 2.7.

## <a name="cli-syntax"></a>Syntaxe d’Azure CLI

Les commandes portent toujours le préfixe `sfctl`. Pour obtenir des informations générales sur toutes les commandes dont vous pouvez vous servir, utilisez `sfctl -h`. Pour obtenir de l’aide avec une seule commande, utilisez `sfctl <command> -h`.

Les commandes suivent une structure répétable, où la cible de la commande précède le verbe ou l’action.

```azurecli
sfctl <object> <action>
```

Dans cet exemple, `<object>` est la cible de `<action>`.

## <a name="select-a-cluster"></a>Sélectionner un cluster

Avant d’effectuer toute opération, vous devez sélectionner un cluster auquel vous connecter. Vous pouvez par exemple exécuter la commande suivante pour sélectionner le cluster nommé `testcluster.com` et vous y connecter :

> [!WARNING]
> N’utilisez pas de clusters Service Fabric non sécurisés dans un environnement de production.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

Le point de terminaison de cluster doit inclure le préfixe `http` ou `https`. Il doit inclure le port pour la passerelle HTTP. Le port et l’adresse sont identiques à l’URL de Service Fabric Explorer.

Pour les clusters sécurisés avec un certificat, vous pouvez spécifier un certificat codé PEM. Le certificat peut être spécifié en tant que fichier unique ou en tant que certificat et paire de clé. S’il s’agit d’un certificat auto-signé qui n’est pas signé par une autorité de certification, vous pouvez passer l’option `--no-verify` pour ignorer la vérification de l’autorité de certification.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Pour plus d’informations, consultez [Se connecter à un cluster sécurisé](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Opérations de base

Les informations de connexion du cluster sont conservées dans plusieurs sessions de l’interface de ligne de commande Service Fabric. Une fois qu’un cluster Service Fabric est sélectionné, vous pouvez exécuter n’importe quelle commande Service Fabric sur le cluster.

Par exemple, pour obtenir l’état d’intégrité d’un cluster Service Fabric, utilisez la commande suivante :

```azurecli
sfctl cluster health
```

La commande débouche sur le résultat suivant :

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>Conseils et résolution des problèmes

Voici quelques suggestions et conseils pour résoudre les problèmes courants.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Convertir un certificat au format PFX en PEM

L’interface de ligne de commande Service Fabric prend en charge les certificats côté client en tant que fichiers PEM (extension .pem). Si vous utilisez des fichiers PFX à partir de Windows, vous devez convertir ces certificats au format PEM. Pour convertir un fichier PFX en fichier PEM, utilisez la commande suivante :

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

De même, pour convertir un fichier PEM en fichier PFX, vous pouvez utiliser la commande suivante (aucun mot de passe n’est fourni ici) :

```bash
openssl  pkcs12 -export -out Certificates.pfx -inkey Certificates.pem -in Certificates.pem -passout pass:'' 
```

Pour plus d’informations, consultez la [documentation OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-problems"></a>Problèmes de connexion

Certaines opérations peuvent générer le message suivant :

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Vérifiez que le point de terminaison du cluster spécifié est disponible et à l’écoute. Vérifiez également que l’interface utilisateur de Service Fabric Explorer est disponible au niveau de cet hôte et de ce port. Utilisez `sfctl cluster select` pour mettre à jour le point de terminaison.

### <a name="detailed-logs"></a>Journaux détaillés

Les journaux détaillés sont souvent utiles lorsque vous déboguez ou signalez un problème. Un indicateur global `--debug` accroît les commentaires des fichiers journaux.

### <a name="command-help-and-syntax"></a>Aide et syntaxe de commande

Pour obtenir de l’aide sur une commande spécifique ou un groupe de commandes, utilisez l’indicateur `-h`.

```azurecli
sfctl application -h
```

Voici un autre exemple :

```azurecli
sfctl application create -h
```

## <a name="updating-the-service-fabric-cli"></a>Mettre à jour l’interface de ligne de commande Service Fabric 

Pour mettre à jour l’interface de ligne de commande Service Fabric, exécutez les commandes suivantes (remplacez `pip` par `pip3` selon ce que vous avez choisi lors de l’installation initiale) :

```bash
pip uninstall sfctl 
pip install sfctl 
```


## <a name="next-steps"></a>Étapes suivantes

* [Déployer une application avec l’interface de ligne de commande Azure Service Fabric](service-fabric-application-lifecycle-sfctl.md)
* [Prise en main de Service Fabric sur Linux](service-fabric-get-started-linux.md)

