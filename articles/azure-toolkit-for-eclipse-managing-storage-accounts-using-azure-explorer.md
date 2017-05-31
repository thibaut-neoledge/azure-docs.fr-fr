---
title: "Gestion des comptes de stockage avec l’Explorateur Azure pour Eclipse | Microsoft Docs"
description: "Découvrez comment gérer vos comptes de stockage Azure à l’aide de l’Explorateur Azure pour Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 5b3014b5aca368be8ea46863c83665abde10fed5
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017


---

# <a name="manage-storage-accounts-by-using-the-azure-explorer-for-eclipse"></a>Gérer des comptes de stockage à l’aide de l’Explorateur Azure pour Eclipse

L’Explorateur Azure, qui fait partie du kit de ressources Azure pour Eclipse, fournit aux développeurs Java une solution facile à utiliser pour gérer les comptes de stockage de leur compte Azure à partir de l’environnement de développement intégré (IDE) Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="create-a-storage-account-in-eclipse"></a>Créer un compte de stockage dans Eclipse

Pour créer un compte de stockage à l’aide de l’Explorateur Azure, procédez comme suit :

1. Connectez-vous à votre compte Azure en suivant les [Instructions de connexion pour le kit de ressources Azure pour Eclipse].

2. Dans la vue **Explorateur Azure**, développez le nœud **Azure**, cliquez avec le bouton droit sur **Comptes de stockage** puis cliquez sur **Créer un compte de stockage**.

   ![Commande Créer un compte de stockage][CS01]

3. Dans la boîte de dialogue **Créer un compte de stockage**, spécifiez les options suivantes :

   ![Boîte de dialogue Créer un compte de stockage][CS02]

   * **Nom** : spécifie le nom du nouveau compte de stockage.

   * **Abonnement** : spécifie l’abonnement Azure que vous voulez utiliser pour le nouveau compte de stockage.

   * **Groupe de ressources** : spécifie le groupe de ressources pour votre machine virtuelle. Sélectionnez l’une des options suivantes :
      * **Créer** : spécifie que vous souhaitez créer un groupe de ressources.
      * **Utiliser l’existant** : spécifie que vous allez opérer un choix dans une liste de groupes de ressources associés à votre compte Azure.

   * **Région** : spécifie l’emplacement où votre compte de stockage sera créé, par exemple « États-Unis de l’Ouest ».

   * **Type de compte** : spécifie le type de compte de stockage à créer, par exemple « Stockage Blob ». Pour plus d’informations, consultez la rubrique [À propos des comptes de stockage Azure].

   * **Performances** : spécifie l’offre de compte de stockage de l’éditeur sélectionné qu’il faut utiliser (par exemple « Premium »). Pour plus d’informations, voir [Objectifs de scalabilité et de performances de Stockage Azure].

   * **Réplication** : spécifie la réplication pour le compte de stockage (par exemple, « Redondant dans une zone »). Pour plus d’informations, consultez [Réplication de Stockage Azure].

4. Après avoir spécifié toutes les options ci-dessus, cliquez sur **Créer**.

## <a name="create-a-storage-container-in-eclipse"></a>Créer un conteneur de stockage dans Eclipse

Pour créer un conteneur de stockage à l’aide de l’Explorateur Azure, procédez comme suit :

1. Dans l’**Explorateur Azure**, cliquez avec le bouton droit sur le compte de stockage où vous voulez créer un conteneur puis cliquez sur **Créer un conteneur d’objets blob**.

   ![Commande Créer un conteneur d’objets blob][CC01]

2. Dans la boîte de dialogue **Créer un conteneur d’objets blob**, spécifiez le nom de votre conteneur puis cliquez sur **OK**. Pour plus d’informations sur l’affectation de noms aux conteneurs de stockage, consultez [Affectation de noms et références aux conteneurs, objets blob et métadonnées].

   ![Boîte de dialogue Créer un conteneur d’objets blob][CC02]

## <a name="delete-a-storage-container-in-eclipse"></a>Supprimer un conteneur de stockage dans Eclipse

Pour supprimer un conteneur de stockage à l’aide de l’Explorateur Azure, procédez comme suit :

1. Dans l’**Explorateur Azure**, cliquez avec le bouton droit sur le conteneur de stockage puis cliquez sur **Supprimer**.

   ![Commande Supprimer un conteneur de stockage][DC01]

2. Dans la fenêtre de confirmation, cliquez sur **OK**.

   ![Fenêtre de confirmation de la suppression d’un conteneur de stockage][DC02]

## <a name="delete-a-storage-account-in-eclipse"></a>Supprimer un compte de stockage dans Eclipse

Pour supprimer un compte de stockage à l’aide de l’Explorateur Azure, procédez comme suit :

1. Dans l’**Explorateur Azure**, cliquez avec le bouton droit sur le compte de stockage puis cliquez sur **Supprimer**.

   ![Commande Supprimer un compte de stockage][DS01]

2. Dans la fenêtre de confirmation, cliquez sur **OK**.

   ![Fenêtre de confirmation de la suppression d’un compte de stockage][DS02]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les comptes de stockage Azure, leurs tailles et leurs tarifications, consultez les liens suivants :

* [Introduction à Microsoft Azure Storage]
* [À propos des comptes de stockage Azure]
* Tailles des comptes de stockage Azure
  * [Tailles des machines virtuelles Windows dans Azure]
  * [Tailles des machines virtuelles Linux dans Azure]
* Tarification des comptes de stockage Azure
  * [Tarification des comptes de stockage Windows]
  * [Tarification des comptes de stockage Linux]

Pour plus d’informations sur les kits de ressources Azure pour les environnements de développement intégré Java, consultez les ressources suivantes :

* [Kit de ressources Azure pour Eclipse]
  * [Nouveautés du Kit de ressources Azure pour Eclipse]
  * [Installation du kit de ressources Azure pour Eclipse]
  * [Instructions de connexion pour le kit de ressources Azure pour Eclipse]
  * [Créer une application web Hello World pour Azure dans Eclipse]
* [Kit de ressources Azure pour IntelliJ]
  * [Nouveautés du kit de ressources Azure pour IntelliJ]
  * [Installation du kit de ressources Azure pour IntelliJ]
  * [Instructions de connexion pour le kit de ressources Azure pour IntelliJ]
  * [Créer une application web Hello World pour Azure dans IntelliJ]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez [Centre de développement Java pour Azure] et [Outils Java pour Visual Studio Team Services].

<!-- URL List -->

[Kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij.md
[Créer une application web Hello World pour Azure dans Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application web Hello World pour Azure dans IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installation du kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installation du kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Instructions de connexion pour le kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Instructions de connexion pour le kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Nouveautés du Kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Nouveautés du kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java pour Azure]: https://azure.microsoft.com/develop/java/
[Outils Java pour Visual Studio Team Services]: https://java.visualstudio.com/

[Introduction à Microsoft Azure Storage]: /azure/storage/storage-introduction
[À propos des comptes de stockage Azure]: /azure/storage/storage-create-storage-account
[Réplication de Stockage Azure]: /azure/storage/storage-redundancy
[Objectifs de scalabilité et de performances de Stockage Azure]: /azure/storage/storage-scalability-targets
[Affectation de noms et références aux conteneurs, objets blob et métadonnées]: http://go.microsoft.com/fwlink/?LinkId=255555

[Tailles des machines virtuelles Windows dans Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Tailles des machines virtuelles Linux dans Azure]: /azure/virtual-machines/virtual-machines-linux-sizes
[Tarification des comptes de stockage Windows]: /pricing/details/virtual-machines/windows/
[Tarification des comptes de stockage Linux]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DC02.png

