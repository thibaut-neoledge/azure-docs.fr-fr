---
title: "Déployer votre application dans Azure App Service avec FTP/S | Microsoft Docs"
description: "Découvrez comment déployer votre application dans Azure App Service avec FTP ou FTPS."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: cephalin;dariac
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: e3ac2f2156719ad975049b0c2b4cbca81d88e779
ms.contentlocale: fr-fr
ms.lasthandoff: 09/20/2017

---

# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Déployer votre application dans Azure App Service avec FTP/S

Cet article vous explique comment utiliser FTP ou FTPS pour déployer votre application web, votre backend d’applications mobiles ou votre application API dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

Le point de terminaison FTP/S de votre application est déjà actif. Aucune configuration n’est nécessaire pour activer le déploiement FTP/S.

> [!IMPORTANT]
> Nous prenons en permanence des mesures pour améliorer la sécurité de la plateforme Microsoft Azure. Dans le cadre de cet effort continu, une mise à niveau des Applications Web est prévue pour les régions d’Allemagne centrale et d’Allemagne du nord-est. L’utilisation du protocole FTP en texte brut pour les déploiements est désactivée pendant cette Web Apps. La recommandation faite à nos clients consiste à basculer vers FTPS pour les déploiements. Aucune interruption de votre service n’aura lieu pendant cette mise à niveau qui est prévue le 5 septembre. Nous vous remercions du soutien apporté à cet effort.

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>Étape 1 : définir les informations d’identification du déploiement

Pour accéder au serveur FTP de votre application, vous devez disposer des informations d’identification du déploiement. 

Pour définir ou réinitialiser vos informations d’identification du déploiement, consultez [Informations d’identification du déploiement d’Azure App Service](app-service-deployment-credentials.md). Ce didacticiel illustre l’utilisation des informations d’identification de niveau utilisateur.

## <a name="step-2-get-ftp-connection-information"></a>Étape 2 : obtenir les informations de connexion FTP

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le [panneau des ressources](../azure-resource-manager/resource-group-portal.md#manage-resources) de votre application.
2. Sélectionnez **Vue d’ensemble** dans le menu de gauche, puis notez les valeurs de **Utilisateur FTP/de déploiement**, **Nom de l'hôte FTP** et **Nom de l'hôte FTPS**. 

    ![Informations de connexion FTP](./media/app-service-deploy-ftp/FTP-Connection-Info.PNG)

    > [!NOTE]
    > Valeur **Utilisateur FTP/de déploiement** affichée par le portail Azure, avec le nom de l’application, afin de fournir le contexte approprié au serveur FTP.
    > Vous pouvez obtenir les mêmes informations en sélectionnant **Propriétés** dans le menu de gauche. 
    >
    > En outre, le mot de passe du déploiement n’est jamais affiché. Si vous oubliez le mot de passe de votre déploiement, revenez à l’[étape 1](#step1) et réinitialisez-le.
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>Étape 3 : déployer les fichiers sur Azure

1. À partir de votre client FTP ([Visual Studio](https://www.visualstudio.com/vs/community/), [FileZilla](https://filezilla-project.org/download.php?type=client), etc.), utilisez les informations de connexion que vous avez recueillies pour vous connecter à votre application.
3. Copiez vos fichiers et la structure de répertoire qui leur correspond dans le répertoire [**/site/wwwroot** dans Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (ou dans le répertoire **/site/wwwroot/App_Data/Jobs/** pour WebJobs).
4. Accédez à l’URL de votre application pour vérifier que l’application s’exécute correctement. 

> [!NOTE] 
> Contrairement aux [déploiements Git](app-service-deploy-local-git.md), un déploiement FTP ne prend pas en charge les automatisations de déploiement suivantes : 
>
> - restauration de dépendances (par exemple, des automatisations NuGet, NPM, PIP et Composer)
> - compilation de fichiers binaires .NET
> - génération du fichier web.config (dont voici un [exemple Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Vous devez restaurer, créer, générer ces fichiers requis manuellement sur votre ordinateur local et les déployer avec votre application.
>
>

## <a name="next-steps"></a>Étapes suivantes

Pour des scénarios de déploiement plus avancés, consultez [Déploiement Git local vers Azure App Service](app-service-deploy-local-git.md). Le déploiement GIT vers Azure autorise le contrôle de version, la restauration du package, MSBuild et bien plus encore.

## <a name="more-resources"></a>Autres ressources

* [Informations d’identification du déploiement d’Azure App Service](app-service-deploy-ftp.md)

