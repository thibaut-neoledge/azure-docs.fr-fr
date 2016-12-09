---
title: "Utilisation du plug-in Azure Web App avec la solution d’intégration continue Jenkins | Microsoft Docs"
description: "Décrit comment utiliser le plug-in Azure Web App avec la solution d’intégration continue Jenkins."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: be6c4e62-da76-44f6-bb00-464902734805
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c6f18c9b802d6fa18c4f50d3a537003eaaf25d14


---
# <a name="how-to-use-the-azure-web-app-plugin-with-jenkins-continuous-integration"></a>Utilisation du plug-in Azure Web App avec la solution d’intégration continue Jenkins
Le plug-in Azure Web App pour Jenkins facilite la création d’applications web sur Azure lors de l’exécution de builds distribuées, et du déploiement d’un fichier WAR sur votre application web.

### <a name="prerequisites"></a>Composants requis
Avant de suivre les étapes décrites dans cet article, vous devez enregistrer et autoriser votre application cliente, puis récupérer votre ID client et la clé secrète client qui seront envoyés à Azure Active Directory lors de l’authentification. Pour plus d’informations sur la configuration requise, consultez les articles suivants :

* [Intégration d’applications dans Azure Active Directory][integrate-apps-with-AAD]
* [Inscrire une application cliente][register-client-app]

En outre, vous devez télécharger le fichier **azure-webapp-plugin.hpi** à partir de l’URL suivante :

* [https://github.com/Microsoft/azure-webapp-plugin/tree/master/install](https://github.com/Microsoft/azure-webapp-plugin/tree/master/install)

## <a name="how-to-install-the-azure-web-app-plugin-for-jenkins"></a>Installation du plug-in Azure Web App pour Jenkins
1. [Téléchargez le fichier **azure-webapp-plugin.hpi** à partir de GitHub][azure-webapp-plugin-install]
2. Connectez-vous à votre tableau de bord Jenkins.
3. Dans le tableau de bord, cliquez sur **Manage Jenkins**(Gérer Jenkins).
   
    ![Gestion de Jenkins][jenkins-dashboard]
4. Sur la page **Manage Jenkins**, cliquez sur **Manage Plugins**.
   
    ![Gestion des plug-ins][manage-jenkins]
5. Cliquez sur l’onglet **Avancé**, puis cliquez sur **Parcourir** dans la section de **téléchargement du plug-in**. Accédez à l’emplacement de téléchargement du fichier **azure-webapp-plugin.hpi** dans les **composants requis**, sélectionnez le fichier, puis cliquez sur **Télécharger**.
   
    ![Téléchargement du plug-in][upload-plugin]
6. Si nécessaire, redémarrez Jenkins.

## <a name="configure-the-azure-web-app-plugin"></a>Configuration du plug-in Azure Web App
1. Dans votre tableau de bord Jenkins, cliquez sur l’un de vos projets.
   
    ![Sélection d’un projet][select-project]
2. Lorsque la page de votre projet s’affiche, cliquez sur **Configurer** dans le menu de gauche.
   
    ![Configuration du projet][configure-project]
3. Dans la section **Actions post-build**, cliquez sur le menu déroulant **Ajouter une action post-build** et sélectionnez **Configuration d’Azure Web App**. 
   
    ![Options avancées][advanced-options]
4. Lorsque la section **Configuration d’Azure Web App** s’affiche, entrez votre **ID d’abonnement**, votre **ID client**, votre **clé secrète client** et votre **point de terminaison de jeton OAuth 2.0** dans la **configuration du profil Azure**.
   
    ![Configuration du profil Azure][azure-profile-configuration]
5. Dans la section **Configuration d’Azure Web App**, entrez le **nom de votre groupe de ressources**, l’**emplacement**, le **nom du plan d’hébergement**, le **nom de Web App**, le **nom de la référence**, **la capacité de référence** et le **chemin d’accès au fichier WAR**.
   
    ![Configuration d’Azure Web App][webapp-configuration]
6. Cliquez sur **Enregistrer** pour enregistrer les paramètres de votre projet.
   
    ![Enregistrement du projet][save-project]
7. Cliquez sur **Générer maintenant** dans le menu de gauche.
   
    ![Génération du projet][build-project]

> [!NOTE]
> Un conteneur Web App est créé uniquement en l’absence d’un conteneur Web App.
> 
> 

<a name="see-also"></a>

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure].

Pour plus d’informations sur le plug-in Azure Web App pour Jenkins, consultez le projet du [plug-in Azure Web App] sur GitHub.

<!-- URL List -->

[Centre de développement Java pour Azure]: https://azure.microsoft.com/develop/java/
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx
[plug-in Azure Web App]: https://github.com/Microsoft/azure-webapp-plugin
[azure-webapp-plugin-install]: https://github.com/Microsoft/azure-webapp-plugin/tree/master/install

<!-- IMG List -->

[jenkins-dashboard]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/jenkins-dashboard.png
[manage-jenkins]:    ./media/app-service-web-azure-web-app-plugin-for-jenkins/manage-jenkins.png
[upload-plugin]:     ./media/app-service-web-azure-web-app-plugin-for-jenkins/upload-plugin.png
[select-project]:    ./media/app-service-web-azure-web-app-plugin-for-jenkins/select-project.png
[configure-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/configure-project.png
[advanced-options]:  ./media/app-service-web-azure-web-app-plugin-for-jenkins/advanced-options.png
[azure-profile-configuration]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/azure-profile-configuration.png
[build-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/build-project.png
[save-project]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/save-project.png
[webapp-configuration]: ./media/app-service-web-azure-web-app-plugin-for-jenkins/webapp-configuration.png



<!--HONumber=Nov16_HO3-->


