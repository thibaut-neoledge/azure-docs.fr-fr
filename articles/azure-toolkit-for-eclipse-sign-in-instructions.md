---
title: "Instructions de connexion à Azure pour le kit de ressources Azure pour Eclipse | Microsoft Docs"
description: "Découvrez comment vous connecter à Microsoft Azure à l’aide du kit de ressources Azure pour Eclipse."
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
ms.openlocfilehash: 02dd9935086c4c40d9ed54cc9ff2412ca96889f5
ms.contentlocale: fr-fr
ms.lasthandoff: 04/22/2017


---

# <a name="azure-sign-in-instructions-for-the-azure-toolkit-for-eclipse"></a>Instructions de connexion à Azure pour le kit de ressources Azure pour Eclipse

Le kit de ressources Azure pour Eclipse propose deux méthodes pour vous connecter à votre compte Azure :

  * **Interactive** : si vous utilisez cette méthode, vous devez entrer vos informations d’identification Azure à chaque fois que vous vous connectez à votre compte Azure.
  * **Automatisée** : si vous utilisez cette méthode, vous devez créer un fichier d’informations d’identification qui contiendra vos données de principal de service, après quoi vous pourrez utiliser le fichier d’informations d’identification pour vous connecter automatiquement à votre compte Azure.

Les étapes décrites dans les sections suivantes décrivent l’utilisation de chaque méthode.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="signing-into-your-azure-account-interactively"></a>Connexion interactive à votre compte Azure

La procédure suivante indique comment vous connecter à Azure en entrant manuellement vos informations d’identification Azure.

1. Ouvrez votre projet avec Eclipse.

1. Cliquez successivement sur **Outils**, **Azure** et **Se connecter**.

   ![Menu d’Eclipse pour la connexion à Azure][I01]

1. Lorsque la boîte de dialogue **Connexion à Azure** s’affiche, sélectionnez **Interactive**, puis cliquez sur **Se connecter**.

   ![Boîte de dialogue Se connecter][I02]

1. Lorsque la boîte de dialogue **Connexion à Azure** s’affiche, entrez vos informations d’identification Azure, puis cliquez sur **Se connecter**.

   ![Boîte de dialogue Connexion à Azure][I03]

1. Lorsque la boîte de dialogue **Sélectionner des abonnements** s’affiche, sélectionnez les abonnements que vous souhaitez utiliser, puis cliquez sur **OK**.

   ![Boîte de dialogue Sélectionner des abonnements][I04]

## <a name="signing-out-of-your-azure-account-when-you-signed-in-interactively"></a>Déconnexion de votre compte Azure lorsque vous vous êtes connecté de manière interactive

Après avoir configuré les étapes indiquées dans la section précédente, vous êtes déconnecté automatiquement de votre compte Azure à chaque redémarrage d’Eclipse. Toutefois, si vous souhaitez vous déconnecter de votre compte Azure sans redémarrer Eclipse, procédez comme suit.

1. Dans Eclipse, cliquez successivement sur **Outils**, **Azure** et **Se déconnecter**.

   ![Menu d’Eclipse pour la déconnexion d’Azure][L01]

1. Lorsque la boîte de dialogue **Déconnexion d’Azure** s’affiche, cliquez sur **Oui**.

   ![Boîte de dialogue Se déconnecter][L02]

## <a name="signing-into-your-azure-account-automatically-and-creating-a-credentials-file-to-use-in-the-future"></a>Connexion automatique à votre compte Azure et création d’un fichier d’informations d’identification à utiliser à l’avenir

La procédure suivante vous guide dans la création d’un fichier d’informations d’identification qui contient vos données de principal de service. Une fois que vous avez effectué ces étapes, Eclipse utilise automatiquement le fichier d’informations d’identification pour vous connecter automatiquement à Azure à chaque fois que vous ouvrez votre projet.

1. Ouvrez votre projet avec Eclipse.

1. Cliquez successivement sur **Outils**, **Azure** et **Se connecter**.

   ![Menu d’Eclipse pour la connexion à Azure][A01]

1. Lorsque la boîte de dialogue **Connexion à Azure** s’affiche, sélectionnez **Automatisée**, puis cliquez sur **Nouveau**.

   ![Boîte de dialogue Se connecter][A02]

1. Lorsque la boîte de dialogue **Connexion à Azure** s’affiche, entrez vos informations d’identification Azure, puis cliquez sur **Se connecter**.

   ![Boîte de dialogue Connexion à Azure][A03]

1. Lorsque la boîte de dialogue **Create authentication files (Créer des fichiers d’authentification)** s’affiche, sélectionnez les abonnements que vous souhaitez utiliser, choisissez votre répertoire de destination, puis cliquez sur **Démarrer**.

   ![Boîte de dialogue Connexion à Azure][A04]

1. La boîte de dialogue **Service Principal Creation Status (État de création du principal de service)** s’affiche, et une fois que vos fichiers ont été créés, cliquez sur **OK**.

   ![Boîte de dialogue Service Principal Creation Status (État de création du principal de service)][A05]

1. Lorsque la boîte de dialogue **Connexion à Azure** s’affiche, cliquez sur **Se connecter**.

   ![Boîte de dialogue Connexion à Azure][A06]

1. Lorsque la boîte de dialogue **Sélectionner des abonnements** s’affiche, sélectionnez les abonnements que vous souhaitez utiliser, puis cliquez sur **OK**.

   ![Boîte de dialogue Sélectionner des abonnements][A07]

## <a name="signing-out-of-your-azure-account-when-you-signed-in-automatically"></a>Déconnexion de votre compte Azure lorsque vous vous êtes connecté automatiquement

Après avoir configuré les étapes indiquées dans la section précédente, le kit de ressources Azure vous connecte automatiquement à votre compte Azure à chaque redémarrage d’Eclipse. Toutefois, pour vous déconnecter de votre compte Azure et empêcher le kit de ressources Azure de vous connecter automatiquement, procédez comme suit.

1. Dans Eclipse, cliquez successivement sur **Outils**, **Azure** et **Se déconnecter**.

   ![Menu d’Eclipse pour la déconnexion d’Azure][L01]

1. Lorsque la boîte de dialogue **Déconnexion d’Azure** s’affiche, cliquez sur **Oui**.

   ![Boîte de dialogue Se déconnecter][L03]

## <a name="signing-into-your-azure-account-automatically-using-a-credentials-file-which-you-have-already-created"></a>Connexion automatique à votre compte Azure et utilisation d’un fichier d’informations d’identification que vous avez déjà créé

Si vous vous déconnectez d’Azure lorsque vous utilisez Eclipse, vous devez reconfigurer le kit de ressources Azure pour Eclipse afin d’utiliser un fichier d’informations d’identification qui a été créé pour pouvoir vous connecter automatiquement à votre compte Azure. La procédure suivante vous guide dans la configuration du kit de ressources Azure pour utiliser un fichier d’informations d’identification existant.

1. Ouvrez votre projet avec Eclipse.

1. Cliquez successivement sur **Outils**, **Azure** et **Se connecter**.

   ![Menu d’Eclipse pour la connexion à Azure][A01]

1. Lorsque la boîte de dialogue **Connexion à Azure** s’affiche, sélectionnez **Automatisée**, puis cliquez sur **Parcourir**.

   ![Boîte de dialogue Se connecter][A02]

1. Lorsque la boîte de dialogue **Select Authenticated File (Sélectionner un fichier authentifié)** s’affiche, sélectionnez un fichier d’informations d’identification que vous avez créé précédemment, puis cliquez sur **Sélectionner**.

   ![Boîte de dialogue Se connecter][A08]

1. Lorsque la boîte de dialogue **Connexion à Azure** s’affiche, cliquez sur **Se connecter**.

   ![Boîte de dialogue Connexion à Azure][A06]

1. Lorsque la boîte de dialogue **Sélectionner des abonnements** s’affiche, sélectionnez les abonnements que vous souhaitez utiliser, puis cliquez sur **OK**.

   ![Boîte de dialogue Sélectionner des abonnements][A07]

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur les boîtes à outils Azure pour les environnements de développement Java, consultez les liens suivants :

* [Kit de ressources Azure pour Eclipse]
  * [Nouveautés du kit de ressources Azure pour Eclipse]
  * [Installation du kit de ressources Azure pour Eclipse]
  * *Instructions de connexion à Azure pour le kit de ressources Azure pour Eclipse (cet article)*
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
[Sign In Instructions for the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Azure Sign In Instructions for the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md (Instructions de connexion à Azure pour le kit de ressources Azure pour IntelliJ)
[Nouveautés du kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Nouveautés du Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java pour Azure]: https://azure.microsoft.com/develop/java/
[outils Java pour Visual Studio Team Services]: https://java.visualstudio.com/

<!-- IMG List -->

[I01]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/I01.png
[I02]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/I02.png
[I03]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/I03.png
[I04]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/I04.png

[A01]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A01.png
[A02]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A02.png
[A03]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A03.png
[A04]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A04.png
[A05]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A05.png
[A06]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A06.png
[A07]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A07.png
[A08]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/A08.png

[L01]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/L01.png
[L02]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/L02.png
[L03]: ./media/azure-toolkit-for-eclipse-sign-in-instructions/L03.png

