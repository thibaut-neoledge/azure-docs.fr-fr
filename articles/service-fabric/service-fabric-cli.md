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
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 5ce9adf6c82e3a5521883c5de1e0689d5bf0d94e
ms.contentlocale: fr-fr
ms.lasthandoff: 08/24/2017

---
# <a name="azure-service-fabric-command-line"></a>Ligne de commande Azure Service Fabric

L’interface de ligne de commande Azure Service Fabric (sfctl) est un utilitaire de la ligne de commande pour l’interaction et de la gestion des entités Azure Service Fabric. Sfctl peut être utilisé avec des clusters Windows ou Linux. Sfctl s’exécute sur toute plateforme prenant en charge python.

## <a name="prerequisites"></a>Composants requis

Avant l’installation, vérifiez que python et pip sont installés dans votre environnement. Pour plus d’informations, examinez la [documentation de démarrage rapide de pip](https://pip.pypa.io/en/latest/quickstart/)et la [documentation d’installation officielle de python](https://wiki.python.org/moin/BeginnersGuide/Download).

Il est recommandé d’utiliser python 3.6 même si python 2.7 et 3.6 sont tous deux pris en charge.

## <a name="install"></a>Installer

L’ interface de ligne de commande Azure Service Fabric (sfctl) est empaquetée en tant que package python. Pour installer la version la plus récente, exécutez :

```bash
pip install sfctl
```

Après l’installation, exécutez `sfctl -h` pour obtenir des informations sur les commandes disponibles.

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

