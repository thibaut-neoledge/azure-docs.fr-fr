---
title: "Gérer des machines virtuelles à l’aide de l’Explorateur Azure pour Eclipse | Microsoft Docs"
description: "Découvrez comment gérer vos machines virtuelles Azure à l’aide de l’Explorateur Azure pour Eclipse."
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
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 9784e8af9c530078afee06f08a23403a44b0762f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/12/2017


---

# <a name="manage-virtual-machines-by-using-the-azure-explorer-for-eclipse"></a>Gérer des machines virtuelles à l’aide de l’Explorateur Azure pour Eclipse

L’Explorateur Azure, qui fait partie du Kit de ressources Azure pour Eclipse, fournit aux développeurs Java une solution facile à utiliser pour gérer les machines virtuelles de leur compte Azure à partir de l’environnement de développement intégré (IDE) Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="create-a-virtual-machine-in-eclipse"></a>Création d’une machine virtuelle dans Eclipse

Pour créer une machine virtuelle à l’aide de l’Explorateur Azure, procédez comme suit :

1. Connectez-vous à votre compte Azure en suivant les [Instructions de connexion pour le Kit de ressources Azure pour Eclipse].

2. Dans l’affichage **Explorateur Azure**, développez le nœud **Azure**, cliquez avec le bouton droit sur **Machines virtuelles**, puis cliquez sur **Créer une machine virtuelle**.

   ![Commande Créer une machine virtuelle][CR01]  
   L’**Assistant Créer une machine virtuelle** s’ouvre.

3. Dans la fenêtre **Choisir un abonnement**, sélectionnez votre abonnement, puis cliquez sur **Suivant**.

   ![Fenêtre Choisir un abonnement][CR02]

4. Dans la fenêtre **Sélectionner une image de machine virtuelle**, entrez les informations suivantes :

   * **Emplacement**: spécifie l’emplacement où votre machine virtuelle sera créée (par exemple *États-Unis de l’Ouest*).

   * **Éditeur** : spécifie l’éditeur qui a créé l’image et que vous allez utiliser pour créer votre machine virtuelle, par exemple *Microsoft*.

   * **Offre** : spécifie la machine virtuelle et l’offre de l’éditeur sélectionné à utiliser (par exemple, *JDK*).

   * **Référence (SKU)** : spécifie l’unité de gestion de stock (SKU) de l’offre sélectionnée à utiliser (par exemple, *JDK_8*).

   * **N° de version** : spécifie la version de la référence (SKU) sélectionnée à utiliser.

    ![Fenêtre Sélectionner une image de machine virtuelle][CR03]

5. Cliquez sur **Suivant**.

6. Dans la fenêtre **Paramètres de base de la machine virtuelle**, entrez les informations suivantes :

   * **Nom de la machine virtuelle** : spécifie le nom de votre nouvelle machine virtuelle, qui doit commencer par une lettre et contenir uniquement des lettres, des chiffres et des traits d’union.

   * **Taille** : spécifie le nombre de cœurs et la quantité de mémoire à allouer à votre machine virtuelle.

   * **Nom d’utilisateur** : spécifie le compte administrateur à créer pour la gestion de votre machine virtuelle.

   * **Mot de passe** et **Confirmer** : spécifient le mot de passe pour votre compte administrateur.

    ![Fenêtre Paramètres de base de la machine virtuelle][CR04]

7. Cliquez sur **Suivant**.

8. Dans la fenêtre **Créer un nouveau compte de stockage**, saisissez les informations suivantes :

   * **Groupe de ressources** : spécifie le groupe de ressources pour votre machine virtuelle. Sélectionnez l’une des options suivantes :
      * **Créer** : spécifie que vous souhaitez créer un groupe de ressources.
      * **Utiliser l’existant** : spécifie que vous souhaitez sélectionner un groupe de ressources déjà associé à votre compte Azure.

      ![La boîte de dialogue Créer un compte de stockage][CR05]

   * **Compte de stockage** : spécifie le compte de stockage à utiliser pour le stockage de votre machine virtuelle. Vous pouvez utiliser un compte de stockage existant ou en créer un nouveau.

   * **Réseau virtuel** et **sous-réseau** : spécifient le réseau virtuel et le sous-réseau auquel se connectera votre machine virtuelle. Vous pouvez utiliser un réseau et un sous-réseau existants, ou créer un réseau et un sous-réseau. Si vous sélectionnez **Créer**, la boîte de dialogue suivante s’affiche :

      ![La boîte de dialogue Créer un réseau virtuel][CR06]

9. Dans la fenêtre **Ressources associées**, entrez les informations suivantes :

   * **Adresse IP publique** : spécifie l’adresse IP externe de votre machine virtuelle. Vous pouvez choisir de créer une adresse IP ou, si votre machine virtuelle n’a pas besoin d’adresse IP publique, vous pouvez sélectionner **(Aucune)**.

   * **Groupe de sécurité réseau** : spécifie un pare-feu réseau facultatif pour votre machine virtuelle. Vous pouvez sélectionner un pare-feu existant ou, si votre machine virtuelle n’a pas besoin de pare-feu réseau, vous pouvez sélectionner **(Aucun)**.

   * **Groupe à haute disponibilité** : spécifie un groupe à haute disponibilité auquel votre machine virtuelle peut appartenir. Vous pouvez sélectionner un groupe à haute disponibilité, créer un groupe à haute disponibilité ou, si votre machine virtuelle ne doit pas appartenir à un groupe à haute disponibilité, vous pouvez sélectionner **(Aucun)**.

   ![Fenêtre ressources associées][CR07]

9. Cliquez sur **Terminer**.  
  Votre nouvelle machine virtuelle s’affiche dans la fenêtre de l’outil Explorateur Azure.

   ![Nouvelle machine virtuelle][CR08]

## <a name="restart-a-virtual-machine-in-eclipse"></a>Redémarrage d’une machine virtuelle dans Eclipse

Pour redémarrer une machine virtuelle à l’aide de l’Explorateur Azure dans Eclipse, procédez comme suit :

1. Dans l’affichage **Explorateur Azure**, cliquez avec le bouton droit sur la machine virtuelle, puis sélectionnez **Redémarrer**.

   ![Commande Redémarrer de la machine virtuelle][RE01]

2. Dans la fenêtre de confirmation, cliquez sur **Oui**.

   ![La fenêtre de confirmation de redémarrage][RE02]

## <a name="shut-down-a-virtual-machine-in-eclipse"></a>Arrêt d’une machine virtuelle dans Eclipse

Pour arrêter une machine virtuelle en cours d’exécution à l’aide de l’Explorateur Azure dans Eclipse, procédez comme suit :

1. Dans l’affichage **Explorateur Azure**, cliquez avec le bouton droit sur la machine virtuelle, puis sélectionnez **Arrêter**.

   ![Commande Arrêter de la machine virtuelle][SH01]

2. Dans la fenêtre de confirmation, cliquez sur **Oui**.

   ![La fenêtre de confirmation d’arrêt de machine virtuelle][SH02]

## <a name="delete-a-virtual-machine-in-eclipse"></a>Suppression d’une machine virtuelle dans Eclipse

Pour supprimer une machine virtuelle à l’aide de l’Explorateur Azure dans Eclipse, procédez comme suit :

1. Dans l’affichage **Explorateur Azure**, cliquez avec le bouton droit sur la machine virtuelle, puis sélectionnez **Supprimer**.

   ![Commande Supprimer de la machine virtuelle][DE01]

2. Dans la fenêtre de confirmation, cliquez sur **Oui**.

   ![La fenêtre de confirmation de suppression de machine virtuelle][DE02]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les tailles et tarifications des machines virtuelles Azure, voir les ressources suivantes :

* Tailles des machines virtuelles Azure
  * [Tailles des machines virtuelles Windows dans Azure]
  * [Tailles des machines virtuelles Linux dans Azure]
* Tarification des machines virtuelles Azure
  * [Tarification des machines virtuelles Windows]
  * [Tarification des machines virtuelles Linux]

Pour plus d’informations sur les boîtes à outils Azure pour les environnements de développement intégré Java, voir les ressources suivantes :

* [Kit de ressources Azure pour Eclipse]
  * [Nouveautés du Kit de ressources Azure pour Eclipse]
  * [Installation du kit de ressources Azure pour Eclipse]
  * [Instructions de connexion pour le Kit de ressources Azure pour Eclipse]
  * [Créer une application web Hello World pour Azure dans Eclipse]
* [Kit de ressources Azure pour IntelliJ]
  * [Nouveautés du Kit de ressources Azure pour IntelliJ]
  * [Installation du kit de ressources Azure pour IntelliJ]
  * [Instructions de connexion pour le Kit de ressources Azure pour IntelliJ]
  * [Créer une application web Hello World pour Azure dans IntelliJ]

Pour plus d’informations sur l’utilisation d’Azure avec Java, voir le [Centre de développement Java pour Azure] et les [Outils Java pour Visual Studio Team Services].

<!-- URL List -->

[Kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij.md
[Créer une application web Hello World pour Azure dans Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application web Hello World pour Azure dans IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installation du kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installation du kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Instructions de connexion pour le Kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Instructions de connexion pour le Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Nouveautés du Kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Nouveautés du Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java pour Azure]: https://azure.microsoft.com/develop/java/
[Outils Java pour Visual Studio Team Services]: https://java.visualstudio.com/

[Tailles des machines virtuelles Windows dans Azure]: /azure/virtual-machines/virtual-machines-windows-sizes
[Tailles des machines virtuelles Linux dans Azure]: /azure/virtual-machines/virtual-machines-linux-sizes
[Tarification des machines virtuelles Windows]: /pricing/details/virtual-machines/windows/
[Tarification des machines virtuelles Linux]: /pricing/details/virtual-machines/linux/

<!-- IMG List -->

[RE01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/RE01.png
[RE02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/RE02.png

[SH01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/SH01.png
[SH02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/SH02.png

[DE01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/DE01.png
[DE02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/DE02.png

[CR01]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR01.png
[CR02]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR02.png
[CR03]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR03.png
[CR04]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR04.png
[CR05]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR05.png
[CR06]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR06.png
[CR07]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR07.png
[CR08]: ./media/azure-toolkit-for-eclipse-managing-virtual-machines-using-azure-explorer/CR08.png

