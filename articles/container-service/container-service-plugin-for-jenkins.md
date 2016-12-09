---
title: Plug-in Azure Container Service pour Jenkins | Microsoft Docs
description: "Déployez un conteneur Docker sur un service de cluster Azure Container Service à l’aide du plug-in Azure Container Service pour Jenkins."
services: container-service
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
tags: azure-container-service, jenkins
keywords: Azure, conteneurs, Jenkins
ms.assetid: dd719bda-6d1f-452b-a918-9a3aa9302107
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 435262867e8ead23d1453e8a2171f1c215fc5205


---
# <a name="azure-container-service-plugin-for-jenkins"></a>Plug-in Azure Container Service pour Jenkins
Le plug-in Azure Container Service pour Jenkins permet de simplifier le processus de déploiement de conteneurs [Docker] à l’aide de [Marathon] sur un cluster de conteneurs Azure exécutant un système de traitement distribué tel que le [système d’exploitation de centre de données (Data Center Operating System, DC/OS) de Mesosphere][mesosphere] ou [Apache Mesos][mesos].

Ce didacticiel explique comment installer le plug-in Azure Container Service pour Jenkins et comment configurer un projet Jenkins qui crée un service Azure Container Service avec Marathon et un orchestrateur DC/OS, et utilise Marathon pour déployer un conteneur Docker dans le cluster. Si le cluster Azure Container Service n’existe pas, Jenkins crée le cluster de conteneur et utilise Marathon pour déployer votre conteneur Docker sur le cluster lors de sa création ; dans le cas contraire, Jenkins déploie votre conteneur Docker sur le cluster Azure Container Service existant à l’aide de Marathon.

Pour plus d’informations sur Azure Container Service, consultez l’article [Présentation d’Azure Container Service][acs-intro].

### <a name="prerequisites"></a>Composants requis
Avant de suivre les étapes décrites dans cet article, vous devez enregistrer et autoriser votre application cliente, puis récupérer votre ID client et la clé secrète client qui seront envoyés à Azure Active Directory lors de l’authentification. Pour plus d’informations sur la configuration requise, voir les articles suivants :

* [Intégration d’applications dans Azure Active Directory][integrate-apps-with-AAD]
* [Inscrire une application cliente][register-client-app]

En outre, vous devez télécharger le fichier **azure-acs-plugin.hpi** à partir de l’URL suivante :

* [https://github.com/Microsoft/azure-acs-plugin/tree/master/install](https://github.com/Microsoft/azure-acs-plugin/tree/master/install)

## <a name="how-to-install-the-azure-container-service-plugin-for-jenkins"></a>Comment installer le plug-in Azure Container Service pour Jenkins
1. [Téléchargez le fichier **azure-acs-plugin.hpi** à partir de GitHub][azure-acs-plugin-install]
2. Connectez-vous à votre tableau de bord Jenkins.
3. Dans le tableau de bord, cliquez sur **Manage Jenkins**(Gérer Jenkins).
   
    ![Gestion de Jenkins][jenkins-dashboard]
4. Sur la page **Manage Jenkins**, cliquez sur **Manage Plugins**.
   
    ![Gestion des plug-ins][manage-jenkins]
5. Cliquez sur l’onglet **Avancé**, puis cliquez sur **Parcourir** dans la section de **téléchargement du plug-in**. Naviguez jusqu’à l’emplacement de téléchargement du fichier **azure-acs-plugin.hpi** dans les **composants requis**, et cliquez sur le **Télécharger** une fois que vous avez sélectionné le fichier.
   
    ![Téléchargement du plug-in][upload-plugin]
6. Si nécessaire, redémarrez Jenkins.

## <a name="configure-the-azure-container-service-plugin"></a>Configuration du plug-in Azure Container Service
1. Dans votre tableau de bord Jenkins, cliquez sur l’un de vos projets.
   
    ![Sélection d’un projet][select-project]
2. Lorsque la page de votre projet s’affiche, cliquez sur **Configurer** dans le menu de gauche.
   
    ![Configuration du projet][configure-project]
3. Dans la section **Post-build Actions** (Action post-génération), cliquez sur le menu déroulant **Add post-build action** (Ajouter une action post-génération) et sélectionnez **Azure Container Service Configuration** (Configuration Azure Container Service). 
   
    ![Options avancées][advanced-options]
4. Lorsque la section **Azure Container Service Configuration** (Configuration Azure Container Service) s’affiche, entrez votre ID d’abonnement, votre ID client, votre clé secrète client et votre point de terminaison de jeton OAuth 2.0.
   
    ![Configuration d’Azure Container Service][azure-container-service-config]
5. Dans la section **ACS Profile Configuration** (Configuration du profil ACS), entrez votre région, le préfixe du nom DNS, le nombre d’agents, la taille de la machine virtuelle de l’agent, le nom d’utilisateur admin, le nombre de maîtres et la clé publique SSH RSA.
   
    ![Configuration du profil ACS][acs-profile-configuration]
6. Dans la section **Marathon Profile Configuration** (Configuration du profil Marathon), entrez le chemin d’accès à votre fichier de configuration Marathon, le chemin d’accès à votre fichier privé SSH RSA et le mot de passe de votre fichier privé SSH RSA.
   
    ![Configuration du profil Marathon][marathon-profile-configuration]
7. Cliquez sur **Enregistrer** pour enregistrer les paramètres de votre projet.
   
    ![Enregistrement du projet][save-project]
8. Cliquez sur **Build Now** (Générer maintenant) dans le menu de gauche.
   
    ![Génération du projet][build-project]

Une fois la génération terminée, les journaux seront disponibles dans les journaux de console de génération.

> [!NOTE]
> Si le cluster Azure Container Service n’existe pas, Jenkins crée le cluster de conteneur et utilise Marathon pour déployer votre conteneur Docker sur le cluster lors de sa création ; dans le cas contraire, Jenkins déploie votre conteneur Docker sur le cluster Azure Container Service existant à l’aide de Marathon.
> 
> 

<a name="see-also"></a>

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur l’utilisation des clusters Azure Container Service et DC/OS, consultez les articles suivants :

* [Présentation d’Azure Container Service][acs-intro]
* [Déploiement d’un cluster Azure Container Service][acs-deploy]
* [Connexion à un cluster Azure Container Service][acs-connect]
* [Gestion des conteneurs via l’interface utilisateur web][acs-webui-management]

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure].

<!-- URL List -->

[azure-acs-plugin-install]: https://github.com/Microsoft/azure-acs-plugin/tree/master/install
[acs-intro]: ./container-service-intro.md
[acs-deploy]: ./container-service-deployment.md
[acs-connect]: ./container-service-connect.md
[acs-webui-management]: ./container-service-mesos-marathon-ui.md
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx

[Marathon]: https://mesosphere.github.io/marathon/
[Docker]: http://docker.io/
[mesosphere]: https://mesosphere.com/products/
[mesos]: https://mesos.apache.org/

[Centre de développement Java pour Azure]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[jenkins-dashboard]: ./media/container-service-plugin-for-jenkins/jenkins-dashboard.png
[manage-jenkins]: ./media/container-service-plugin-for-jenkins/manage-jenkins.png
[search-plugins]: ./media/container-service-plugin-for-jenkins/search-for-azure-plugin.png
[install-plugin]: ./media/container-service-plugin-for-jenkins/install-plugin.png
[select-project]: ./media/container-service-plugin-for-jenkins/select-project.png
[configure-project]: ./media/container-service-plugin-for-jenkins/configure-project.png
[advanced-options]: ./media/container-service-plugin-for-jenkins/advanced-options.png
[azure-container-service-config]: ./media/container-service-plugin-for-jenkins/azure-container-service-configuration.png
[acs-profile-configuration]: ./media/container-service-plugin-for-jenkins/acs-profile-configuration.png
[marathon-profile-configuration]: ./media/container-service-plugin-for-jenkins/marathon-profile-configuration.png
[save-project]: ./media/container-service-plugin-for-jenkins/save-project.png
[build-project]: ./media/container-service-plugin-for-jenkins/build-project.png
[upload-plugin]: ./media/container-service-plugin-for-jenkins/upload-plugin.png



<!--HONumber=Nov16_HO3-->


