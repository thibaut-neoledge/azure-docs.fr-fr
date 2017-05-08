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
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 3c95ddde2909cd8dee055724ba01f22647627bd3
ms.lasthandoff: 04/22/2017


---

# <a name="managing-storage-accounts-using-the-azure-explorer-for-eclipse"></a>Gestion des comptes de stockage avec l’Explorateur Azure pour Eclipse

L’Explorateur Azure, qui fait partie du kit de ressources Azure pour Eclipse, fournit aux développeurs Java une solution facile à utiliser pour gérer les comptes de stockage de leur compte Azure à partir de l’IDE Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="creating-a-storage-account-in-eclipse"></a>Création d’un compte de stockage dans Eclipse

Les étapes suivantes vous permettront de créer un compte de stockage avec l’Explorateur Azure.

1. Connectez-vous à votre compte Azure à l’aide de la procédure décrite dans l’article [Azure Sign In Instructions for the Azure Toolkit for Eclipse] (Instructions de connexion à Azure pour le kit de ressources Azure pour Eclipse).

1. Dans la vue **Explorateur Azure**, développez le nœud **Azure**, cliquez avec le bouton droit sur **Comptes de stockage**, puis cliquez sur **Créer un compte de stockage**.
   ![Menu Créer un compte de stockage][CS01]

1. Quand la boîte de dialogue **Créer un compte de stockage** s’affiche, spécifiez les options ci-dessous : ![Boîte de dialogue Créer un compte de stockage][CS02]

   a. **Nom** : spécifie le nom du nouveau compte de stockage.

   b. **Abonnement** : spécifie l’abonnement Azure que vous voulez utiliser pour le nouveau compte de stockage.

   c. **Groupe de ressources** : spécifie le groupe de ressources pour votre machine virtuelle ; vous devez en choisir un parmi les options ci-dessous.
      * **Créer** : spécifie que vous souhaitez créer un groupe de ressources.
      * **Utiliser existant** : spécifie que vous allez choisir à partir d’une liste les groupes de ressources associés à votre compte Azure.

   d. **Région** : spécifie l’emplacement où votre compte de stockage sera créé, par exemple « États-Unis de l’Ouest ».

   e. **Type de compte** : spécifie le type de compte de stockage à créer, par exemple « Stockage Blob ». (Pour plus d’informations, consultez [À propos des comptes de stockage Azure].)

   f. **Performances** : spécifie le compte de stockage et l’offre à utiliser à partir de l’éditeur sélectionné, par exemple « Premium ». (Pour plus d’informations, consultez [Objectifs d’extensibilité et de performances de stockage Azure].)

   g. **Réplication** : spécifie la réplication pour le compte de stockage, par exemple « Redondant dans une zone ». (Pour plus d’informations, consultez [Réplication de stockage Azure].)

1. Après avoir indiqué toutes les options ci-dessus, cliquez sur **Créer**.

## <a name="creating-a-storage-container-in-eclipse"></a>Création d’un conteneur de stockage dans Eclipse

Les étapes suivantes vous permettront de créer un conteneur de stockage avec l’Explorateur Azure.

1. Dans l’Explorateur Azure, cliquez avec le bouton droit sur le compte de stockage dans lequel vous souhaitez créer un conteneur, puis cliquez sur **Créer un conteneur d’objets blob**.
   ![Menu Créer un conteneur d’objets blob][CC01]

1. Lorsque la boîte de dialogue **Créer un conteneur d’objets blob** s’affiche, spécifiez le nom de votre conteneur, puis cliquez sur **OK**. (Pour plus d’informations sur l’affectation de noms aux conteneurs de stockage, consultez [Naming and Referencing Containers, Blobs, and Metadata] (Affectation de noms et références aux conteneurs, objets blob et métadonnées)). ![Boîte de dialogue Créer un conteneur d’objets blob][CC02]

## <a name="deleting-a-storage-container-in-eclipse"></a>Suppression d’un conteneur de stockage dans Eclipse

Pour supprimer un conteneur de stockage avec l’Explorateur Azure, procédez comme suit :

1. Dans l’Explorateur Azure, cliquez avec le bouton droit sur le conteneur de stockage, puis cliquez sur **Supprimer**.
   ![Menu Supprimer un conteneur de stockage][DC01]

1. Cliquez sur **OK** lorsque vous êtes invité à supprimer le conteneur de stockage.
   ![Boîte de dialogue Delete Storage Container (Supprimer un conteneur de stockage)][DC02]

## <a name="deleting-a-storage-account-in-eclipse"></a>Suppression d’un compte de stockage dans Eclipse

Pour supprimer un compte de stockage avec l’Explorateur Azure, procédez comme suit :

1. Dans la vue **Explorateur Azure**, cliquez avec le bouton droit sur le compte de stockage et choisissez **Supprimer**.
   ![Menu Supprimer un compte de stockage][DS01]

1. Cliquez sur **OK** lorsque vous êtes invité à supprimer le compte de stockage.
   ![Boîte de dialogue Supprimer un compte de stockage][DS02]

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur les comptes de stockage Azure, les tailles et les tarifs, consultez les liens suivants :

* [Introduction à Microsoft Azure Storage]
* [À propos des comptes de stockage Azure]
* Tailles de compte de stockage Azure
  * [Tailles des machines virtuelles Windows dans Azure]
  * [Tailles des machines virtuelles Linux dans Azure]
* Tarifs associés aux comptes de stockage Azure
  * [Tarification Stockage Azure]
  * [Tarification Stockage Azure]

Pour plus d’informations sur les boîtes à outils Azure pour les environnements de développement Java, consultez les liens suivants :

* [Kit de ressources Azure pour Eclipse]
  * [Nouveautés du kit de ressources Azure pour Eclipse]
  * [Installation du kit de ressources Azure pour Eclipse]
  * [Azure Sign In Instructions for the Azure Toolkit for Eclipse] (Instructions de connexion à Azure pour le kit de ressources Azure pour Eclipse)
  * [Créer une application web « Hello World » pour Azure dans Eclipse]
* [Kit de ressources Azure pour IntelliJ]
  * [Nouveautés du Kit de ressources Azure pour IntelliJ]
  * [Installation du kit de ressources Azure pour IntelliJ]
  * [Azure Sign In Instructions for the Azure Toolkit for IntelliJ] (Instructions de connexion à Azure pour le kit de ressources Azure pour IntelliJ)
  * [Créer une application web « Hello World » pour Azure dans IntelliJ]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure] et les [outils Java pour Visual Studio Team Services].

<!-- URL List -->

[Kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij.md
[Créer une application web « Hello World » pour Azure dans Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application web « Hello World » pour Azure dans IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installation du kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installation du kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Azure Sign In Instructions for the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md (Instructions de connexion à Azure pour le kit de ressources Azure pour Eclipse)
[Azure Sign In Instructions for the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md (Instructions de connexion à Azure pour le kit de ressources Azure pour IntelliJ)
[Nouveautés du kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Nouveautés du Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java pour Azure]: https://azure.microsoft.com/develop/java/
[outils Java pour Visual Studio Team Services]: https://java.visualstudio.com/

[Introduction à Microsoft Azure Storage]: /azure/storage/storage-introduction
[À propos des comptes de stockage Azure]: /azure/storage/storage-create-storage-account
[Réplication de stockage Azure]: /azure/storage/storage-redundancy
[Objectifs d’extensibilité et de performances de stockage Azure]: /azure/storage/storage-scalability-targets
[Naming and Referencing Containers, Blobs, and Metadata]: http://go.microsoft.com/fwlink/?LinkId=255555

[Tailles des machines virtuelles Windows dans Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Tailles des machines virtuelles Linux dans Azure]: /azure/virtual-machines/virtual-machines-linux-sizes
[Tarification Stockage Azure]: /pricing/details/virtual-machines/windows/
[Tarification Stockage Azure]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: ./media/azure-toolkit-for-eclipse-managing-storage-accounts-using-azure-explorer/DC02.png

