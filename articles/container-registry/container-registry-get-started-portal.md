---
title: "Créez un registre de Docker privé dans le portail Azure | Microsoft Docs"
description: "Prise en main de la création et de la gestion de registres de conteneurs Docker privés avec le portail Azure"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: e74c5428f0e31d9d3cf06b85aa8cefde868e9d67
ms.lasthandoff: 03/27/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-portal"></a>Créez un registre de conteneur Docker privé à l’aide du portail Azure
Utilisez le portail Azure pour créer un registre de conteneur et gérer ses paramètres. Vous pouvez également créer et gérer des registres de conteneur à l’aide des [commandes de d’Azure CLI 2.0](container-registry-get-started-azure-cli.md) ou par programme avec [l’API REST](https://go.microsoft.com/fwlink/p/?linkid=834376) de Container Registry.

Pour plus d’informations, notamment sur les concepts, voir la [page de présentation](container-registry-intro.md).



## <a name="create-a-container-registry"></a>Créer un registre de conteneur
1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **+ Nouveau**.
2. Recherchez **Azure Container Registry** dans le marketplace.
3. Sélectionnez **Azure Container Registry**, avec l’éditeur **Microsoft**.
    ![Service Container Registry dans Azure Marketplace](./media/container-registry-get-started-portal/container-registry-marketplace.png)
4. Cliquez sur **Create**. Le panneau **Azure Container Registry** s’affiche.

    ![Paramètres de Container Registry](./media/container-registry-get-started-portal/container-registry-settings.png)
5. Dans le panneau **Azure Container Registry**, entrez les informations suivantes. Une fois ces opérations effectuées, cliquez sur **Créer**.

    a. **Nom du registre** : Un nom de domaine global unique et de niveau supérieur pour votre registre spécifique. Dans cet exemple, le nom de registre est *myRegistry01*, mais vous pouvez le remplacer par un nom unique de votre choix. Le nom ne peut contenir que des lettres et des chiffres.

    b. **Groupe de ressources** : sélectionnez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups) existant ou tapez le nom d’un nouveau groupe.

    c. **Emplacement** : sélectionnez un emplacement de centre de données Azure où le service est [disponible](https://azure.microsoft.com/regions/services/), tel que **Sud du centre des États-Unis**.

    d. **Utilisateur Administrateur** : si vous le souhaitez, vous pouvez autoriser l’accès au registre à un utilisateur Administrateur. Vous pouvez modifier ce paramètre une fois le registre créé.

    > [!IMPORTANT]
    > En plus de fournir l’accès via un compte d’utilisateur Admin, les registres de conteneur prennent en charge l’authentification supportée par les principaux du service Azure Active Directory. Pour plus d’informations et pour connaître les éléments à prendre en considération, consultez la section relative à [l’authentification auprès d’un conteneur](container-registry-authentication.md).


    e. **Compte de stockage** : utilisez le paramètre par défaut pour créer un [compte de stockage](../storage/storage-introduction.md), ou sélectionnez un compte de stockage existant dans le même emplacement. Pour l’instant, l’offre Stockage Premium n’est pas prise en charge.


## <a name="manage-registry-settings"></a>Gérer les paramètres du registre
Après avoir créé le registre, recherchez ses paramètres en commençant par le panneau des **Registres de conteneur** dans le portail. Par exemple, vous aurez peut-être besoin des paramètres pour vous connecter à votre registre, ou vous souhaiterez peut-être activer ou désactiver l’utilisateur Admin.

1. Dans le panneau **Registres de conteneur**, cliquez sur le nom de votre registre.

    ![Panneau Container Registry](./media/container-registry-get-started-portal/container-registry-blade.png)
2. Pour gérer les paramètres d’accès, cliquez sur **Clé d’accès**.

    ![Accès à Container Registry](./media/container-registry-get-started-portal/container-registry-access.png)
3. Notez les points suivants :

   * **Serveur de connexion** : le nom complet à utiliser pour se connecter au registre. Dans cet exemple, il s’agit de `myregistry01.azurecr.io`.
   * **Utilisateur Admin** : bascule pour activer ou désactiver le compte d’utilisateur Admin du registre.
   * **Nom d’utilisateur** et **Mot de passe** : informations d’identification du compte d’utilisateur Admin (si activé) que vous pouvez utiliser pour vous connecter au registre. Vous pouvez éventuellement régénérer les mots de passe. Deux mots de passe sont créés pour que vous puissiez maintenir des connexions vers le Registre à l’aide d’un mot de passe, tandis que vous régénérez l’autre mot de passe. Si vous préférez une authentification avec un principal de service, consultez [S’authentifier avec un registre de conteneurs Docker](container-registry-authentication.md).

## <a name="next-steps"></a>Étapes suivantes
* [Effectuer un push de votre première image à l’aide de l’interface CLI Docker](container-registry-get-started-docker-cli.md)

