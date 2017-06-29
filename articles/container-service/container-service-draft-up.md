---
title: Utiliser Draft avec Azure Container Service et Azure Container Registry | Microsoft Docs
description: "Créez un cluster Kubernetes ACS et un service Azure Container Registry pour générer votre première application dans Azure avec Draft."
services: container-service
documentationcenter: 
author: squillace
manager: gamonroy
editor: 
tags: draft, helm, acs, azure-container-service
keywords: Docker, Containers, microservices, Kubernetes, Draft, Azure
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: rasquill
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 20619fd21f376afee95facb688e35534f5979b90
ms.contentlocale: fr-fr
ms.lasthandoff: 06/03/2017



---

# <a name="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>Utiliser Draft avec Azure Container Service et Azure Container Registry pour générer et déployer une application dans Kubernetes

[Draft](https://aka.ms/draft) est un nouvel outil open source qui facilite le développement d’applications basées sur des conteneurs et leur déploiement dans des clusters Kubernetes sans nécessiter une connaissance approfondie de Docker et Kubernetes, ni même l’installation de ces outils. L’utilisation d’outils tels que Draft vous permet, à vous et votre équipe, de vous concentrer sur la génération de l’application avec Kubernetes sans avoir à accorder autant d’attention à l’infrastructure.

Vous pouvez utiliser Draft avec n’importe quel registre d’images Docker et n’importe quel cluster Kubernetes, y compris au niveau local. Ce didacticiel vous indique comment utiliser Azure Container Service (ACS) avec Kubernetes, Azure Container Registry (ACR) et Azure DNS pour créer un pipeline développeur CI/CD en direct à l’aide de Draft.


## <a name="create-an-azure-container-registry"></a>Création d’un Azure Container Registry
Vous pouvez facilement [créer un service Azure Container Registry](../container-registry/container-registry-get-started-azure-cli.md), mais les étapes requises sont les suivantes :

1. Créez un groupe de ressources Azure pour gérer votre registre ACR et le cluster Kubernetes dans ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Créez un registre d’images ACR à l’aide de la commande [az acr create](/cli/azure/acr#create).
      ```azurecli
      az acr create -g draft -n draftacs --sku Basic --admin-enabled true -l eastus
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Créer un service Azure Container Service avec Kubernetes

Vous voici prêt à utiliser [az acs create](/cli/azure/acs#create) pour créer un cluster ACS à l’aide de Kubernetes en tant que valeur `--orchestrator-type`.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes
```

> [!NOTE]
> Étant donné que Kubernetes n’est pas le type d’orchestrator par défaut, veillez à utiliser le commutateur `--orchestrator-type kubernetes`.

Lorsque l’opération a réussi, la sortie ressemble à ce qui suit.

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

À présent que vous disposez d’un cluster, vous pouvez importer les informations d’identification à l’aide de la commande [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). Vous possédez maintenant un fichier de configuration local pour votre cluster, ce fichier étant requis pour l’exécution des opérations Helm et Draft.

## <a name="install-and-configure-draft"></a>Installer et configurer Draft
Les instructions d’installation de Draft figurent dans le [référentiel Draft](https://github.com/Azure/draft/blob/master/docs/install.md). Ces instructions sont relativement simples, mais nécessitent certaines tâches de configuration, car la procédure dépend de [Helm](https://aka.ms/helm) pour la création et le déploiement d’un graphique Helm dans le cluster Kubernetes.

1. [Téléchargez et installez Helm](https://aka.ms/helm#install).
2. Utilisez Helm pour rechercher et installer `stable/traefik`, ainsi que le contrôleur d’entrée pour autoriser les demandes entrantes pour vos builds.
    ```bash
    $ helm search traefik
    NAME              VERSION    DESCRIPTION
    stable/traefik    1.2.1-a    A Traefik based Kubernetes ingress controller w...

    $ helm install stable/traefik --name ingress
    ```
    À présent, définissez un espion sur le contrôleur `ingress` pour capturer la valeur IP externe lors de son déploiement. Cette adresse IP correspondra à celle [mappée sur votre domaine de déploiement](#wire-up-deployment-domain) à la section suivante.

    ```bash
    kubectl get svc -w
    NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
    ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
    kubernetes                    10.0.0.1       <none>          443/TCP                      7h
    ```

    Dans ce cas précis, l’adresse IP externe du domaine de déploiement est `13.64.108.240`. Maintenant, vous pouvez mapper votre domaine sur cette adresse IP.

## <a name="wire-up-deployment-domain"></a>Lier le domaine de déploiement

Draft crée une version pour chaque graphique Helm qu’il génère, autrement dit pour chaque application sur laquelle vous travaillez. Chacune d’elles obtient un nom généré qui est utilisé par Draft en tant que _sous-domaine_ au-dessus du _domaine de déploiement_ racine que vous contrôlez. (Dans cet exemple, nous utilisons `squillace.io` comme domaine de déploiement.) Pour autoriser ce comportement de sous-domaine, vous devez créer un enregistrement A pour `'*'` dans vos entrées DNS pour votre domaine de déploiement, afin que chaque sous-domaine généré soit routé vers le contrôleur d’entrée du cluster Kubernetes.

Votre fournisseur de domaine dispose de sa propre méthode pour affecter des serveurs DNS ; pour [déléguer vos serveurs de noms de domaine à Azure DNS](../dns/dns-delegate-domain-azure-dns.md), procédez comme suit :

1. Créez un groupe de ressources pour votre zone.
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. Créez une zone DNS pour votre domaine.
Utilisez la commande [az network dns zone create](/cli/azure/network/dns/zone#create) pour obtenir les serveurs de noms afin de déléguer le contrôle DNS à Azure DNS pour votre domaine.
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. Ajoutez les serveurs DNS qui vous sont affectés au fournisseur de domaine de votre domaine de déploiement, ce qui vous permet d’utiliser Azure DNS pour repointer votre domaine à votre convenance.
4. Créez une entrée de jeu d’enregistrements A pour le mappage de votre domaine de déploiement sur l’adresse IP `ingress` à l’étape 2 de la section précédente.
    ```azurecli
    az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*' -g squillace.io -z squillace.io
    ```
Le résultat ressemble à ceci :
    ```json
    {
      "arecords": [
        {
          "ipv4Address": "13.64.108.240"
        }
      ],
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
      "metadata": null,
      "name": "*",
      "resourceGroup": "squillace.io",
      "ttl": 3600,
      "type": "Microsoft.Network/dnszones/A"
    }
    ```

5. Configurez Draft pour l’utilisation de votre registre et la création de sous-domaines pour chaque graphique Helm qu’il génère. Pour configurer Draft, vous devez disposer des éléments suivants :
  - Nom de votre service Azure Container Registry (dans cet exemple, `draftacs`)
  - Votre clé de Registre, ou mot de passe, à partir de `az acr credential show -n $acrname --output tsv --query "passwords[0].value"`
  - Domaine de déploiement racine que vous avez mappé sur l’adresse IP externe d’entrée de Kubernetes (ici, `13.64.108.240`)

  Vous pouvez utiliser ces valeurs pour créer la valeur à codage base 64 de la chaîne JSON de configuration, `{"username":"<user>","password":"<secret>","email":"email@example.com"}`. Voici l’une des manières de coder la valeur (vous devez remplacer les valeurs de cet exemple par vos propres valeurs).
      ```bash
      acrname="draftacs"
      password=$(az acr credential show -n $acrname --output tsv --query "passwords[0].value")
      authtoken=$(echo \{\"username\":\"$acrname\",\"password\":\"$password\",\"email\":\"rasquill@microsoft.com\"\} | base64)
      ```

  Vous pouvez vérifier l’exactitude de la chaîne JSON en tapant `echo $authtoken | base64 -D` pour afficher le résultat non codé.
  À présent, initialisez Draft avec cette commande et cet argument de configuration pour l’option `-set` :
      ```bash
      draft init --set registry.url=$acrname.azurecr.io,registry.org=$acrname,registry.authtoken=$authtoken,basedomain=squillace.io
      ```
      > [!NOTE]
      > Il est facile d’oublier que la valeur `basedomain` est le domaine de déploiement de base que vous contrôlez et que vous avez configuré comme devant pointer vers l’adresse IP externe d’entrée.

Vous voici prêt à déployer une application.


## <a name="build-and-deploy-an-application"></a>Générer et déployer une application

Le référentiel Draft comporte [six exemples d’applications simples](https://github.com/Azure/draft/tree/master/examples). Clonez le référentiel, puis utilisons [l’exemple Python](https://github.com/Azure/draft/tree/master/examples/python). Accédez au répertoire examples/Python, puis tapez `draft create` pour générer l’application. La sortie doit ressembler à l’exemple qui suit.
```bash
$ draft create
--> Python app detected
--> Ready to sail
```

La sortie inclut un fichier Dockerfile et un graphique Helm. Pour générer et déployer l’application, il vous suffit de taper `draft up`. La sortie est volumineuse, mais commence comme dans l’exemple suivant
```bash
$ draft up
--> Building Dockerfile
Step 1 : FROM python:onbuild
onbuild: Pulling from library/python
10a267c67f42: Pulling fs layer
fb5937da9414: Pulling fs layer
9021b2326a1e: Pulling fs layer
dbed9b09434e: Pulling fs layer
ea8a37f15161: Pulling fs layer
<snip>
```

et, lorsque l’opération a réussi, doit se terminer par un code semblable à ce qui suit.
```bash
ab68189731eb: Pushed
53c0ab0341bee12d01be3d3c192fbd63562af7f1: digest: sha256:bb0450ec37acf67ed461c1512ef21f58a500ff9326ce3ec623ce1e4427df9765 size: 2841
--> Deploying to Kubernetes
--> Status: DEPLOYED
--> Notes:

  http://gangly-bronco.squillace.io to access your application

Watching local files for changes...
```

Quel que soit le nom de votre graphique, vous pouvez désormais utiliser la commande `curl http://gangly-bronco.squillace.io` pour recevoir la réponse, `Hello World!`.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un cluster Kubernetes ACS, vous pouvez utiliser [Azure Container Registry](../container-registry/container-registry-intro.md) pour découvrir comment créer d’autres déploiements distincts de ce scénario. Par exemple, vous pouvez créer un jeu d’enregistrements DNS de domaine draft._basedomain.toplevel_ qui contrôle les éléments d’un sous-domaine plus profond pour des déploiements ACS spécifiques.







