---
title: Remise continue pour les services cloud avec Visual Studio Online | Microsoft Docs
description: "Apprenez à configurer la remise continue pour les applications cloud Azure sans enregistrer la clé de stockage de diagnostic sur les fichiers de configuration de service"
services: cloud-services
documentationcenter: 
author: cawa
manager: paulyuk
editor: 
ms.assetid: 148b2959-c5db-4e4a-a7e9-fccb252e7e8a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/02/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 165ab7363efaf90eaab41098f71e2f1b846c346e
ms.openlocfilehash: 7e70f92d4d1333ca6cbac5876e5ccbc763bd915c

---
# <a name="securely-save-cloud-services-diagnostics-storage-key-and-setup-continuous-integration-and-deployment-to-azure-using-visual-studio-online"></a>Enregistrer en toute sécurité la clé de stockage de diagnostic des services cloud et configurer l’intégration et le déploiement continus dans Azure à l’aide de Visual Studio Online
 Il est aujourd’hui courant d’ouvrir les projets source. L’enregistrement des secrets de l’application dans les fichiers de configuration n’est plus sûr, dans la mesure où des failles de sécurité peuvent provoquer la révélation des secrets à partir des contrôles de code source publics. Le stockage des secrets sous forme de texte brut dans un fichier situé dans un pipeline d’intégration continue n’est pas sûr non plus, car les serveurs de génération peuvent constituer des ressources partagées dans l’environnement cloud. Cet article explique comment Visual Studio et Visual Studio Online limitent les risques liés à la sécurité lors du développement et du processus d’intégration continue.

## <a name="remove-diagnostics-storage-key-secret-in-project-configuration-file"></a>Supprimer le secret de clé de stockage de diagnostic dans le fichier de configuration de projet
L’extension des diagnostics des services cloud nécessite un stockage Azure pour l’enregistrement des résultats des diagnostics. La chaîne de connexion au stockage était par le passé spécifiée dans les fichiers de configuration des services cloud (.cscfg) et pouvait être vérifiée via le contrôle du code source. Dans la dernière version du Kit de développement logiciel (SDK) Azure, le comportement a été modifié pour stocker uniquement une chaîne de connexion partielle, la clé étant remplacée par un jeton. Les étapes suivantes décrivent le fonctionnement des nouveaux outils de services cloud :

### <a name="1-open-the-role-designer"></a>1. Ouvrez le Concepteur de rôle
* Double-cliquez ou cliquez avec le bouton droit de la souris sur un rôle de service cloud pour ouvrir le Concepteur de rôle

![Ouvrez le Concepteur de rôle][0]

### <a name="2-under-diagnostics-section-a-new-check-box-dont-remove-storage-key-secret-from-project-is-added"></a>2. Dans la section Diagnostics, une nouvelle case à cocher « Ne pas supprimer le secret de clé de stockage du projet » a été ajoutée.
* Si vous utilisez l’émulateur de stockage local, cette case est désactivée, car il n’existe aucun secret à gérer pour la chaîne de connexion locale, qui est UseDevelopmentStorage=true.

![La chaîne de connexion de l’émulateur de stockage local n’est pas secrète][1]

* Si vous créez un nouveau projet, cette case est désactivée par défaut. De ce fait, la section Clé de stockage de la chaîne de connexion de stockage sélectionnée est remplacée par un jeton. La valeur du jeton se trouve sous le dossier AppData Roaming de l’utilisateur actuel, par exemple : C:\Utilisateurs\contosouser\AppData\Roaming\Microsoft\CloudService

> Notez que l’accès au dossier user\AppData est contrôlé par les droits de l’utilisateur connecté et est considéré comme un emplacement sécurisé pour stocker les secrets de développement.
> 
> 

![La clé de stockage est enregistrée dans le dossier du profil utilisateur][2]

### <a name="3-select-a-diagnostics-storage-account"></a>3. Sélectionnez un compte de stockage de diagnostics
* Sélectionnez un compte de stockage à partir de la boîte de dialogue ouverte en cliquant sur « ... » . Notez que la chaîne de connexion de stockage générée n’a pas la clé de compte de stockage.
* Par exemple : DefaultEndpointsProtocol=https;AccountName=contosostorage;AccountKey=$(*clouddiagstrg.key*)

### <a name="4----debugging-the-project"></a>4.    Débogage du projet
* F5 pour lancer le débogage dans Visual Studio. Tout doit fonctionner comme avant.
  ![Démarrer le débogage local][3]

### <a name="5-publish-project-from-visual-studio"></a>5. Publier un projet à partir de Visual Studio
* Ouvrez la boîte de dialogue Publier et suivez les instructions de connexion pour publier l’application sur Azure.

### <a name="6-additional-information"></a>6. Informations supplémentaires
> Remarque : le panneau Paramètres du Concepteur de rôle reste inchangé pour l’instant. Si vous souhaitez utiliser la fonctionnalité de gestion des secrets pour les diagnostics, accédez à l’onglet Configurations.
> 
> 

![Ajouter des paramètres][4]

> Remarque : si cette option est activée, la clé Application Insights sera stockée en tant que texte brut. La clé est uniquement utilisée pour télécharger le contenu. Il n’existe donc aucun risque de compromission des données sensibles.
> 
> 

## <a name="build-and-publish-a-cloud-services-project-using-visual-studio-online-task-templates"></a>Créer et publier un projet de service cloud à l’aide des modèles de tâches en ligne Visual Studio
* Les étapes suivantes expliquent comment configurer l’intégration continue pour un projet de service cloud à l’aide des tâches en ligne Visual Studio :
  ### <a name="1----obtain-a-vso-account"></a>1.    Obtenir un compte VSO
* [Créer un compte Visual Studio Online][Créer un compte Visual Studio Online] si vous n’en avez pas encore
* [Créer un projet d’équipe][Créer un projet d’équipe] dans votre compte Visual Studio Online

### <a name="2----setup-source-control-in-visual-studio"></a>2.    Configurer le contrôle de code source dans Visual Studio
* Connectez-vous à un projet d’équipe

![Connectez-vous à un projet d’équipe][5]

![Sélectionnez le projet d’équipe auquel vous connecter][6]

* Ajoutez votre projet au contrôle de code source

![Ajoutez le projet au contrôle de code source][7]

![Mappez le projet vers un dossier de contrôle de code source][8]

* Archivez votre projet à partir de Team Explorer

![Archivez le projet dans le contrôle de code source][9]

### <a name="3----configure-build-process"></a>3.    Configurer le processus de génération
* Accédez à votre projet d’équipe et ajoutez un nouveau modèle de processus de génération

![Ajoutez une nouvelle génération][10]

* Sélectionnez une tâche de génération

![Ajoutez une tâche de génération][11]

![Sélectionnez le modèle de tâche de génération Visual Studio][12]

* Modifiez l’entrée de tâche de génération. Veuillez personnaliser les paramètres de génération en fonction de vos besoins

![Configurez la tâche de génération][13]

`/t:Publish /p:TargetProfile=$(targetProfile) /p:DebugType=None /p:SkipInvalidConfigurations=true /p:OutputPath=bin\ /p:PublishDir="$(build.artifactstagingdirectory)\\"`

* Configurez les variables de génération

![Configurez les variables de génération][14]

* Ajoutez une tâche pour charger la cible de génération

![Choisissez de publier la tâche de cible de génération][15]

![Configurez la publication de la tâche de cible de génération][16]

* Exécutez la génération

![Mettez la nouvelle génération en file d’attente][17]

![Affichez le résumé de génération][18]

* Si la génération est réussie, vous verrez un résultat semblable à celui-ci

![Résultat de la génération][19]

### <a name="4----configure-release-process"></a>4.    Configurez un processus de publication
* Créez une nouvelle version

![Créez une nouvelle version][20]

* Sélectionnez la tâche de déploiement des services cloud Azure

![Sélectionnez la tâche de déploiement des services cloud Azure][21]

* La clé de compte de stockage n’étant pas archivé dans le contrôle de code source, nous devons spécifier la clé secrète pour la configuration des extensions des diagnostics. Développez la section **Options avancées pour la création d’un service** et modifiez l’entrée de paramètre **Clés de compte de stockage de diagnostics**. Cette entrée inclut plusieurs lignes de la paire clé / valeur au format **[RoleName]:$(StorageAccountKey)**

> Remarque : si votre compte de stockage de diagnostics est dans le même abonnement que celui où vous allez publier l’application de services cloud, il est inutile d’entrer la clé dans l’entrée de tâche de déploiement ; le déploiement obtiendra automatiquement les informations de stockage à partir de votre abonnement
> 
> 

![Configurez la tâche de déploiement des services cloud][22]

* Utilisez les variables de génération secrètes pour enregistrer les clés de stockage. Pour masquer une variable secrète, cliquez sur l’icône de verrou sur le côté droit de l’entrée Variables

![Enregistrez les clés de stockage dans les variables de génération secrètes][23]

* Créez une version et déployez votre projet dans Azure

![Créez une nouvelle version][24]

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la configuration des extensions de diagnostics pour les services cloud Azure, consultez [Activer les diagnostics dans les services cloud Azure à l’aide de PowerShell][Activer les diagnostics dans les services cloud Azure à l’aide de PowerShell]

[Créer un compte Visual Studio Online]:https://www.visualstudio.com/team-services/
[Créer un projet d’équipe]: https://www.visualstudio.com/it-it/docs/setup-admin/team-services/connect-to-visual-studio-team-services
[Activer les diagnostics dans les services cloud Azure à l’aide de PowerShell]:https://azure.microsoft.com/en-us/documentation/articles/cloud-services-diagnostics-powershell/

[0]: ./media/cloud-services-vs-ci/vs-01.png
[1]: ./media/cloud-services-vs-ci/vs-02.png
[2]: ./media/cloud-services-vs-ci/file-01.png
[3]: ./media/cloud-services-vs-ci/vs-03.png
[4]: ./media/cloud-services-vs-ci/vs-04.png
[5]: ./media/cloud-services-vs-ci/vs-05.png
[6]: ./media/cloud-services-vs-ci/vs-06.png
[7]: ./media/cloud-services-vs-ci/vs-07.png
[8]: ./media/cloud-services-vs-ci/vs-08.png
[9]: ./media/cloud-services-vs-ci/vs-09.png
[10]: ./media/cloud-services-vs-ci/vso-01.png
[11]: ./media/cloud-services-vs-ci/vso-02.png
[12]: ./media/cloud-services-vs-ci/vso-03.png
[13]: ./media/cloud-services-vs-ci/vso-04.png
[14]: ./media/cloud-services-vs-ci/vso-05.png
[15]: ./media/cloud-services-vs-ci/vso-06.png
[16]: ./media/cloud-services-vs-ci/vso-07.png
[17]: ./media/cloud-services-vs-ci/vso-08.png
[18]: ./media/cloud-services-vs-ci/vso-09.png
[19]: ./media/cloud-services-vs-ci/vso-10.png
[20]: ./media/cloud-services-vs-ci/vso-11.png
[21]: ./media/cloud-services-vs-ci/vso-12.png
[22]: ./media/cloud-services-vs-ci/vso-13.png
[23]: ./media/cloud-services-vs-ci/vso-14.png
[24]: ./media/cloud-services-vs-ci/vso-15.png



<!--HONumber=Nov16_HO3-->


