---
title: "Didacticiel d’Azure Container Registry - Envoyer une image mise à jour vers les déploiements régionaux"
description: "Distribuez une image Docker modifiée vers votre registre de conteneurs Azure géorépliqué, puis déployez les modifications automatiquement sur les applications web en cours d’exécution dans plusieurs régions. Troisième partie d’une série en trois parties."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
tags: acr, azure-container-registry, geo-replication
keywords: Docker, conteneurs, registre, Azure
ms.service: container-registry
ms.devlang: 
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 05c5149ed6c8502c31539f31bfff046f98dc633d
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2017
---
# <a name="push-an-updated-image-to-regional-deployments"></a>Distribuer une image mise à jour vers les déploiements régionaux

Il s’agit de la troisième partie d’un didacticiel en trois parties. Dans le [didacticiel précédent](container-registry-tutorial-deploy-app.md), la géoréplication a été configurée pour deux déploiements d’application web régionaux différents. Dans ce didacticiel, vous allez tout d’abord modifier l’application, puis générer une image de conteneur et la distribuer à votre registre géorépliqué. Enfin, vous allez afficher la modification, déployée automatiquement par des webhooks Azure Container Registry dans les deux instances de l’application web.

Dans ce didacticiel, il s’agit de la dernière partie de la série :

> [!div class="checklist"]
> * Modification de l’application web HTML
> * Générer et marquer l’image Docker
> * Envoyer de la modification à Azure Container Registry
> * Afficher l’application mise à jour dans deux régions différentes

Si vous n’avez pas encore configuré les deux déploiements régionaux *Web App for Containers*, retournez au didacticiel précédent dans la série, [Déployer une application web à partir d’Azure Container Registry](container-registry-tutorial-deploy-app.md).

## <a name="modify-the-web-application"></a>Modification de l’application Web

Dans cette étape, apportez une modification à l’application web qui sera très visible une fois que vous enverrez l’image de conteneur mise à jour vers Azure Container Registry.

Recherchez le fichier `AcrHelloworld/Views/Home/Index.cshtml` dans la source de l’application que vous avez [clonée à partir de GitHub](container-registry-tutorial-prepare-registry.md#get-application-code) dans un didacticiel précédent et ouvrez-le dans l’éditeur de texte de votre choix. Ajoutez la ligne suivante au-dessus de la ligne `<img>` :

```html
<h1>MODIFIED</h1>
```

Votre `Index.cshtml` modifié doit ressembler à ceci :

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<h1>MODIFIED</h1>
<img width="700" src="~/images/@ViewData["MAPIMAGE"]" />
<ul>
<li>Registry URL: @ViewData["REGISTRYURL"]</li>
<li>Registry IP: @ViewData["REGISTRYIP"]</li>
<li>HostEntry: @ViewData["HOSTENTRY"]</li>
<li>Region: @ViewData["REGION"]</li>
<li>Map: @ViewData["MAPIMAGE"]</li>
</ul>
```

## <a name="rebuild-the-image"></a>Régénérer l’image

Maintenant que vous avez mis à jour l’application web, régénérez son image de conteneur. Comme précédemment, utilisez le nom d’image complet, y compris l’URL du serveur de connexion, pour la balise :

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="run-the-container-locally"></a>Exécutez localement le conteneur

Avant le déploiement sur Azure Container Registry, exécutez l’image localement afin de vérifier que la version fonctionne comme attendu.

```bash
docker run -d -p 8080:80 <acrName>.azurecr.io/acr-helloworld:v1
```

Accédez à http://localhost:8080 dans votre navigateur web pour vérifier que le conteneur est en cours d’exécution et que votre modification s’affiche.

![IMAGE DE CONTENEUR LOCAL][local-container-01]

## <a name="push-image-to-azure-container-registry"></a>Envoyer l’image à Azure Container Registry

Maintenant, envoyez l’image de conteneur *acr-helloworld* mise à jour dans le registre géorépliqué. Ici, vous exécutez une seule commande `docker push` pour déployer l’image mise à jour sur les réplicas de registre, à la fois dans les régions *Ouest des États-Unis* et *Est des États-Unis*.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Le résultat doit ressembler à ce qui suit :

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
c003ed6fc8b8: Pushed
02b11afef3fd: Layer already exists
cf17b6f921be: Layer already exists
c93ae914d31e: Layer already exists
2eea44510cee: Layer already exists
670f809bd6d5: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:e44c0956a21c91e1f5f7bc83f23f1de710c798246df1e0e508c0c88025449646 size: 1792
```

## <a name="view-the-webhook-logs"></a>Afficher les journaux du webhook

Pendant que l’image est en cours de réplication, vous pouvez voir les webhooks Azure Container Registry déclenchés.

Pour voir les webhooks régionaux qui ont été créés lorsque vous avez déployé le conteneur sur *Web Apps pour les conteneurs* dans un didacticiel précédent, accédez à votre registre de conteneurs dans le portail Azure, puis sélectionnez **Webhooks** sous **SERVICES**.

![Webhooks de registre de conteneurs dans le portail Azure][tutorial-portal-01]

Sélectionnez chaque webhook pour consulter l’historique des appels et des réponses. Vous devez voir une ligne pour l’action **push** dans les journaux des deux webhooks. Ici, le journal pour le webhook qui se trouve dans la région *Ouest des États-Unis* montre l’action **push** déclenchée par le `docker push` à l’étape précédente :

![Journal des webhooks de registre de conteneurs dans le portail Azure (Ouest des États-Unis)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>Afficher l’application web mise à jour

Les webhooks notifient les applications web qu’une nouvelle image a été envoyée au registre, qui déploie automatiquement le conteneur mis à jour pour les deux applications web régionales.

Vérifiez que l’application a été mise à jour dans les deux déploiements en accédant aux deux déploiements régionaux dans votre navigateur web. En guise de rappel, vous pouvez trouver l’URL de l’application web déployée dans le coin supérieur droit de chaque onglet de la vue d’ensemble d’App Service.

![Vue d’ensemble de App Service dans le portail Azure][tutorial-portal-03]

Pour voir l’application mise à jour, sélectionnez le lien dans la vue d’ensemble d’App Service. Voici une vue d’exemple de l’application en cours d’exécution dans la région *Ouest des États-Unis* :

![Affichage du navigateur de l’application web modifiée en cours d’exécution dans la région Ouest des États-Unis][deployed-app-westus-modified]

Vérifiez que l’image de conteneur mise à jour a également été déployée sur le déploiement *États-Unis* en l’affichant dans votre navigateur.

![Affichage du navigateur de l’application web modifiée en cours d’exécution dans la région Est des États-Unis][deployed-app-eastus-modified]

Avec un seul `docker push`, vous avez mis à jour les deux déploiements d’application Web régionaux et le registre de conteneurs Azure a distribué les images des conteneurs à partir de référentiels en réseau fermé.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez mis à jour et envoyé une nouvelle version du conteneur d’application web dans le registre géorépliqué. Les webhooks dans Azure Container Registry ont informé Web App pour conteneurs de la mise à jour, ce qui a déclenché une extraction locale à partir des registres réplicas.

Dans ce dernier didacticiel de la série, vous avez :

> [!div class="checklist"]
> * Mis à jour l’application web HTML
> * Créé et marqué l’image Docker
> * Envoyé la modification à Azure Container Registry
> * Affiché l’application mise à jour dans deux régions différentes

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png