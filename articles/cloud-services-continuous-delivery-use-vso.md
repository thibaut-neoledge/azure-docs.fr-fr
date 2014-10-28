<properties linkid="dev-net-common-tasks-publishing-with-vso" urlDisplayName="Publishing with Visual Studio Online" pageTitle="Continuous delivery with Visual Studio Online in Azure" metaKeywords="" description="Learn how to configure your Visual Studio Online team projects to automatically build and deploy to Azure websites or cloud services." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Continuous delivery to Azure using Visual Studio Online" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen"></tags>

# Livraison continue sur Azure au moyen de Visual Studio Online

Vous pouvez configurer vos projets d'équipe Visual Studio Online afin de les générer et de les déployer automatiquement sur des sites Web Azure ou des services cloud. (Pour plus d'informations sur la procédure à suivre pour configurer un système de génération et de déploiement continus au moyen d'un serveur TFS *local*, consultez la rubrique [Remise continue pour Cloud Services dans Azure][].)

Ce didacticiel part du principe que vous avez déjà installé Visual Studio 2013 et le Kit de développement logiciel (SDK) Azure sur votre système. Si Visual Studio 2013 n'est pas déjà installé, téléchargez-le en choisissant le lien **Test gratuit de Visual Studio** sur [www.visualstudio.com][]. Pour installer le Kit de développement logiciel (SDK) Azure, cliquez [ici][].

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Pour suivre ce didacticiel, vous avez besoin d'un compte Visual Studio Online&nbsp;:</h5>
<p>Vous pouvez <a href="http://go.microsoft.com/fwlink/p/?LinkId=512979">ouvrir gratuitement un compte Visual Studio Online</a>.</p>
</div>

Pour configurer un service cloud permettant de générer et de déployer automatiquement sur Azure au moyen de Visual Studio Online, procédez comme suit :

-   [Étape 1 : création d'un projet d'équipe][]

-   [Étape 2 : archivage d'un projet dans le contrôle de code source][]

-   [Étape 3 : connexion du projet à Azure][]

-   [Étape 4 : exécution des modifications et déclenchement d'une régénération et d'un redéploiement][]

-   [Étape 5 : redéploiement d'une build antérieure (facultatif)][]

-   [Étape 6 : modification du déploiement de production (services cloud uniquement)][]

-   [Étape 7 : exécution de tests unitaires (facultatif)][]

## <a name="step1"></a><span class="short-header">Création d'un projet d'équipe</span>Étape 1 : création d'un projet d'équipe

Suivez les instructions disponibles [ici][1] pour créer votre projet d'équipe et l'associer à Visual Studio. Cette procédure pas à pas part du principe que vous utilisez TFVC (Team Foundation Version Control) en tant que solution de contrôle de code source. Si vous souhaitez utiliser Git pour le contrôle de version, consultez [la version Git de cette procédure pas à pas][].

## <a name="step2"> </a><span class="short-header">Archivage d'un projet dans le contrôle de code source</span>Étape 2 : archivage d'un projet dans le contrôle de code source

1.  Dans Visual Studio, ouvrez la solution à déployer, ou créez-en une.
    Vous pouvez déployer un site Web ou un service cloud (application Azure) en suivant les étapes de cette procédure.
    Si vous voulez créer une solution, créez un projet de service cloud Azure
    ou ASP.NET MVC. Vérifiez que le projet cible .NET Framework 4 ou 4.5, et si vous créez un projet de service cloud, ajoutez un rôle Web ASP.NET MVC et un rôle de travail, et choisissez Application Internet pour le rôle Web. Lorsque vous y êtes invité, choisissez **Application Internet**.
    Si vous voulez créer un site Web, choisissez le modèle de projet Application Web ASP.NET, puis sélectionnez MVC. Consultez la page [Prise en main d'Azure et ASP.NET][].

2.  Ouvrez le menu contextuel pour la solution et sélectionnez **Ajouter la solution au contrôle de code source**.
  
  ![][]

3.  Acceptez ou modifiez les valeurs par défaut et choisissez le bouton **OK**. Au terme du processus, les icônes du contrôle de code source apparaissent dans l'Explorateur de solutions.
 
   ![][2]

4.  Ouvrez le menu contextuel de la solution et choisissez **Archiver**.
 
   ![][3]

5.  Dans la zone Modifications en attente de Team Explorer, tapez un commentaire pour l'archivage et choisissez le bouton **Archiver**.
 
   ![][4]

Remarquez les options permettant d'inclure ou d'exclure des modifications spécifiques lorsque vous archivez. Si des modifications souhaitées ont été exclues, choisissez le lien **Tout inclure**.

![][5]

## <a name="step3"> </a><span class="short-header">Connexion du projet à Azure</span>Étape 3 : connexion du projet à Azure

1.  Maintenant que vous disposez d'un projet d'équipe VSO contenant du code source, vous êtes prêt à connecter votre projet d'équipe à Azure. Dans le [portail Azure][], sélectionnez votre service cloud ou site Web, ou créez-en un en sélectionnant l'icône + en bas à gauche et en choisissant **Service cloud** ou **Site Web**, puis **Création rapide**. Choisissez le lien **Configurer la publication avec Visual Studio Online**.

   ![][6]

2.  Dans l'Assistant, tapez le nom de votre compte Visual Studio Online dans la zone de texte et cliquez sur le lien **Autoriser maintenant**. Vous serez peut-être invité à vous connecter.
 
   ![][7]

3.  Dans la boîte de dialogue contextuelle OAuth, choisissez **Accepter** pour configurer votre projet d'équipe dans VSO.
 
   ![][8]

4.  Si le processus d'autorisation aboutit, une zone déroulante affiche une liste de vos projets d'équipe Visual Studio Online. Sélectionnez le nom du projet d'équipe que vous avez créé aux étapes précédentes et choisissez la coche de l'Assistant.
  
  ![][9]

5.  Une fois votre projet lié, des instructions s'affichent pour vous permettre d'archiver les modifications dans votre projet d'équipe Visual Studio Online. Lors du prochain archivage, Visual Studio Online générera et déploiera votre projet sur Azure. Essayez maintenant en cliquant sur le lien **Archiver depuis Visual Studio**, puis sur le lien **Lancer Visual Studio** (ou le bouton **Visual Studio** équivalent en bas de l'écran du portail).

   ![][10]

## <a name="step4"> </a><span class="short-header">Déclenchement d'une régénération</span>Étape 4 : déclenchement d'une régénération d'un redéploiement de votre projet

1.  Dans Visual Studio Team Explorer, cliquez sur le lien **Explorateur du contrôle de code source**.
 
   ![][11]

2.  Accédez à votre fichier solution et ouvrez-le.

   ![][12]

3.  Dans l'Explorateur de solutions, ouvrez un fichier et modifiez-le. Par exemple, modifiez le fichier \_Layout.cshtml sous le dossier Views\\Shared dans un rôle Web MVC.
  
  ![][13]

4.  Modifiez le logo du site et appuyez sur Ctrl+S pour enregistrer le fichier.
  
  ![][14]

5.  Dans Team Explorer, choisissez le lien **Modifications en attente**.
  
  ![][15]

6.  Tapez un commentaire et choisissez le bouton **Archiver**.
 
   ![][16]

7.  Choisissez le bouton Accueil pour revenir à la page d'accueil de Team Explorer.
 
   ![][17]

8.  Choisissez le lien **Builds** pour afficher les builds en cours.
  
  ![][18]

    Team Explorer indique qu'une build est disponible pour archivage.
  
  ![][19]

9.  Double-cliquez sur le nom de la build en cours pour afficher un journal détaillé lors du processus de génération.
  
  ![][20]

10. Pendant la création de la build, examinez la définition de build qui a été créée lorsque vous avez lié TFS à Azure au moyen de l'Assistant. Ouvrez le menu contextuel de la définition de build et choisissez **Modifier la définition de build**.
  
  ![][21]

    Dans l'onglet **Déclencher**, vous allez voir que la définition de build prévoit par défaut un processus de génération pour chaque archivage.
  
  ![][22]

    Dans l'onglet **Processus**, vous pouvez voir que l'environnement de déploiement est défini sur le nom de votre service cloud ou site Web. Si vous travaillez avec des sites Web, les propriétés que vous verrez seront différentes de celles affichées ici.
   
  ![][23]


    Spécifiez des valeurs pour les propriétés si vous souhaitez d'autres valeurs que celles par défaut. Les propriétés pour la publication Azure se trouvent dans la section Déploiement.
    Le tableau suivant présente les propriétés disponibles dans la section Déploiement :
  
<table>
<tr>
<td>
<strong>Propriété</strong>
</td>
<td>
<strong>Valeur par défaut</strong>
</td>
</tr>
</p>
<tr>
<td>
Autoriser les certificats non reconnus

</td>
<td>
Si la valeur est false, les certificats SSL doivent être signés par une autorité racine.

</td>
</tr>
<tr>
<td>
Autoriser la mise à niveau

</td>
<td>
Autorise le déploiement à mettre à jour un déploiement existant au lien d'en créer un nouveau. Préserve l’adresse IP.

</td>
</tr>
<tr>
<td>
Ne pas supprimer

</td>
<td>
Si la valeur est true, n'écrase pas un déploiement non lié existant (la mise à jour est autorisée).

</td>
</tr>
<tr>
<td>
Chemin vers les paramètres de déploiement

</td>
<td>
Le chemin vers votre fichier .pubxml pour un site Web, relatif au répertoire racine du référentiel. Ignoré pour les services cloud.

</td>
</tr>
<tr>
<td>
Environnement de déploiement Sharepoint

</td>
<td>
Identique au nom du service

</td>
</tr>
<tr>
<td>
Environnement de déploiement Windows Azure

</td>
<td>
Le nom du site Web ou du service cloud

</td>
</tr>
</table>

Si vous utilisez plusieurs configurations de service (fichiers .cscfg), vous pouvez spécifier la configuration du service désiré dans le paramètre **Build, Advanced, MSBuild arguments**. Par exemple, pour utiliser ServiceConfiguration.Test.cscfg, définissez l'option de ligne d'argument MSBuild /p:TargetProfile=Test.

![][24]

1.  À ce stade, la création de la build doit être terminée.
  
  ![][25]

2.  Si vous double-cliquez sur le nom de la build, Visual Studio affiche un **Résumé de la build**, y compris tous les résultats de test provenant de projets de test unitaires associés.
  
  ![][26]

3.  Dans le [portail Azure][], vous pouvez afficher le déploiement associé sous l'onglet Déploiements lorsque l'environnement intermédiaire est sélectionné.

    ![][27]

4.  Accédez à l'URL de votre site. Dans le cas d'un site Web, cliquez simplement sur le bouton Parcourir dans la barre de commandes. Dans le cas d'un service cloud, choisissez l'URL dans la section **Aperçu rapide** de la page **Tableau de bord** représentant l'environnement intermédiaire d'un service cloud. Les déploiements résultant de l'intégration continue de services cloud sont publiés dans l'environnement intermédiaire par défaut. Vous pouvez changer cela en définissant la propriété Environnement du service cloud de substitution sur Production. Cet écran indique où se trouve l'URL de site dans la page de tableau de bord du service cloud :
 
   ![][28]

    Un nouvel onglet de navigateur apparaît pour afficher votre site en cours d'exécution.
  
  ![][29]

5.  Pour les services cloud, si vous apportez d'autres modifications à votre projet, vous déclenchez d'autres builds et vous accumulez plusieurs déploiements. Le plus récent est marqué comme étant actif.

    ![][30]

## <a name="step5"> </a><span class="short-header">Redéploiement d'une build antérieure</span>Étape 5 : redéploiement d'une build antérieure

Cette étape (facultative) s'applique aux services cloud. Dans le portail de gestion, sélectionnez un déploiement antérieur et cliquez sur le bouton **Redéployer** pour revenir à un archivage antérieur de votre site. Notez que cette action va déclencher une nouvelle build dans TFS, et créer une nouvelle entrée dans l'historique de vos déploiements.

![][31]

## <a name="step6"> </a><span class="short-header">Modification du déploiement de production</span>Étape 6 : modification du déploiement de production

Cette étape s'applique uniquement aux services cloud, pas aux sites Web. Une fois que vous êtes prêt, vous pouvez promouvoir l'environnement intermédiaire en environnement de production en choisissant le bouton Swap dans le portail de gestion. L'environnement intermédiaire récemment déployé passe à l'état de production et l'environnement de production précédent, le cas échéant, devient un environnement intermédiaire. Le déploiement actif peut être différent pour les environnements de production et intermédiaire, mais l'historique de déploiement des builds récentes est identique quel que soit l'environnement.

![][32]

## <a name="step7"> </a><span class="short-header">Exécution de tests unitaires</span>Étape 7 : exécution de tests unitaires

Pour mettre en place un « portail de qualité » dans vos déploiements intermédiaires ou instantanés, vous pouvez exécuter des tests unitaires. S'ils échouent, vous pouvez interrompre le déploiement.

1.  Dans Visual Studio, ajoutez un projet de test unitaire.
  
  ![][33]

2.  Ajoutez les références de projet aux projets que vous souhaitez tester.
  
  ![][34]

3.  Ajoutez quelques tests unitaires. Pour commencer, essayez de réaliser un faux test qui réussira toujours.

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

4.  Modifiez la définition de la build, choisissez l'onglet Processus et étendez le nœud de test.

5.  Définissez le paramètre **Fail build on test failure** sur True. Cela signifie que le déploiement ne sera pas réalisé à moins que le test ne réussisse.
 
   ![][35]

6.  Placez une nouvelle build dans la file d'attente.
 
   ![][36]
 
   ![][37]

7.  Pendant que la build est en cours de traitement, suivez sa progression.
 
   ![][38]

   ![][39]

8.  Lorsque la build est terminée, consultez les résultats de test.
  
  ![][40]

   ![][41]

9.  Essayez de créer un nouveau test qui échouera. Ajoutez un nouveau test en copiant le premier, en le renommant et en plaçant la ligne de code NotImplementedException en commentaire.

        [TestMethod]
        [ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            //throw new NotImplementedException();
        }

10. Archivez le changement pour mettre une nouvelle build dans la file d'attente.
  
  ![][42]

11. Consultez les résultats du test pour obtenir des détails sur l'échec.
 
   ![][43]
 
   ![][44]

Pour en savoir plus sur le test unitaire dans Visual Studio Online, consultez [Exécuter des tests unitaires dans votre build][].

Pour plus d'informations, consultez la page [Visual Studio Online][]. Si vous utilisez Git, consultez les pages [Partagez votre code dans Git][] et [Publication à partir du contrôle de code source sur des sites Web Azure][].

  [Remise continue pour Cloud Services dans Azure]: ../cloud-services-dotnet-continuous-delivery
  [www.visualstudio.com]: http://www.visualstudio.com
  [ici]: http://go.microsoft.com/fwlink/?LinkId=239540
  [ouvrir gratuitement un compte Visual Studio Online]: http://go.microsoft.com/fwlink/p/?LinkId=512979
  [Étape 1 : création d'un projet d'équipe]: #step1
  [Étape 2 : archivage d'un projet dans le contrôle de code source]: #step2
  [Étape 3 : connexion du projet à Azure]: #step3
  [Étape 4 : exécution des modifications et déclenchement d'une régénération et d'un redéploiement]: #step4
  [Étape 5 : redéploiement d'une build antérieure (facultatif)]: #step5
  [Étape 6 : modification du déploiement de production (services cloud uniquement)]: #step6
  [Étape 7 : exécution de tests unitaires (facultatif)]: #step7
  [1]: http://go.microsoft.com/fwlink/?LinkId=512980
  [la version Git de cette procédure pas à pas]: http://go.microsoft.com/fwlink/p/?LinkId=397358
  [Prise en main d'Azure et ASP.NET]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-dotnet-get-started/
  []: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
  [2]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
  [3]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
  [4]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
  [5]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
  [portail Azure]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
  [7]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
  [8]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
  [9]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
  [10]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
  [11]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
  [12]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
  [13]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
  [14]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
  [15]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
  [16]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
  [17]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
  [18]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
  [19]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
  [20]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
  [21]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
  [22]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
  [23]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
  [24]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
  [25]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
  [26]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
  [27]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
  [28]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
  [29]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
  [30]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
  [31]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
  [32]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
  [33]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
  [34]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
  [35]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
  [36]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
  [37]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
  [38]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
  [39]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
  [40]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
  [41]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
  [42]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
  [43]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
  [44]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
  [Exécuter des tests unitaires dans votre build]: http://go.microsoft.com/fwlink/p/?LinkId=510474
  [Visual Studio Online]: http://go.microsoft.com/fwlink/?LinkId=253861
  [Partagez votre code dans Git]: http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx
  [Publication à partir du contrôle de code source sur des sites Web Azure]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-publish-source-control
