---
title: "Connecter une application Web à un réseau de distribution de contenu | Microsoft Docs"
description: "Connecter une application Web à un réseau de distribution de contenu pour distribuer vos fichiers statiques à partir des nœuds de périmètre."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: a12eab1f7bc4177f659771d58a58f749507c994c
ms.lasthandoff: 04/12/2017

---
# <a name="connect-a-web-app-to-a-content-delivery-network"></a>Connecter une application web à un réseau de distribution de contenu

Dans ce didacticiel, vous allez créer un profil Azure CDN et un point de terminaison Azure CDN pour traiter les fichiers statiques à partir de votre application web via les emplacements POP Azure CDN.

> [!TIP]
> Passez en revue la liste à jour des [Emplacements POP des CDN Azure](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations).
>

## <a name="step-1---login-to-azure-portal"></a>Étape 1 : Connectez-vous au portail Azure

Tout d’abord, ouvrez votre navigateur préféré et accédez au [Portail](https://portal.azure.com) Azure.

## <a name="step-2---create-a-cdn-profile"></a>Étape 2 : Créez un profil CDN

Cliquez sur le bouton `+ New` situé dans le volet de navigation de gauche, cliquez sur **Web et mobilité**. Sous la catégorie Web et mobilité, sélectionnez **CDN**.

Spécifiez le **Nom**, l’**Emplacement**, le **Groupe de ressources** et le **Niveau tarifaire**, puis cliquez sur **Créer**.

Ouvrez le concentrateur de groupes de ressources à partir de la barre de navigation de gauche, sélectionnez **myResourceGroup**. Dans la liste des ressources, sélectionnez **myCDNProfile**.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>Étape 3 : Créer un point de terminaison CDN

Cliquez sur `+ Endpoint` à partir des commandes à proximité de la zone de recherche, cette action lance le panneau de création de point de terminaison.

Spécifiez le **Nom**, le **Type d’origine**, le **Nom d’hôte d’origine**, puis cliquez sur **Ajouter**.

Le point de terminaison sera créé, une fois que le point de terminaison du réseau de distribution de contenu est créé le statut sera mis à jour sur **en cours d’exécution**.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---leveraging-cdn"></a>Étape 4 - Utilisation des CDN

Maintenant que le point de terminaison est en cours d’exécution, nous allons valider que le contenu est disponible sur le serveur POP en accédant à un fichier statique sur le serveur web, puis en modifiant le nom d’hôte de `http://<app_name>.azurewebsites.net/path/to-static-file` à `http://<endpoint_name>.azureedge.net/path/to-static-file`.

![app-service-web-url-to-cdn-endpoint-url](media/app-service-web-tutorial-content-delivery-network/app-service-web-url-to-cdn-endpoint-url.png)

## <a name="next-steps"></a>Étapes suivantes


