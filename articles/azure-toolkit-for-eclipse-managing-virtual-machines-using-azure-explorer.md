---
title: "Gestion des machines virtuelles avec l’Explorateur Azure pour Eclipse | Microsoft Docs"
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
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f8631c5fe566762862f64b47843e154b68bb8031
ms.contentlocale: fr-fr
ms.lasthandoff: 04/22/2017


---

# <a name="managing-virtual-machines-using-the-azure-explorer-for-eclipse"></a>Gestion des machines virtuelles avec l’Explorateur Azure pour Eclipse

L’Explorateur Azure, qui fait partie du kit de ressources Azure pour Eclipse, fournit aux développeurs Java une solution facile à utiliser pour gérer les machines virtuelles de leur compte Azure à partir de l’IDE Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="creating-a-virtual-machine-in-eclipse"></a>Création d’une machine virtuelle dans Eclipse

Les étapes suivantes vous permettront de créer une machine virtuelle avec l’Explorateur Azure.

1. Connectez-vous à votre compte Azure à l’aide de la procédure décrite dans l’article [Azure Sign In Instructions for the Azure Toolkit for Eclipse] (Instructions de connexion à Azure pour le kit de ressources Azure pour Eclipse).

1. Dans la vue **Explorateur Azure**, développez le nœud **Azure**, cliquez avec le bouton droit sur **Machines virtuelles**, puis cliquez sur **Créer une machine virtuelle**.
   ![Menu Créer une machine virtuelle][CR01]

1. Lorsque l’Assistant **Créer une machine virtuelle** s’affiche, choisissez votre abonnement, puis cliquez sur **Suivant**.
   ![Assistant Créer une machine virtuelle][CR02]

1. Sur l’écran suivant de l’Assistant, spécifiez les options suivantes, puis cliquez sur **Suivant** :  ![Assistant Créer une machine virtuelle][CR03]

   a. **Emplacement** : spécifie l’emplacement où votre machine virtuelle sera créée, par exemple « États-Unis de l’Ouest ».

   b. **Éditeur** : spécifie l’éditeur qui a créé l’image et que vous allez utiliser pour créer votre machine virtuelle, par exemple « Microsoft ».

   c. **Offre** : spécifie la machine virtuelle et l’offre à utiliser à partir de l’éditeur sélectionné, par exemple « JDK ».

   d. **Référence (SKU)** : spécifie *l’unité de gestion de stock (SKU)* à utiliser à partir de l’offre sélectionnée, par exemple « JDK_8 ».

   e. **Version #** (N° de version) : spécifie la version à utiliser à partir de la référence sélectionnée.

1. Sur l’écran suivant de l’Assistant, spécifiez les options suivantes, puis cliquez sur **Suivant** : ![Assistant Créer une machine virtuelle][CR04]

   a. **Nom de la machine virtuelle** : spécifie le nom de votre nouvelle machine virtuelle, qui doit commencer par une lettre et contenir uniquement des lettres, des chiffres et des traits d’union.

   b. **Taille** : spécifie le nombre de cœurs et la quantité de mémoire à allouer à votre machine virtuelle.

   c. **Nom d’utilisateur** : spécifie le compte administrateur à créer pour la gestion de votre machine virtuelle.

   d. **Mot de passe** et **Confirmer** : spécifient le mot de passe pour votre compte administrateur.

1. Sur le dernier écran de l’Assistant, spécifiez les options suivantes :

   a. **Groupe de ressources** : spécifie le groupe de ressources pour votre machine virtuelle ; vous devez en choisir un parmi les options ci-dessous.
      * **Créer** : spécifie que vous souhaitez créer un groupe de ressources.
      * **Utiliser existant** : spécifie que vous allez choisir à partir d’une liste les groupes de ressources associés à votre compte Azure.

   b. **Compte de stockage** : spécifie le compte de stockage à utiliser pour le stockage de votre machine virtuelle ; vous pouvez choisir un compte de stockage existant ou en créer un. Si vous choisissez **&lt;&lt;Créer&gt;&gt;**, la boîte de dialogue suivante s’affiche :

      ![Boîte de dialogue Créer un compte de stockage][CR05]

   c. **Réseau virtuel** et **Sous-réseau** : spécifient le réseau virtuel et le sous-réseau auxquels votre machine virtuelle se connectera ; vous pouvez choisir un réseau et un sous-réseau existant à utiliser pour votre machine virtuelle, ou vous pouvez en créer. Si vous choisissez **&lt;&lt;Créer&gt;&gt;**, la boîte de dialogue suivante s’affiche :<br/>

      ![Boîte de dialogue Créer un réseau virtuel][CR06]

   d. **Adresse IP publique** : spécifie l’adresse IP externe pour votre machine virtuelle ; vous pouvez choisir de créer une adresse IP ou sélectionner **(None)** (Aucune) si votre machine virtuelle n’aura pas d’adresse IP publique.

   e. **Groupe de sécurité réseau** : spécifie un pare-feu réseau facultatif utilisé par votre machine virtuelle ; vous pouvez choisir un pare-feu existant ou sélectionner **(None)** (Aucun) si votre machine virtuelle n’utilisera pas de pare-feu réseau.

   f. **Groupe à haute disponibilité** : spécifie un groupe à haute disponibilité facultatif auquel votre machine virtuelle peut appartenir ; vous pouvez choisir un groupe à haute disponibilité existant, ou en créer un ou sélectionner **(None)** (Aucun) si votre machine virtuelle n’appartiendra pas à un groupe à haute disponibilité.

1. Une fois que vous avez entré toutes les options répertoriées ci-dessus, cliquez sur **Terminer** : ![Assistant Créer une nouvelle machine virtuelle][CR07]

1. Lorsque vous avez terminé les étapes ci-dessus, votre nouvelle machine virtuelle s’affichera dans la fenêtre de l’outil Explorateur Azure.
   ![Nouvelle machine virtuelle][CR08]

## <a name="restarting-a-virtual-machine-in-eclipse"></a>Redémarrage d’une machine virtuelle dans Eclipse

Pour redémarrer une machine virtuelle à l’aide de l’Explorateur Azure dans Eclipse, procédez comme suit :

1. Dans la vue **Explorateur Azure**, cliquez avec le bouton droit sur la machine virtuelle et choisissez **Redémarrer**.
   ![Redémarrage d’une machine virtuelle][RE01]

1. Cliquez sur **Oui** lorsque vous êtes invité à redémarrer la machine virtuelle.
   ![Redémarrage d’une machine virtuelle][RE02]

## <a name="shutting-down-a-virtual-machine-in-eclipse"></a>Arrêt d’une machine virtuelle dans Eclipse

Pour arrêter une machine virtuelle en cours d’exécution à l’aide de l’Explorateur Azure dans Eclipse, procédez comme suit :

1. Dans la vue **Explorateur Azure**, cliquez avec le bouton droit sur la machine virtuelle et choisissez **Arrêt**.
   ![Arrêt d’une machine virtuelle][SH01]

1. Cliquez sur **Oui** lorsque vous êtes invité à arrêter la machine virtuelle.
   ![Arrêt d’une machine virtuelle][SH02]

## <a name="deleting-a-virtual-machine-in-eclipse"></a>Suppression d’une machine virtuelle dans Eclipse

Pour redémarrer une machine virtuelle à l’aide de l’Explorateur Azure dans Eclipse, procédez comme suit :

1. Dans la vue **Explorateur Azure**, cliquez avec le bouton droit sur la machine virtuelle et choisissez **Supprimer**.
   ![Suppression d’une machine virtuelle][DE01]

1. Cliquez sur **Oui** lorsque vous êtes invité à supprimer la machine virtuelle.
   ![Suppression d’une machine virtuelle][DE02]

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur les tailles et les tarifs associés aux machines virtuelles Azure, consultez les liens suivants :

* Tailles de machine virtuelle Azure
  * [Tailles des machines virtuelles Windows dans Azure]
  * [Tailles des machines virtuelles Linux dans Azure]
* Tarifs associés aux machines virtuelles Azure
  * [Tarification des machines virtuelles Windows]
  * [Tarification des machines virtuelles Linux]

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

