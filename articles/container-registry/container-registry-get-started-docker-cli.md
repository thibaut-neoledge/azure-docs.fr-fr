---
title: "Transmission push de l’image Docker au Registre Azure privé | Microsoft Docs"
description: "Transmission et extraction des images Docker à un Registre de conteneur privé dans Azure à l’aide de l’interface de ligne de commande (CLI) Docker"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 64fbe43f-fdde-4c17-a39a-d04f2d6d90a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: b6c26f28aa1e574ba3aabe53eda359cb6bf2edcc
ms.lasthandoff: 03/27/2017

---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Transmission de votre première image vers un Registre de conteneur Docker privé à l’aide de l’interface de ligne de commande (CLI) Docker
Un Registre de conteneur Azure stocke et gère les images privées du conteneur [Docker](http://hub.docker.com), de la même manière que [Docker Hub](https://hub.docker.com/) stocke les images publiques du Docker. Vous pouvez utiliser [l’interface de ligne de commande Docker](https://docs.docker.com/engine/reference/commandline/cli/) (interface CLI Docker) pour la [connexion](https://docs.docker.com/engine/reference/commandline/login/), la [transmission](https://docs.docker.com/engine/reference/commandline/push/), [l’extraction](https://docs.docker.com/engine/reference/commandline/pull/) et d’autres opérations sur le Registre du conteneur.

Pour plus d’informations, notamment sur les concepts, voir la [page de présentation](container-registry-intro.md)



## <a name="prerequisites"></a>Composants requis
* **Azure Container Registry** : créez un Registre de conteneur dans votre abonnement Azure. Par exemple, utilisez le [portail Azure](container-registry-get-started-portal.md) ou [Azure CLI 2.0](container-registry-get-started-azure-cli.md).
* **Interface CLI Docker** : configurez votre ordinateur local comme un hôte Docker et accédez aux commandes de l’interface CLI Docker, installez le [moteur Docker](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Se connecter à un Registre
Exécutez `docker login` pour vous connecter à votre Registre de conteneur à l’aide de vos [informations d’identification du Registre](container-registry-authentication.md).

L’exemple suivant transmet l’ID et le mot de passe d’un [principal du service](../active-directory/active-directory-application-objects.md) Azure Active Directory . Par exemple, vous pouvez avoir affecté un principal du service à votre Registre pour un scénario d’automatisation.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

> [!TIP]
> Veillez à spécifier le nom de Registre qualifié complet (en minuscules). Dans cet exemple, il s’agit de `myregistry.azurecr.io`.

## <a name="steps-to-pull-and-push-an-image"></a>Étapes d’extraction et de transmission d’une image
L’exemple suivant télécharge l’image Nginx à partir du Registre Docker Hub public, la balise pour votre Registre de conteneur Azure privé, la transmet à votre Registre, puis l’extrait à nouveau.

**1. Extraire l’image officielle de Docker pour Nginx**

Tout d’abord, extrayez l’image Nginx publique de votre ordinateur local.

```
docker pull nginx
```
**2. Démarrer le conteneur Nginx**

La commande suivante démarre le conteneur Nginx local interactivement sur le port 8080, ce qui vous permet de voir le résultat depuis Nginx. Il supprime le conteneur en cours d’exécution une fois arrêté.

```
docker run -it --rm -p 8080:80 nginx
```

Accédez à [http://localhost : 8080](http://localhost:8080) pour afficher le conteneur en cours d’exécution. Vous voyez un écran semblable au suivant.

![Nginx sur un ordinateur local](./media/container-registry-get-started-docker-cli/nginx.png)

Pour arrêter le conteneur en cours d’exécution, appuyez sur [CTRL] + [C].

**3. Créer un alias de l’image dans votre Registre**

La commande suivante crée un alias de l’image, avec un chemin d’accès qualifié complet à votre Registre. Cet exemple spécifie l’espace de noms `samples` pour éviter l’encombrement à la racine du Registre.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```  

**4. Envoyer l’image vers votre Registre**

```
docker push myregistry.azurecr.io/samples/nginx
```

**5. Extraire l’image de votre Registre**

```
docker pull myregistry.azurecr.io/samples/nginx
```

**6. Démarrer le conteneur Nginx à partir de votre Registre**

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Accédez à [http://localhost : 8080](http://localhost:8080) pour afficher le conteneur en cours d’exécution.

Pour arrêter le conteneur en cours d’exécution, appuyez sur [CTRL] + [C].

**7. (Facultatif) Retirer l’image**

```
docker rmi myregistry.azurecr.io/samples/nginx
```

##<a name="concurrent-limits"></a>Limites simultanées
Dans certains scénarios, l’exécution des appels simultanément peut générer des erreurs. Le tableau suivant contient les limites des appels simultanés avec des opérations « Push » et « Pull » sur le Registre de conteneur Azure :

| Opération  | Limite                                  |
| ---------- | -------------------------------------- |
| PULL       | Jusqu’à 10 opérations Pull simultanées par Registre |
| PUSH       | Jusqu’à 5 opérations Push simultanées par Registre |

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous connaissez les principes de base, vous êtes prêt à utiliser votre Registre. Par exemple, commencez à déployer les images du conteneur vers un cluster [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/).

