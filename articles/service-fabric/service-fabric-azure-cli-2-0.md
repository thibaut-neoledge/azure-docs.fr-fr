---
title: "Prise en main de Service Fabric et d’Azure CLI 2.0"
description: "Méthode d’utilisation du module de commandes Service Fabric dans l’interface de ligne de commande Azure, version 2.0, incluant la méthode de connexion à un cluster et de gestion des applications"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: c5cc6e54acf27456185eeb48858c4d981aa46b4b
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017

---
<a id="service-fabric-and-azure-cli-20" class="xliff"></a>

# Service Fabric et Azure CLI 2.0

La nouvelle interface de ligne de commande Azure CLI 2.0 inclut désormais des commandes permettant de gérer des clusters Service Fabric. Cette documentation contient des étapes qui vous guideront dans la prise en main de cette interface.

<a id="install-azure-cli-20" class="xliff"></a>

## Installer Azure CLI 2.0

Azure CLI inclut désormais des commandes permettant d’interagir avec des clusters Service Fabric et de les gérer. Vous pouvez suivre le [processus d’installation standard](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) pour obtenir la dernière version d’Azure CLI.

Pour en savoir plus, reportez-vous à la [documentation sur Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview)

<a id="cli-syntax" class="xliff"></a>

## Syntaxe d’Azure CLI

Toutes les commandes d’Azure Service Fabric sont précédées de `az sf` dans Azure CLI. Pour en savoir plus sur les commandes disponibles, vous avez la possibilité d’exécuter `az sf -h` afin d’obtenir des informations générales. Vous pouvez également exécuter `az sf <command> -h` pour obtenir une aide détaillée sur une seule commande.

Les commandes Azure Service Fabric dans la CLI respectent un modèle de dénomination.

```azurecli
az sf <object> <action>
```

Ici, `<object>` est la cible de `<action>`.

<a id="selecting-a-cluster" class="xliff"></a>

## Sélection d’un cluster

Avant de pouvoir effectuer des opérations, vous devez sélectionner un cluster auquel vous connecter. Par exemple, consultez l’extrait de code suivant pour vous connecter à un cluster non sécurisé.

> [!WARNING]
> N’utilisez pas de clusters Service Fabric non sécurisés pour les environnements de production

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

Le point de terminaison du cluster doit être précédé de `http` ou `https` et doit inclure le port de la passerelle HTTP. Ce port et cette adresse sont identiques à l’URL de Service Fabric Explorer.

Pour sécuriser les clusters avec un certificat, les fichiers `pem`, `crt` et `key` non chiffrés sont pris en charge.

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Pour en savoir plus, consultez la [documentation détaillée sur la connexion à des clusters sécurisés](service-fabric-connect-to-secure-cluster.md).

> [!NOTE]
> La commande Select n’effectue aucune requête précédant le renvoi. Pour vérifier qu’un cluster a été correctement spécifié, exécutez une commande telle que `az sf cluster health` et vérifiez que la commande ne renvoie aucune erreur.

<a id="performing-basic-operations" class="xliff"></a>

## Effectuer des opérations de base

Les informations de connexion du cluster sont conservées dans différentes sessions d’Azure CLI. Une fois qu’un cluster Service Fabric est sélectionné, vous pouvez exécuter n’importe quelle commande Service Fabric.

Par exemple, pour obtenir l’état d’intégrité d’un cluster Service Fabric, exécutez la commande suivante :

```azurecli
az sf cluster health
```

La commande génère la sortie suivante, en supposant que la sortie JSON est spécifiée dans la configuration d’Azure CLI :

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

<a id="tips-and-faq" class="xliff"></a>

## Conseils et FAQ

Voici quelques informations qui peuvent vous être utiles lorsque vous rencontrez des problèmes lors de l’utilisation de commandes Service Fabric dans Azure CLI.

<a id="converting-a-certificate-from-pfx-to-pem" class="xliff"></a>

### Convertir un certificat PFX en PEM

Azure CLI prend en charge les certificats côté client comme les fichiers PEM (extension `.pem`). Si vous utilisez des fichiers PFX à partir de Windows, ces certificats doivent être convertis au format PEM. Pour convertir un fichier PFX en fichier PEM, utilisez la commande suivante :

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Reportez-vous à la [documentation OpenSSL](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html) pour plus d’informations.

<a id="connection-issues" class="xliff"></a>

### Problèmes de connexion

Lorsque vous effectuez des opérations, vous risquez de rencontrer l’erreur suivante :

> `Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Dans ce cas, vérifiez bien que le point de terminaison du cluster spécifié est accessible et à l’écoute. Vérifiez également que l’interface utilisateur de Service Fabric Explorer est accessible à cet hôte et à ce port. Utilisez `az sf cluster select` pour mettre à jour le point de terminaison.

<a id="getting-detailed-logs" class="xliff"></a>

### Obtenir des journaux détaillés

Lors du débogage ou du signalement d’un problème, il est utile d’inclure des journaux détaillés. Azure CLI inclut un indicateur `--debug` global qui augmente le niveau de détail des journaux.

<a id="command-help-and-syntax" class="xliff"></a>

### Aide et syntaxe de commande

Les commandes Service Fabric suivent la même convention qu’Azure CLI. Spécifiez l’indicateur `-h` afin d’obtenir de l’aide concernant une commande spécifique ou un groupe de commandes. Par exemple :

```azurecli
az sf application -h
```

ou

```azurecli
az sf application create -h
```

