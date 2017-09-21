---
title: "Publier les versions d’une API à l’aide de Gestion des API Azure | Microsoft Docs"
description: "Suivez les étapes de ce didacticiel pour apprendre à publier plusieurs versions dans Gestion des API."
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 7c355e2feb5ebe5971d8391b326422a1abec1497
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---

# <a name="publish-multiple-versions-of-your-api-in-a-predictable-way"></a>Publier plusieurs versions d’une API de manière prévisible
Ce didacticiel explique comment configurer les versions de votre API et choisir la façon dont elles sont appelées par les développeurs d’API.

## <a name="prerequisites"></a>Composants requis
Pour suivre ce didacticiel, vous devez créer un Service Gestion des API et disposer d’une API existante que vous pouvez modifier (à la place de l’API Conference).

## <a name="about-versions"></a>À propos des versions
Il est parfois peu pratique que tous les appelants de votre API utilisent exactement la même version. Parfois, vous souhaitez publier de nouvelles fonctionnalités ou des fonctionnalités différentes pour certains utilisateurs, tandis que d’autres veulent rester sur l’API qui fonctionne actuellement pour eux. Lorsque des appelants souhaitent effectuer une mise à niveau vers une version ultérieure, ils veulent être en mesure de le faire à l’aide d’une approche facile à comprendre.  Cela est possible en utilisant les **versions** dans Gestion des API Azure.

## <a name="walkthrough"></a>Procédure pas à pas
Au cours de cette procédure pas à pas, nous allons ajouter une nouvelle version à une API existante et choisir un schéma, ainsi qu’un identificateur de version.

## <a name="add-a-new-version"></a>Ajouter une nouvelle version
![Menu contextuel de l’API : ajouter une version](media/api-management-getstarted-publish-versions/AddVersionMenu.png)
1. Accédez à la page des **API** au sein du service Gestion des API sur le portail Azure.
2. Sélectionnez **API Conference** dans la liste des API, puis le menu contextuel (**... **) en regard de celle-ci.
3. Sélectionnez **+ Ajouter une version**.

    > [!TIP]
    > Il est également possible d’activer des versions lorsque vous créez une nouvelle API : sélectionnez **Créer une version pour cette API ?** sur l’écran **Ajouter une API**.

## <a name="choose-a-versioning-scheme"></a>Choisissez un schéma de contrôle de version
Gestion des API Azure vous permet de choisir la manière selon laquelle vous autorisez les appelants à spécifier la version de votre API qu’ils souhaitent. Pour cela, vous devez choisir un **schéma de contrôle des versions**. Ce schéma peut être un **chemin d’accès, un en-tête ou une chaîne de requête**. Dans notre exemple, nous utilisons le chemin d’accès.
![Écran Ajouter une version](media/api-management-getstarted-publish-versions/AddVersion.PNG)
1. Laissez le **chemin d’accès** sélectionné comme **schéma de contrôle des versions**.
2. Ajoutez **v1** comme **identificateur de version**.

    > [!TIP]
    > Si vous sélectionnez **en-tête** ou **chaîne de requête** comme schéma de contrôle de version, vous devrez fournir une valeur supplémentaire : le nom du paramètre d’en-tête ou de chaîne de requête.

3. Ajoutez la description de votre choix.
4. Sélectionnez **Créer** pour configurer votre nouvelle version.
5. Sous **API Big Conference** dans la liste des API, deux API distinctes s’affichent maintenant : **Original** et **v1**.
![Versions répertoriées sous une API dans le portail Azure](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Si vous ajoutez une version à une API sans version, nous créons toujours pour vous une API **Original** qui répond sur l’URL par défaut. Cela permet aux appelants existants de ne pas être interrompus par le processus d’ajout d’une version. Si vous créez une nouvelle API avec des versions activées au démarrage, une API Original n’est pas créée.

6. Vous pouvez maintenant modifier et configurer **v1** en tant qu’API totalement distincte de l’API **Original**. Les modifications apportées à une version n’affectent pas l’autre.

## <a name="add-the-version-to-a-product"></a>Ajouter la version à un produit
Pour que les appelants voient votre nouvelle version, celle-ci doit être ajoutée à un **produit** (les produits ne sont pas hérités des versions parentes).

1. Sélectionnez **Produits** sur la page de gestion des services.
2. Sélectionnez **Illimité**.
3. Sélectionnez **API**.
4. Sélectionnez **Ajouter**.
5. Sélectionnez **Conference API, Version v1**.
6. Revenez à la page de gestion des services et sélectionnez **API**.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Parcourir le portail des développeurs pour afficher la version
1. Sélectionnez **Portail des développeurs** dans le menu supérieur.
2. Sélectionnez **API**, notez que **API Conference** indique les versions **Original** et **v1**.
3. Sélectionnez **v1**.
4. Notez l’**URL de la demande** de la première opération de la liste. Il indique que le chemin d’accès de l’URL de l’API inclut **v1**.
![Version indiquée sur le portail des développeurs](media/api-management-getstarted-publish-versions/VersionDevPortal.PNG)

