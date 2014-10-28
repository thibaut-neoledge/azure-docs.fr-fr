<properties linkid="dev-nodejs-cloud9" urlDisplayName="Deploying with Cloud9" pageTitle="Node.js deploying with Cloud9 - Azure tutorial" metaKeywords="Cloud9 IDE Azure, Azure node.js, Azure node apps" description="Learn how to use Cloud9 IDE to develop, build, and deploy a Node.js application to Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Deploying an Azure App from Cloud9" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Déploiement d'une application Azure App à partir de Cloud9

Ce didacticiel décrit l'utilisation de l'IDE Cloud9 pour développer,
générer et déployer une application Node.js sur Azure.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   créer un projet IDE Cloud9 ;
-   déployer le projet dans Azure ;
-   mettre à jour un déploiement Azure existant ;
-   déplacer des projets entre l'environnement intermédiaire et l'environnement de production.

L'[IDE Cloud9][IDE Cloud9] est un environnement de développement interplateforme
accessible depuis un navigateur. Une des fonctions de Cloud9 pour les projets Node.js est que
vous pouvez directement effectuer le déploiement sur
Azure depuis l'IDE.Cloud9 assure également l'intégration aux services GitHub et
BitBucket, le partage de vos projets est donc simple.

Avec Cloud9, vous pouvez développer une application et la déployer
sur Azure à partir de nombreux navigateurs et systèmes d'exploitation
sans avoir à installer d'outils de développement ou de Kits de développement logiciel (SDK) supplémentaires en local. Les étapes qui
suivent sont effectuées dans Google Chrome sur un Mac.

## Inscription

Pour utiliser Cloud9, vous devez d'abord visiter le site web et [prendre
un abonnement][IDE Cloud9]. Vous pouvez vous connecter avec un
compte GitHub ou BitBucket, ou bien créer un compte Cloud9. Il existe
aussi un abonnement gratuit, ainsi qu'une offre payante qui
comporte plus d'options. Pour plus d'informations, consultez le site web [IDE Cloud9][IDE Cloud9].

## Création d'un projet Node.js

1.  Connectez-vous à Cloud9, cliquez sur le symbole **+** à côté de
    **My Projects**, puis sélectionnez **Create a new project**.

    ![créer un projet Cloud9][créer un projet Cloud9]

2.  Dans la boîte de dialogue **Create a new project**, entrez le
    nom du projet, son accès et son type. Cliquez sur **Create** pour créer le projet.

    ![Boîte de dialogue de création de projet Cloud9][Boîte de dialogue de création de projet Cloud9]

    <div class="dev-callout">
<strong>Remarque</strong>
<p>Certaines options n&eacute;cessitent un abonnement payant &agrave; Cloud9.</p>
</div>

    <div class="dev-callout">
<strong>Remarque</strong>
<p>Le nom de votre projet Cloud9 n'est pas celui utilis&eacute; lors du d&eacute;ploiement sur Azure.</p>
</div>

3.  Une fois le projet créé, cliquez sur **Start Editing**. Si c'est la première fois que vous utilisez l'IDE Cloud9, vous êtes invité à une visite guidée du service. Si vous ne voulez pas suivre la visite guidée et y revenir plus tard, sélectionnez **Just the editor, please**.

    ![modifier le projet Cloud9][modifier le projet Cloud9]

4.  Pour créer une application Node, sélectionnez **File**, puis **New
    File**.

    ![créer un fichier dans le projet Cloud9][créer un fichier dans le projet Cloud9]

5.  Un nouvel onglet **Untitled1** s'affiche. Entrez le
    code suivant sous l'onglet **Untitled1** pour créer
    l'application Node :

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure\n');
        }).listen(port);

    <div class="dev-callout">
<strong>Remarque</strong>
<p>L'utilisation de process.env.PORT permet de s'assurer que l'application choisit bien le bon port, qu'elle soit ex&eacute;cut&eacute;e dans le d&eacute;bogueur Cloud9 ou d&eacute;ploy&eacute;e sur Azure.</p>
</div>

6.  Pour enregistrer le code, sélectionnez **File**, puis **Save as**. Dans
    la boîte de dialogue **Save As**, entrez le nom de fichier **server.js**,
    puis cliquez sur **Save**.

    <div class="dev-callout">
<strong>Remarque</strong>
<p>Un avertissement indiquant que la variable req n'est pas utilis&eacute;e risque de s'afficher. Vous pouvez ignorer cet avertissement.</p>
</div>

    ![enregistrer le fichier server.js][enregistrer le fichier server.js]

## Exécution de l'application

<div class="dev-callout">
<strong>Remarque</strong>
<p>Les &eacute;tapes pr&eacute;sent&eacute;es dans cette section sont suffisantes dans le cadre d'une application tr&egrave;s simple, mais pour les applications qui utilisent des modules externes, vous devrez peut-&ecirc;tre s&eacute;lectionner une version sp&eacute;cifique de Node.js pour l'environnement de d&eacute;bogage. Pour cela, s&eacute;lectionnez <strong>Configure...</strong> dans la liste d&eacute;roulante de d&eacute;bogage, puis la version sp&eacute;cifique de Node.js. Par exemple, il est possible que vous obteniez des erreurs d'authentification lorsque vous utilisez le module &laquo;&nbsp;azure&nbsp;&raquo;, si Node.js 0.6.x n'est pas s&eacute;lectionn&eacute;.</p>
</div>

1.  Cliquez sur **Debug** pour exécuter l'application dans le débogueur Cloud9.

    ![exécuter dans le débogueur][exécuter dans le débogueur]

2.  Une fenêtre de résultat s'affiche. Cliquez sur l'URL pour
    accéder à votre application dans une fenêtre du navigateur.

    ![fenêtre de résultat][fenêtre de résultat]

    L'application finale ressemble à ce qui suit :

    ![application exécutée dans le navigateur][application exécutée dans le navigateur]

3.  Pour arrêter le débogage de l'application, cliquez sur **stop**.

## Création d'un compte Azure

Pour déployer votre application, vous avez besoin d'un compte Azure. Si vous
ne disposez pas déjà d'un compte Azure, vous pouvez obtenir
une évaluation gratuite. Pour cela, procédez comme suit :

[WACOM.INCLUDE [create-azure-account](../includes/create-azure-account.md)]

## Création d'un déploiement

1.  Pour créer un déploiement, sélectionnez **Deploy**, puis cliquez sur **+** pour créer un serveur de déploiement.

    [création d'un déploiement][création d'un déploiement]

2.  Dans la boîte de dialogue **Add a deploy target**, entrez le nom du déploiement, puis sélectionnez **Azure** dans la liste **Choose type**. Le nom de déploiement que vous indiquez servira à identifier le déploiement sur Cloud9. Il ne s'agit pas du nom du déploiement sur Azure.

3.  Comme c'est la première fois que vous créez un déploiement Cloud9 qui utilise Azure, vous devez configurer vos paramètres de publication Azure. Effectuez les opérations suivantes pour télécharger et installer ces paramètres sur Cloud9 :

    1.  Cliquez sur **Download Azure Settings**.

        ![télécharger les paramètres de publication][télécharger les paramètres de publication]

        Ceci ouvre le portail de gestion Azure et vous invite à télécharger les paramètres de publication Azure. Vous devez vous connecter avec votre compte Azure avant de commencer.

    2.  Enregistrez le fichier de paramètres de publication sur votre disque local.

    3.  Dans la boîte de dialogue **Add a deploy target**, sélectionnez
        **Choose File**, puis le fichier téléchargé à l'étape précédente.

    4.  Une fois le fichier sélectionné, cliquez sur **Upload**.

4.  Cliquez sur **+ Create new** pour créer un service hébergé. Le *service hébergé* est le conteneur dans lequel votre application est hébergée lorsqu'elle est déployée sur Azure. Pour plus d'informations, consultez la page [Présentation de la création d'un service hébergé pour Azure][Présentation de la création d'un service hébergé pour Azure].

    ![créer un déploiement][créer un déploiement]

5.  Vous êtes invité à préciser le nom du nouveau service hébergé, ainsi que certaines options de configuration comme le nombre d'instances, le système d'exploitation hôte et le centre de données. Le nom de déploiement spécifié est le nom du service hébergé dans Azure. Ce nom doit être unique dans le système Azure.

    ![créer un service hébergé][créer un service hébergé]

    <div class="dev-callout">
<strong>Remarque</strong>
<p>Dans la bo&icirc;te de dialogue <strong>Add a deploy target</strong>, les services h&eacute;berg&eacute;s Azure existants sont r&eacute;pertori&eacute;s dans la section <strong>Choose existing deployment</strong>. Si vous s&eacute;lectionnez un service h&eacute;berg&eacute; existant, le projet sera d&eacute;ploy&eacute; sur ce service.</p>
</div>

    <div class="dev-callout">
<strong>Remarque</strong>
<p>S&eacute;lectionnez <strong>Enable RDP</strong> et indiquez un nom d'utilisateur et un mot de passe pour activer le Bureau &agrave; distance sur votre d&eacute;ploiement.</p>
</div>

## Déploiement dans un environnement de production Azure

1.  Sélectionnez le déploiement que vous avez créé dans les étapes précédentes. Une boîte
    de dialogue s'affiche, avec des informations sur ce déploiement,
    ainsi que l'URL de production qui sera utilisée après le déploiement
    sur Azure.

    ![choix du déploiement][choix du déploiement]

2.  Sélectionnez **Déployer vers l'environnement de production**.

3.  Cliquez sur **Deploy** pour commencer le déploiement.

4.  Si c'est la première fois que vous déployez ce projet sur Azure, vous obtenez une erreur **No web.config found**. Sélectionnez **Oui** pour créer le fichier. Cette opération ajoute un fichier Web.cloud.config à votre projet.

    ![message no web.config file found][message no web.config file found]

5.  Si c'est la première fois que vous déployez ce projet sur Azure, vous obtenez une erreur **No 'csdef' file present**. Sélectionnez **Oui** pour créer le fichier .csdef. Cette opération ajoute un fichier ServiceDefinition.csdef à votre projet. Il s'agit d'un fichier propre à Azure, nécessaire pour la publication de votre application. Pour plus d'informations, consultez la page [Présentation de la création d'un service hébergé pour Azure][Présentation de la création d'un service hébergé pour Azure].

6.  Vous êtes invité à sélectionner la taille de l'instance pour cette application. Sélectionnez **Small**, puis cliquez sur **Create**. Pour plus d'informations sur la taille des machines virtuelles Azure, consultez la page [Configuration de la taille des machines virtuelles][Configuration de la taille des machines virtuelles].

    ![Valeurs du fichier csdef][Valeurs du fichier csdef]

7.  L'entrée de déploiement affiche l'état du processus de déploiement. Une fois terminé, le déploiement apparaît comme **Active**.

    ![état du déploiement][état du déploiement]

    <div class="dev-callout">
<strong>Remarque</strong>
<p>Les projets d&eacute;ploy&eacute;s via l'IDE Cloud9 portent un GUID comme nom de d&eacute;ploiement dans Azure.</p>
</div>

8.  La boîte de dialogue de déploiement comprend un lien vers l'URL de production. Une fois le déploiement terminé, cliquez sur l'URL pour accéder à votre application exécutée dans Azure.

    ![URL de production Azure][URL de production Azure]

## Mise à jour de l'application

Lorsque vous appliquez des modifications à votre application, vous pouvez utiliser Cloud9 pour déployer cette application mise à jour sur le même service hébergé Azure.

1.  Dans le fichier server.js, mettez à jour votre code afin que le message « hello azure v2 » s'affiche à l'écran. Vous pouvez remplacer le code existant par le code mis à jour ci-dessous :

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure v2\n');
        }).listen(port);

2.  Pour enregistrer le code, sélectionnez **File**, puis **Save**.

## Déploiement de la mise à jour dans un environnement intermédiaire Azure

1.  Sélectionnez **Deploy to Staging**.

2.  Cliquez sur **Deploy** pour commencer le déploiement.

    Chaque service hébergé Azure prend en charge deux environnements, l'environnement intermédiaire et l'environnement de production. L'environnement intermédiaire est exactement comme l'environnement de production, à la différence que vous ne pouvez accéder à l'environnement intermédiaire qu'avec une URL basée sur le GUID, générée par Azure. Vous pouvez utiliser l'environnement intermédiaire pour tester votre application. Après avoir vérifié les modifications, vous pouvez faire passer la version intermédiaire en production en appliquant un échange d'adresses IP virtuelles (VIP), comme décrit plus loin dans ce didacticiel.

3.  Une fois votre application déployée dans l'environnement intermédiaire, l'URL intermédiaire basée sur le GUID s'affiche dans le résultat de la console, comme illustré dans la capture d'écran ci-dessous. Cliquez sur l'URL pour ouvrir votre application intermédiaire dans un navigateur.

    ![résultat de la console avec l'URL intermédiaire][résultat de la console avec l'URL intermédiaire]

## Transfert de la mise à jour en production avec un échange d'adresses IP virtuelles

Lorsqu'un service est déployé dans l'environnement intermédiaire
ou de production, une adresse IP virtuelle lui est attribuée dans
cet environnement. Lorsque vous souhaitez transférer un service de
l'environnement intermédiaire vers l'environnement de production, vous
pouvez le faire sans redéploiement, en appliquant un échange d'adresses IP
virtuelles, ce qui intervertit les deux déploiements. L'échange d'adresses IP virtuelles met l'application
intermédiaire testée en production, sans interruption dans l'environnement de production. Pour plus
d'informations, consultez la page [Présentation de la gestion des déploiements dans Azure][Présentation de la gestion des déploiements dans Azure].

1.  Dans la boîte de dialogue de déploiement, cliquez sur le lien **Open
    portal** pour ouvrir le portail de gestion Azure.

    [Lien de la boîte de dialogue de déploiement vers le portail de gestion Azure][Lien de la boîte de dialogue de déploiement vers le portail de gestion Azure]

2.  Connectez-vous au portail de gestion avec vos informations d'identification.

3.  Dans la partie gauche de la page web, sélectionnez **Hosted Services,
    Storage Accounts & CDN**, puis cliquez sur **Hosted Services**.

    ![Portail de gestion Azure][Portail de gestion Azure]

    Le volet de résultats présente le service hébergé avec le nom spécifié dans Cloud9 et deux déploiements avec deux valeurs **Environment** différentes, l'un avec **Staging**, l'autre avec **Production**.

4.  Pour effectuer l'échange d'adresses IP virtuelles, sélectionnez le service hébergé, puis cliquez sur **Échanger les adresses IP virtuelles**.

    ![Échange d'adresses IP virtuelles][Échange d'adresses IP virtuelles]

5.  Cliquez sur **OK** dans la boîte de dialogue d'échange d'adresses IP virtuelles qui s'affiche.

6.  Accédez à votre application de production. La version de l'application précédemment déployée dans l'environnement intermédiaire est maintenant en production.

    ![Application de production exécutée dans Azure][Application de production exécutée dans Azure]

## Utilisation du Bureau à distance

Si vous avez activé le Bureau à distance et que vous avez spécifié un nom d'utilisateur et un mot de passe lors de la création de votre déploiement, vous pouvez utiliser le Bureau à distance pour vous connecter à votre
service hébergé en sélectionnant une instance particulière, puis en sélectionnant Connect dans le ruban.

![Connexion à une instance][Connexion à une instance]

Lorsque vous cliquez sur Connect, vous êtes invité à ouvrir un fichier RDP ou à le télécharger. Ce fichier contient les informations nécessaires à la connexion à votre session de Bureau à distance. Lorsque vous exécutez ce fichier sur Windows, vous êtes invité à indiquer le nom d'utilisateur et le mot de passe que vous avez entrés à la création du déploiement. Vous êtes ensuite
connecté au Bureau de l'instance sélectionnée.

<div class="dev-callout">
<strong>Remarque</strong>
<p>Le fichier RDP utilis&eacute; pour la connexion &agrave; l'instance h&eacute;berg&eacute;e de votre application ne fonctionne qu'avec l'application Bureau &agrave; distance de Windows.</p>
</div>

## Arrêt et suppression de l'application

Azure facture les instances de rôle par heures de serveur consommées. Une fois votre application déployée, elle consomme du temps de serveur, même si les instances ne sont pas exécutées et sont arrêtées. De plus, les
déploiements intermédiaire et de production consomment du temps de serveur.

Cloud9 s'attache surtout à fournir un environnement de développement et n'offre pas de méthode permettant d'arrêter ou de supprimer une application une fois qu'elle est déployée sur Azure. Pour supprimer une application hébergée sur Azure, effectuez les opérations suivantes :

1.  Dans la boîte de dialogue de déploiement, cliquez sur le lien **Open portal** pour ouvrir le portail de gestion Azure.

    ![Lien de la boîte de dialogue de déploiement vers le portail de gestion Azure][Lien de la boîte de dialogue de déploiement vers le portail de gestion Azure]

2.  Connectez-vous au portail de gestion avec vos informations d'identification.

3.  Dans la partie gauche de la page web, sélectionnez **Hosted Services, Storage Accounts & CDN**, puis cliquez sur **Hosted Services**.

4.  Sélectionnez le déploiement intermédiaire (indiqué par la valeur **Environment**). Cliquez sur **Supprimer** dans le ruban pour supprimer l'application.

    ![supprimer le déploiement][supprimer le déploiement]

5.  Sélectionnez le déploiement de production, puis cliquez sur **Supprimer** pour supprimer aussi cette application.

## Ressources supplémentaires

-   [Documentation Cloud9][Documentation Cloud9]

  [IDE Cloud9]: http://cloud9ide.com/
  [créer un projet Cloud9]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_create_project.png
  [Boîte de dialogue de création de projet Cloud9]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_project.png
  [modifier le projet Cloud9]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_startediting.png
  [créer un fichier dans le projet Cloud9]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_filenew.png
  [enregistrer le fichier server.js]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_saveas.png
  [exécuter dans le débogueur]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug.png
  [fenêtre de résultat]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_output.png
  [application exécutée dans le navigateur]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug_browser.png
  [create-azure-account]: ../includes/create-azure-account.md
  [télécharger les paramètres de publication]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_choosetypeandcert.png
  [Présentation de la création d'un service hébergé pour Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj155995.aspx
  [créer un déploiement]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createdeployment.png
  [créer un service hébergé]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_hosted_service_settings.png
  [choix du déploiement]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_select_deployment.png
  [message no web.config file found]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_no_web_config.png
  [Configuration de la taille des machines virtuelles]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee814754.aspx
  [Valeurs du fichier csdef]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createcsdef.png
  [état du déploiement]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deployment_status.png
  [URL de production Azure]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_url.png
  [résultat de la console avec l'URL intermédiaire]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_staging_console_output.png
  [Présentation de la gestion des déploiements dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433027.aspx
  [Échange d'adresses IP virtuelles]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_vipswap.png
  [Application de production exécutée dans Azure]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_on_azure.png
  [Connexion à une instance]: ./media/cloud-services-nodejs-develop-deploy-cloud9/connect.png
  [Lien de la boîte de dialogue de déploiement vers le portail de gestion Azure]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_link.png
  [supprimer le déploiement]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deletedeployment.png
  [Documentation Cloud9]: http://go.microsoft.com/fwlink/?LinkId=241421&clcid=0x409
