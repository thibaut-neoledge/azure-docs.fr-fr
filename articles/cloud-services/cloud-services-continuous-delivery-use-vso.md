---
title: Livraison continue avec Visual Studio Team Services dans Azure | Microsoft Docs
description: "Découvrez comment configurer vos projets d&quot;équipe Visual Studio Team Services afin de les générer et de les déployer automatiquement vers la fonctionnalité Web App d’Azure App Service ou des Cloud Services."
services: cloud-services
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: 797f67ad-e4d4-4063-ae91-41cbdf154191
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/06/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: d80ce63eb7ddfd7c45726be887a772f9a7594b28
ms.lasthandoff: 04/06/2017


---
# <a name="continuous-delivery-to-azure-using-visual-studio-team-services"></a>Diffusion continue sur Azure au moyen de Visual Studio Team Services
Vous pouvez configurer vos projets Visual Studio Team Services afin de les générer et de les déployer automatiquement sur des applications Web Azure ou des Cloud Services.  (Pour plus d'informations sur la procédure à suivre pour configurer un système de génération et de déploiement continus au moyen d'un serveur TFS *local* , consultez la rubrique [Remise continue pour Cloud Services dans Azure](cloud-services-dotnet-continuous-delivery.md).)

Ce didacticiel part du principe que vous avez déjà installé Visual Studio 2013 et le Kit de développement logiciel (SDK) Azure sur votre système. Si Visual Studio 2013 n'est pas déjà installé, téléchargez-le en choisissant le lien **Test gratuit de Visual Studio** sur [www.visualstudio.com](http://www.visualstudio.com). Pour installer le Kit de développement logiciel (SDK) Azure, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=239540).

> [!NOTE]
> Vous avez besoin d’un compte Visual Studio Team Services pour suivre ce didacticiel : vous pouvez [ouvrir un compte Visual Studio Team Services gratuit](http://go.microsoft.com/fwlink/p/?LinkId=512979).
> 
> 

Pour configurer un service cloud permettant de générer et de déployer automatiquement sur Azure au moyen de Visual Studio Team Services, suivez ces étapes.

## <a name="1-create-a-team-project"></a>1 : Création d'un projet d'équipe
Suivez les instructions disponibles [ici](http://go.microsoft.com/fwlink/?LinkId=512980) pour créer votre projet d'équipe et l'associer à Visual Studio. Cette procédure pas à pas part du principe que vous utilisez TFVC (Team Foundation Version Control) en tant que solution de contrôle de code source. Si vous souhaitez utiliser Git pour le contrôle de version, consultez [la version Git de cette procédure pas à pas](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## <a name="2-check-in-a-project-to-source-control"></a>2 : Archivage d'un projet dans le contrôle de code source
1. Dans Visual Studio, ouvrez la solution à déployer, ou créez-en une.
   Vous pouvez déployer une application Web ou un service cloud (application Azure) en suivant les étapes de cette procédure.
   Si vous voulez créer une solution, créez un projet de service cloud Azure ou ASP.NET MVC. Vérifiez que le projet cible .NET Framework 4 ou 4.5, et si vous créez un projet de service cloud, ajoutez un rôle Web ASP.NET MVC et un rôle de travail, et choisissez Application Internet pour le rôle Web. Lorsque vous y êtes invité, choisissez **Application Internet**.
   Si vous voulez créer une application Web, choisissez le modèle de projet Application Web ASP.NET, puis sélectionnez MVC. Consultez la rubrique [Création d’une application Web ASP.NET dans Azure App Service](../app-service-web/app-service-web-get-started-dotnet.md).
   
   > [!NOTE]
   > Actuellement, Visual Studio Team Services ne prend en charge que les déploiements CI des applications web Visual Studio. Les projets de site web n’entrent pas dans ce cadre.
   > 
   > 
2. Ouvrez le menu contextuel pour la solution et sélectionnez **Ajouter la solution au contrôle de code source**.
   
    ![][5]
3. Acceptez ou modifiez les valeurs par défaut et choisissez le bouton **OK** . Au terme du processus, les icônes du contrôle de code source apparaissent dans l' **Explorateur de solutions**.
   
    ![][6]
4. Ouvrez le menu contextuel de la solution et choisissez **Archiver**.
   
    ![][7]
5. Dans la zone **Modifications en attente** de **Team Explorer**, tapez un commentaire pour l’archivage et choisissez le bouton **Archiver**.
   
    ![][8]
   
    Notez les options à inclure ou exclure quand vous archivez des modifications spécifiques. Si des modifications voulues sont exclues, choisissez le lien **Tout inclure** .
   
    ![][9]

## <a name="3-connect-the-project-to-azure"></a>3 : Connexion du projet à Azure
1. Maintenant que vous disposez d’un projet d’équipe Visual Studio Team Services contenant du code source, vous êtes prêt à connecter votre projet d’équipe à Azure.  Dans le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885), sélectionnez votre service cloud ou application web, ou créez-en un en sélectionnant l’icône **+** en bas à gauche et en choisissant **Service cloud** ou **Application web**, puis **Création rapide**. Choisissez le lien **Configurer la publication avec Visual Studio Team Services** .
   
    ![][10]
2. Dans l'Assistant, tapez le nom de votre compte Visual Studio Team Services dans la zone de texte et cliquez sur le lien **Autoriser maintenant** . Vous serez peut-être invité à vous connecter.
   
    ![][11]
3. Dans la boîte de dialogue contextuelle **Demande de connexion**, choisissez le bouton **Accepter** pour autoriser Azure à configurer votre projet d’équipe dans Visual Studio Team Services.
   
    ![][12]
4. Si le processus d'autorisation aboutit, une zone déroulante affiche une liste de vos projets Visual Studio Team Services. Choisissez le nom du projet d’équipe que vous avez créé aux étapes précédentes et choisissez la coche de l’Assistant.
   
    ![][13]
5. Une fois votre projet lié, des instructions s'affichent pour vous permettre d'archiver les modifications dans votre projet d'équipe Visual Studio Team Services.  Lors du prochain archivage, Visual Studio Team Services générera et déploiera votre projet sur Azure.  Essayez maintenant en cliquant sur le lien **Archiver depuis Visual Studio**, puis sur le lien **Lancer Visual Studio** (ou le bouton **Visual Studio** équivalent en bas de l’écran du portail).
   
    ![][14]

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4 : Déclenchement d'une régénération et redéploiement de votre projet
1. Dans Visual Studio **Team Explorer**, choisissez le lien **Explorateur du contrôle de code source**.
   
    ![][15]
2. Accédez à votre fichier solution et ouvrez-le.
   
    ![][16]
3. Dans l' **Explorateur de solutions**, ouvrez un fichier et modifiez-le. Par exemple, modifiez le fichier `_Layout.cshtml` sous le dossier Views\\Shared dans un rôle Web MVC.
   
    ![][17]
4. Modifiez le logo du site et appuyez sur **Ctrl+S** pour enregistrer le fichier.
   
    ![][18]
5. Dans **Team Explorer**, choisissez le lien **Modifications en attente**.
   
    ![][19]
6. Entrez un commentaire et choisissez le bouton **Archiver** .
   
    ![][20]
7. Choisissez le bouton **Accueil** pour revenir à la page d’accueil de **Team Explorer**.
   
    ![][21]
8. Choisissez le lien **Builds** pour afficher les builds en cours.
   
    ![][22]
   
    **Team Explorer** indique qu’une build est disponible pour archivage.
   
    ![][23]
9. Double-cliquez sur le nom de la build en cours pour afficher un journal détaillé lors du processus de génération.
   
    ![][24]
10. Pendant la création de la build, examinez la définition de build qui a été créée lorsque vous avez lié TFS à Azure au moyen de l'Assistant.  Ouvrez le menu contextuel de la définition de build et choisissez **Modifier la définition de build**.
    
     ![][25]
    
     Dans l’onglet **Déclencher** , vous allez voir que la définition de build prévoit par défaut un processus de génération pour chaque archivage.
    
     ![][26]
    
     Dans l'onglet **Processus** , vous pouvez voir que l'environnement de déploiement est défini sur le nom de votre service cloud ou application Web. Si vous utilisez des applications Web, les propriétés affichées seront différentes de celles figurant ici.
    
     ![][27]
11. Spécifiez des valeurs pour les propriétés si vous souhaitez d'autres valeurs que celles par défaut. Les propriétés pour la publication Azure se trouvent dans la section **Déploiement** .
    
     Le tableau suivant présente les propriétés disponibles dans la section **Déploiement** :
    
    | Propriété | Valeur par défaut |
    | --- | --- |
    | Autoriser les certificats non approuvés |Si cette propriété a la valeur false, des certificats SSL doivent être signés par une autorité racine. |
    | Autoriser la mise à niveau |Permet au déploiement de mettre à jour un déploiement existant au lieu d'en créer un. Conserve l'adresse IP. |
    | Ne pas supprimer |Si cette propriété a la valeur true, ne remplacez pas un déploiement sans rapport (la mise à niveau est autorisée). |
    | Chemin d'accès des paramètres de déploiement |Chemin d’accès à votre fichier .pubxml pour un site Web, relatif au dossier racine du référentiel. Ignorée pour les services cloud. |
    | Environnement de déploiement SharePoint |Identique au nom du service. |
    | Environnement de déploiement Azure |Nom de l'application Web ou du service cloud. |
12. Si vous utilisez plusieurs configurations de service (fichiers .cscfg), vous pouvez spécifier la configuration du service désiré dans le paramètre **Build, Advanced, MSBuild arguments** . Par exemple, pour utiliser ServiceConfiguration.Test.cscfg, définissez l'option de ligne d'argument MSBuild `/p:TargetProfile=Test`.
    
     ![][38]
    
     À ce stade, la création de la build doit être terminée.
    
     ![][28]
13. Si vous double-cliquez sur le nom de la build, Visual Studio affiche un **Résumé de la build**, y compris tous les résultats de test provenant de projets de test unitaires associés.
    
     ![][29]
14. Dans le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885), vous pouvez afficher le déploiement associé sous l’onglet **Déploiements** quand l’environnement intermédiaire est sélectionné.
    
     ![][30]
15. Accédez à l'URL de votre site. Dans le cas d'une application Web, cliquez simplement sur le bouton **Parcourir** dans la barre de commandes. Dans le cas d’un service cloud, choisissez l’URL dans la section **Aperçu rapide** de la page **Tableau de bord** représentant l’environnement intermédiaire d’un service cloud. Les déploiements résultant de l'intégration continue de services cloud sont publiés dans l'environnement intermédiaire par défaut. Vous pouvez changer cela en définissant la propriété **Environnement du service cloud de substitution** sur **Production**. Cet écran indique où se trouve l'URL de site dans la page de tableau de bord du service cloud.
    
    ![][31]
    
    Un nouvel onglet de navigateur apparaît pour afficher votre site en cours d'exécution.
    
    ![][32]
    
    Pour les services cloud, si vous apportez d'autres modifications à votre projet, vous déclenchez d'autres builds et vous accumulez plusieurs déploiements. Le plus récent est marqué comme étant actif.
    
    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5 : Redéploiement d'une build antérieure
Cette étape (facultative) s'applique aux services cloud. Dans le portail Azure Classic, choisissez un déploiement antérieur et cliquez sur le bouton **Redéployer** pour revenir à un archivage antérieur de votre site.  Notez que cela déclenche une nouvelle génération dans TFS et crée une entrée dans l’historique de votre déploiement.

![][34]

## <a name="6-change-the-production-deployment"></a>6 : Modification du déploiement de production
Cette étape s'applique uniquement aux services cloud, pas aux applications Web. Une fois que vous êtes prêt, vous pouvez promouvoir l'environnement intermédiaire en environnement de production en choisissant le bouton **Swap** dans le portail Azure Classic. L'environnement intermédiaire récemment déployé passe à l'état de production et l'environnement de production précédent, le cas échéant, devient un environnement intermédiaire. Le déploiement actif peut être différent pour les environnements de production et intermédiaire, mais l'historique de déploiement des builds récentes est identique quel que soit l'environnement.

![][35]

## <a name="7-run-unit-tests"></a>7 : Exécution de tests unitaires
Cette étape s'applique uniquement aux applications Web, et non aux services cloud. Pour mettre en place un « portail de qualité » dans votre déploiement, vous pouvez exécuter des tests unitaires. S'ils échouent, vous pouvez interrompre le déploiement.

1. Dans Visual Studio, ajoutez un projet de test unitaire.
   
   ![][39]
2. Ajoutez les références de projet au projet que vous souhaitez tester.
   
   ![][40]
3. Ajoutez quelques tests unitaires. Pour commencer, essayez de réaliser un faux test qui réussira toujours.
   
       ```
       using System;
       using Microsoft.VisualStudio.TestTools.UnitTesting;
   
       namespace UnitTestProject1
       {
           [TestClass]
           public class UnitTest1
           {
               [TestMethod]
               [ExpectedException(typeof(NotImplementedException))]
               public void TestMethod1()
               {
                   throw new NotImplementedException();
               }
           }
       }
       ```
4. Modifiez la définition de la build, choisissez l’onglet **Processus** et étendez le nœud de **test**.
5. Définissez le paramètre **Fail build on test failure** sur True. Cela signifie que le déploiement ne sera pas réalisé à moins que le test ne réussisse.
   
   ![][41]
6. Placez une nouvelle build dans la file d'attente.
   
   ![][42]
   
   ![][43]
7. Pendant que la build est en cours de traitement, suivez sa progression.
   
    ![][44]
   
    ![][45]
8. Lorsque la build est terminée, consultez les résultats de test.
   
    ![][46]
   
    ![][47]
9. Essayez de créer un nouveau test qui échouera. Ajoutez un nouveau test en copiant le premier, en le renommant et en plaçant en commentaire la ligne de code indiquant que NotImplementedException est une exception attendue.
   
       ```
       [TestMethod]
       //[ExpectedException(typeof(NotImplementedException))]
       public void TestMethod2()
       {
           throw new NotImplementedException();
       }
       ```
10. Archivez le changement pour mettre une nouvelle build dans la file d'attente.
    
     ![][48]
11. Consultez les résultats du test pour obtenir des détails sur l'échec.
    
     ![][49]
    
     ![][50]

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur le test unitaire dans Visual Studio Team Services, consultez [Exécuter des tests unitaires dans votre build](http://go.microsoft.com/fwlink/p/?LinkId=510474). Si vous utilisez Git, consultez les rubriques [Partager votre code dans Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) et [Déploiement continu vers Azure App Service](../app-service-web/app-service-continuous-deployment.md).  Pour en savoir plus sur Visual Studio Team Services, consultez [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG

