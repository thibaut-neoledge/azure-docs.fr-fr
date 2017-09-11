---
title: "Prise en main de l’interface de ligne de commande Azure Service Fabric (sfctl)"
description: "Découvrez comment utiliser l’interface de ligne de commande Azure Service Fabric. Apprenez à vous connecter à un cluster et à gérer des applications."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 2faca2887f25b45d833dea7b2259277466290670
ms.contentlocale: fr-fr
ms.lasthandoff: 08/29/2017

---
# <a name="azure-service-fabric-command-line"></a>Ligne de commande Azure Service Fabric

L’interface de ligne de commande Azure Service Fabric (sfctl) est un utilitaire de la ligne de commande pour l’interaction et de la gestion des entités Azure Service Fabric. Sfctl peut être utilisé avec des clusters Windows ou Linux. Sfctl s’exécute sur toute plateforme prenant en charge python.

## <a name="prerequisites"></a>Composants requis

Avant l’installation, vérifiez que python et pip sont installés dans votre environnement. Pour plus d’informations, examinez la [documentation de démarrage rapide de pip](https://pip.pypa.io/en/latest/quickstart/)et la [documentation d’installation officielle de python](https://wiki.python.org/moin/BeginnersGuide/Download).

Il est recommandé d’utiliser python 3.6 même si python 2.7 et 3.6 sont tous deux pris en charge. La section suivante couvre l’installation des composants requis et de l’interface de ligne de commande.

## <a name="install-pip-python-and-sfctl"></a>Installer pip, python et sfctl

Il existe plusieurs façons d’installer pip et python sur votre plateforme, mais voici quelques étapes à suivre pour configurer rapidement python 3.6 et pip sur des systèmes d’exploitation importants :

### <a name="windows"></a>Windows

Pour Windows 10, Server 2016 et Server 2012R2, utilisez les instructions d’installation officielles standards. Par défaut, le programme d’installation de python installe aussi pip.

- Accédez à la [page officielle des téléchargements de python](https://www.python.org/downloads/) pour télécharger la dernière version de python 3.6
- Exécutez le programme d’installation
- Définissez l’option en bas de l’invite sur `Add Python 3.6 to PATH`
- Sélectionnez `Install Now`.
- Terminez l’installation

Vous devez maintenant être en mesure d’ouvrir une nouvelle fenêtre de commande pour obtenir la version de python et pip :

```bat
python --version
pip --version
```

Exécutez la commande suivante pour installer l’interface de ligne de commande Service Fabric

```
pip install sfctl
sfctl -h
```

### <a name="ubuntu"></a>Ubuntu

Pour Ubuntu 16.04 Desktop, vous pouvez installer python 3.6 à l’aide d’un PPA tiers :

À partir du terminal, exécutez les commandes suivantes :

```bash
sudo add-apt-repository ppa:jonathonf/python-3.6
sudo apt-get update
sudo apt-get install python3.6
sudo apt-get install python3-pip
```

Pour installer sfctl pour l’installation de python 3.6 uniquement, exécutez la commande suivante :

```bash
python3.6 -m pip install sfctl
sfctl -h
```

Ces étapes n’affectent pas les versions 3.5 et 2.7 de python installées sur le système. N’essayez pas de modifier ces installations, à moins de connaître Ubuntu.

### <a name="macos"></a>MacOS

Sur MacOS, il est recommandé d’utiliser le [Gestionnaire de package HomeBrew](https://brew.sh). S’il n’est pas déjà installé, exécutez la commande suivante pour installer HomeBrew :

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Installez ensuite python 3.6, pip et sfctl depuis le terminal

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

Ces étapes ne modifient pas l’installation système de python 2.7.

## <a name="cli-syntax"></a>Syntaxe d’Azure CLI

Les commandes sont toujours préfixées avec `sfctl`. Pour obtenir des informations générales sur les commandes dont vous pouvez vous servir, utilisez `sfctl -h`. Pour obtenir de l’aide avec une seule commande, utilisez `sfctl <command> -h`.

Les commandes suivent une structure renouvelée, avec la cible de la commande qui précède le verbe ou l’action :

```azurecli
sfctl <object> <action>
```

Dans cet exemple, `<object>` est la cible de `<action>`.

## <a name="select-a-cluster"></a>Sélectionner un cluster

Avant d’effectuer toute opération, vous devez sélectionner un cluster auquel vous connecter. Vous pouvez par exemple exécuter la commande suivante pour sélectionner un cluster et vous y connecter avec le nom `testcluster.com`.

> [!WARNING]
> N’utilisez pas de clusters Service Fabric non sécurisés dans un environnement de production.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

Le point de terminaison de cluster doit inclure le préfixe `http` ou `https`. Il doit inclure le port pour la passerelle HTTP. Le port et l’adresse sont identiques à l’URL de Service Fabric Explorer.

Pour les clusters qui sont sécurisés avec un certificat, vous pouvez spécifier un certificat PEM encodé. Le certificat peut être spécifié en tant que fichier unique ou en tant qu’ensemble de cert et de clé.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Pour plus d’informations, consultez [Se connecter à un cluster sécurisé](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Opérations de base

Les informations de connexion du cluster persistent dans plusieurs sessions sfctl. Une fois qu’un cluster Service Fabric est sélectionné, vous pouvez exécuter n’importe quelle commande Service Fabric sur le cluster.

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

Quelques suggestions et conseils pour la résolution des problèmes courants.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Convertir un certificat au format PFX en PEM

L’interface de ligne de commande Service Fabric prend en charge les certificats côté client en tant que fichiers PEM (extension .pem). Si vous utilisez des fichiers PFX à partir de Windows, vous devez convertir ces certificats au format PEM. Pour convertir un fichier PFX en fichier PEM, utilisez la commande suivante :

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Pour plus d’informations, consultez la [documentation OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-issues"></a>Problèmes de connexion

Certaines opérations peuvent générer le message suivant :

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Vérifiez que le point de terminaison du cluster spécifié est disponible et à l’écoute. Vérifiez également que l’interface utilisateur de Service Fabric Explorer est disponible au niveau de cet hôte et de ce port. Utilisez `sfctl cluster select` pour mettre à jour le point de terminaison.

### <a name="detailed-logs"></a>Journaux détaillés

Les journaux détaillés peuvent souvent être utiles lorsque vous déboguez ou signalez un problème. Il existe un indicateur `--debug` général qui accroît les commentaires des fichiers journaux.

### <a name="command-help-and-syntax"></a>Aide et syntaxe de commande

Pour obtenir de l’aide sur une commande spécifique ou un groupe de commandes, utilisez l’indicateur `-h` :

```azurecli
sfctl application -h
```

Autre exemple :

```azurecli
sfctl application create -h
```

## <a name="next-steps"></a>Étapes suivantes

* [Déployer une application avec l’interface de ligne de commande Azure Service Fabric](service-fabric-application-lifecycle-sfctl.md)
* [Prise en main de Service Fabric sur Linux](service-fabric-get-started-linux.md)

