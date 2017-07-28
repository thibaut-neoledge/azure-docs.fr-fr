---
title: "Prise en main de Microsoft Azure Service Fabric et d’Azure CLI 2.0"
description: "Découvrez comment utiliser le module de commandes de Microsoft Azure Service Fabric dans Azure CLI, version 2.0. Apprenez à vous connecter à un cluster et à gérer des applications."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: ee3302b984ca2f5509755dc17b0a5fd06ace0afe
ms.contentlocale: fr-fr
ms.lasthandoff: 07/14/2017

---
# <a name="azure-service-fabric-and-azure-cli-20"></a>Microsoft Azure Service Fabric et Azure CLI 2.0

L’outil en ligne de commande Azure (Azure CLI) version 2.0 comprend des commandes permettant de gérer les clusters Microsoft Azure Service Fabric. Apprenez à prendre en main Azure CLI et Service Fabric.

## <a name="install-azure-cli-20"></a>Installer Azure CLI 2.0

Vous pouvez utiliser les commandes Azure CLI 2.0 pour interagir avec des clusters Service Fabric et les gérer. Pour obtenir la dernière version d’Azure CLI, suivez les [processus d’installation standard Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

Pour plus d’informations, consultez la [présentation d’Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview).

## <a name="azure-cli-syntax"></a>Syntaxe d’Azure CLI

Dans Azure CLI, toutes les commandes Service Fabric incluent le préfixe `az sf`. Pour obtenir des informations générales sur les commandes dont vous pouvez vous servir, utilisez `az sf -h`. Pour obtenir de l’aide avec une seule commande, utilisez `az sf <command> -h`.

Les commandes Service Fabric dans Azure CLI respectent le modèle d’affectation de noms suivant :

```azurecli
az sf <object> <action>
```

`<object>` est la cible de `<action>`.

## <a name="select-a-cluster"></a>Sélectionner un cluster

Avant d’effectuer toute opération, vous devez sélectionner un cluster auquel vous connecter. Pour obtenir un exemple, consultez le code suivant. Le code se connecte à un cluster non sécurisé.

> [!WARNING]
> N’utilisez pas de clusters Service Fabric non sécurisés dans un environnement de production.

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

Le point de terminaison de cluster doit inclure le préfixe `http` ou `https`. Il doit inclure le port pour la passerelle HTTP. Le port et l’adresse sont identiques à l’URL de Service Fabric Explorer.

Pour les clusters qui sont sécurisés avec un certificat, vous pouvez utiliser des fichiers non chiffrés .pem ou des fichiers .crt et .key. Par exemple :

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Pour plus d’informations, consultez [Se connecter à un cluster sécurisé](service-fabric-connect-to-secure-cluster.md).

> [!NOTE]
> La commande `select` n’agit pas sur les demandes avant d’être renvoyée. Pour vérifier que vous avez correctement spécifié un cluster, utilisez une commande similaire à `az sf cluster health`. Vérifiez que la commande ne renvoie pas d’erreurs.

## <a name="basic-operations"></a>Opérations de base

Les informations de connexion du cluster sont conservées dans plusieurs sessions d’Azure CLI. Une fois qu’un cluster Service Fabric est sélectionné, vous pouvez exécuter n’importe quelle commande Service Fabric sur le cluster.

Par exemple, pour obtenir l’état d’intégrité d’un cluster Service Fabric, utilisez la commande suivante :

```azurecli
az sf cluster health
```

La commande génère la sortie suivante (en supposant que la sortie JSON est spécifiée dans la configuration d’Azure CLI) :

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

Vous estimerez peut-être les informations suivantes utiles si vous rencontrez des problèmes en utilisant les commandes Service Fabric dans Azure CLI.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Convertir un certificat au format PFX en PEM

Azure CLI prend en charge les certificats côté client comme les fichiers PEM (extension .pem). Si vous utilisez des fichiers PFX à partir de Windows, vous devez convertir ces certificats au format PEM. Pour convertir un fichier PFX en fichier PEM, utilisez la commande suivante :

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Pour plus d’informations, consultez la [documentation OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-issues"></a>Problèmes de connexion

Certaines opérations peuvent générer le message suivant :

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Vérifiez que le point de terminaison du cluster spécifié est disponible et à l’écoute. Vérifiez également que l’interface utilisateur de Service Fabric Explorer est disponible au niveau de cet hôte et de ce port. Utilisez `az sf cluster select` pour mettre à jour le point de terminaison.

### <a name="detailed-logs"></a>Journaux détaillés

Les journaux détaillés peuvent souvent être utiles lorsque vous déboguez ou signalez un problème. Azure CLI propose un indicateur `--debug` global qui augmente le niveau de détail des fichiers journaux.

### <a name="command-help-and-syntax"></a>Aide et syntaxe de commande

Les commandes Service Fabric suivent les mêmes conventions qu’Azure CLI. Pour obtenir de l’aide sur une commande spécifique ou un groupe de commandes, utilisez l’indicateur `-h` :

```azurecli
az sf application -h
```

Voici un autre exemple :

```azurecli
az sf application create -h
```

