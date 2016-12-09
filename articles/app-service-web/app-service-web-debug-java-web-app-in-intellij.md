---
title: "Débogage d’une application web Java sur Azure dans IntelliJ | Microsoft Docs"
description: "Ce didacticiel vous explique comment utiliser le Kit de ressources Azure pour IntelliJ pour déboguer une application web Java s’exécutant sur Azure."
services: app-service\web
documentationcenter: java
author: selvasingh
manager: wpickett
editor: 
ms.assetid: 78148648-5a89-4b7d-98f1-9cf8f38dfe8d
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 09/20/2016
ms.author: asirveda;robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 804edd8cd0e9648e6a61ef39a2a71dc2b8a239c6


---
# <a name="debug-a-java-web-app-on-azure-in-intellij"></a>Débogage d’une application web Java sur Azure dans IntelliJ
Ce didacticiel explique comment déboguer une application web Java s’exécutant sur Azure à l’aide du [Kit de ressources Azure pour IntelliJ]. Par souci de simplicité, vous utiliserez un exemple Java Server Page (JSP) de base pour ce didacticiel, mais la procédure est identique pour un servlet Java lors du débogage sur Azure.

À la fin de ce didacticiel, votre application doit ressembler à l’illustration suivante quand vous la déboguez dans IntelliJ :

![][01]

## <a name="prerequisites"></a>Composants requis
* JDK (Java Development Kit) version 1.8 ou ultérieure.
* IntelliJ IDEA édition Ultimate. Il peut être téléchargé à partir du site <https://www.jetbrains.com/idea/download/index.html>.
* Une distribution d’un serveur web ou d’un serveur d’applications basé sur Java, comme Apache Tomcat ou Jetty.
* Un abonnement Azure, qui peut être obtenu à l’adresse <https://azure.microsoft.com/fr-fr/free/> ou <http://azure.microsoft.com/pricing/purchase-options/>.
* Le Kit de ressources Azure pour IntelliJ. Pour plus d’informations, consultez [Installing the Azure Toolkit for IntelliJ](Installation du kit de ressources Azure pour IntelliJ).
* Un projet web dynamique créé et déployé sur Azure App Service ; par exemple, consultez [Créer une application web « Hello World » pour Azure dans IntelliJ].

## <a name="to-debug-a-java-web-app-on-azure"></a>Pour déboguer une application web Java sur Azure
Pour effectuer les étapes de cette section, vous pouvez utiliser un projet web dynamique existant que vous avez déjà déployé comme application web Java sur Azure, vous téléchargez un [exemple de projet web dynamique] et suivez les étapes indiquées dans [Créer une application web « Hello World » pour Azure dans IntelliJ] pour le déployer sur Azure. 

1. Ouvrez le projet d’application web Java que vous avez déployé sur Azure dans IntelliJ.
2. Cliquez sur le menu **Run** (Exécuter), puis sur **Edit Configurations...** (Modifier les configurations...).
   
    ![][02]
3. Lorsque la boîte de dialogue **Run/Debug Configurations** (Exécuter/Déboguer les configurations) s’affiche : 
   
   1. Sélectionnez **Azure Web App**.
   2. Cliquez sur **+** pour ajouter une nouvelle configuration.
   3. Entrez un **nom** pour la configuration.
   4. Acceptez les autres valeurs par défaut qui sont proposées par le Kit de ressources Azure, puis cliquez sur **OK**.
      
       ![][03]
4. Sélectionnez la configuration de débogage Azure Web App que vous venez de créer dans le coin supérieur droit du menu et cliquez sur **Debug**
   
    ![][04]
5. À l’invite **Enable remote debugging in the remote Web App now?** (Activer le débogage à distance sur l’application web à distance maintenant ?), cliquez sur **OK**.
6. À l’invite **Your web app is now ready for remote debugging** (Votre application web est maintenant prête pour le débogage à distance), cliquez sur **OK**.
   
    ![][05]
7. Sélectionnez la configuration de débogage Azure Web App que vous venez de créer dans le coin supérieur droit du menu et cliquez sur **Debug**(Déboguer).
8. Une invite de commande Windows ou un shell Unix s’ouvre et prépare la connexion nécessaire pour le débogage. Vous devez attendre que la connexion à votre application web Java à distance soit établie avant de continuer. Si vous utilisez Windows, cela ressemble à l’illustration suivante :
   
    ![][06]
9. Insérez un point d’arrêt dans votre page JSP, puis ouvrez l’URL de votre application web Java dans un navigateur :
   
   1. Ouvrez **Azure Explorer** dans IntelliJ.
   2. Accédez à **Web Apps** et à l’application web Java à déboguer.
   3. Cliquez avec le bouton droit sur l’application web, puis cliquez sur **Ouvrir dans un navigateur**.
   4. IntelliJ passe en mode débogage.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure].

Pour plus d’informations sur la création d’Azure Web Apps, consultez la [Vue d’ensemble de Web Apps].

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Kit de ressources Azure pour IntelliJ]: ../azure-toolkit-for-intellij.md
[Installing the Azure Toolkit for IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Créer une application web « Hello World » pour Azure dans IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[exemple de projet web dynamique]: http://go.microsoft.com/fwlink/?LinkId=817337

[Centre de développement Java pour Azure]: https://azure.microsoft.com/develop/java/
[Vue d’ensemble de Web Apps]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png



<!--HONumber=Nov16_HO3-->


