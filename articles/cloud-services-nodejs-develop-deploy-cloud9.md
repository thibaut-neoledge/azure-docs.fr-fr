<properties 
	pageTitle="Déploiement de Node.js avec Cloud9 - Didacticiel Azure" 
	description="Découvrez comment utiliser l'IDE Cloud9 pour développer, générer et déployer une application Node.js sur Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>






# Déploiement d'une application Azure App à partir de Cloud9

Ce didacticiel explique comment utiliser l'IDE Cloud9 pour développer, créer et
déployer une application Node.js dans Azure.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   créer un projet IDE Cloud9 ;
-   déployer le projet vers Azure ;
-   mettre à jour un déploiement Azure existant ;
-   déplacer des projets entre l'environnement intermédiaire et l'environnement de production.

L'[IDE Cloud9] est un environnement de développement interplateforme accessible depuis
un navigateur. L'une des fonctionnalités de Cloud9 pour les projets Node.js
vous permet de déployer directement vers Azure à partir de l'IDE.
Cloud9 s'intègre également aux services de référentiel GitHub et BitBucket,
vous permettant ainsi de partager facilement votre projet avec d'autres utilisateurs.

Avec Cloud9, vous pouvez développer et déployer une application dans Azure
depuis de nombreux navigateurs et systèmes d'exploitation modernes, sans avoir à
installer les outils de développement supplémentaires ou des kits de développement logiciel localement. Les étapes ci-dessous
ont été effectuées à l'aide de Google Chrome sur un ordinateur Mac.

## Inscription

Pour utiliser Cloud9, vous devez d'abord visiter le site Web et [souscrire un
abonnement][IDE Cloud9]. Vous pouvez vous connecter à l'aide d'un compte
GitHub ou BitBucket, ou créer un compte Cloud9. Il existe aussi
un abonnement gratuit, ainsi qu'une offre payante
qui comporte plus d'options. Pour plus d'informations, consultez la page [IDE Cloud9][].

## Création d'un projet Node.js

1.  Connectez-vous à Cloud9, cliquez sur le **+** en regard de **My Projects**,
    puis sélectionnez **Create a new project**.

	![create new Cloud9 project](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_create_project.png)

2.  Dans la boîte de dialogue **Create a new project**, entrez un nom de projet,
    l'accès et le type de projet. Cliquez sur **Create** pour créer le projet.

	![create new project dialog Cloud9](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_project.png)

	> [AZURE.NOTE] Certaines options nécessitent un abonnement payant à Cloud9.
	   
	> [AZURE.NOTE] Le nom de votre projet Cloud9 n'est pas celui utilisé lors du déploiement sur Azure.

3.  Une fois le projet créé, cliquez sur **Start Editing**. Si c'est la première fois que vous utilisez l'IDE Cloud9, vous êtes invité à une visite guidée du service. Si vous ne voulez pas suivre la visite guidée et y revenir plus tard, sélectionnez **Just the editor,please**.

	![start editing the Cloud9 project](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_startediting.png)

4.  Pour créer une application Node, sélectionnez **File**, puis **New
    File**.

	![create new file in the Cloud9 project](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_filenew.png)

5.  Un nouvel onglet appelé **Untitled1** s'affiche. Entrez le
    code suivant dans l'onglet **Untitled1** pour créer l'application
    Node :

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure\n');
        }).listen(port);
	
	> [AZURE.NOTE] L'utilisation de process.env.PORT permet de s'assurer que l'application choisit bien le bon port, qu'elle soit exécutée dans le débogueur Cloud9 ou déployée sur Azure.

6.  Pour enregistrer le code, sélectionnez **File**, puis **Save as**. Dans la
    boîte de dialogue **Save as**, saisissez **server.js** comme nom de fichier, puis
    cliquez sur **Save**.


	> [AZURE.NOTE] Un avertissement indiquant que la variable req n'est pas utilisée risque de s'afficher. Vous pouvez ignorer cet avertissement.

	![save the server.js file](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_saveas.png)

## Exécuter l'Application

> [AZURE.NOTE] Les étapes présentées dans cette section sont suffisantes dans le cadre d'une application très simple, mais pour les applications qui utilisent des modules externes, vous devrez peut-être sélectionner une version spécifique de Node.js pour l'environnement de débogage. Pour ce faire, sélectionnez **Configure...** dans la liste déroulante de débogage, puis sélectionnez la version spécifique de Node.js. Par exemple, vous pouvez recevoir des erreurs d'authentification lors de l'utilisation du module 'azure' si vous n'avez pas sélectionné Node.js 0.6.x.


1.  Cliquez sur **Debug** pour exécuter l'application dans le débogueur Cloud9.
	
	![run in the debugger](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug.png)

2.  Une fenêtre de résultat s'affiche. Cliquez sur l'URL indiquée pour
    accéder à votre application via une fenêtre de navigateur.

	![output window](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_output.png)

	L'application finale ressemble à ce qui suit :

	![application running in browser](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug_browser.png)

3.  Pour arrêter le débogage de l'application, cliquez sur **stop**.

## Création d'un compte Azure

Pour déployer votre application, vous avez besoin d'un compte Azure. Si vous
ne possédez pas déjà un compte Azure, vous pouvez vous inscrire gratuitement pour obtenir une
version d'évaluation en suivant ces étapes :

[AZURE.INCLUDE [create-azure-account](../includes/create-azure-account.md)]


## Création d'un déploiement

1.  Pour créer un déploiement, sélectionnez **Deploy**, puis cliquez sur **+** pour créer un serveur de déploiement.

    ![create a new deployment][create a new deployment]

2.  Dans la boîte de dialogue **Add a deploy target**, entrez le nom du déploiement, puis sélectionnez **Azure** dans la liste **Choose type**. Le nom de déploiement que vous indiquez servira à identifier le déploiement sur Cloud9. Il ne s'agit pas du nom du déploiement sur Azure.

3.  Comme c'est la première fois que vous créez un déploiement Cloud9 qui utilise Azure, vous devez configurer vos paramètres de publication Azure. Effectuez les opérations suivantes pour télécharger et installer ces paramètres sur Cloud9 :

    1.  Cliquez sur **Download Azure Settings**.

        ![download publish settings](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_choosetypeandcert.png)

        Ceci ouvre le portail de gestion Azure et vous invite à télécharger les paramètres de publication Azure. Vous devez vous connecter avec votre compte Azure avant de commencer.

    2.  Enregistrez le fichier de paramètres de publication sur votre disque local.

    3.  Dans la boîte de dialogue **Add a deploy target**, sélectionnez **Choose File**,
        puis sélectionnez le fichier téléchargé lors de l'étape précédente.

    4.  Une fois le fichier sélectionné, cliquez sur **Upload**.

4.  Cliquez sur **+ Create new** pour créer un service hébergé.  *hosted service* désigne le conteneur dans lequel votre application est hébergée quand elle est déployée sur Azure. Pour plus d'informations, consultez la page [Présentation de la création d'un service hébergé pour Azure][].

	![create a new deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createdeployment.png)

5.  Vous êtes invité à préciser le nom du nouveau service hébergé, ainsi que certaines options de configuration comme le nombre d'instances, le système d'exploitation hôte et le centre de données. Le nom de déploiement spécifié est le nom du service hébergé dans Azure. Ce nom doit être unique dans le système Azure.
	
	![create a new hosted service](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_hosted_service_settings.png)

	> [AZURE.NOTE] Dans la boîte de dialogue **Add a deploy target**, les services hébergés Azure existants sont répertoriés dans la section **Choose existing deployment**. Si vous sélectionnez un service hébergé existant, le projet sera déployé sur ce service.

	> [AZURE.NOTE] Sélectionnez **Enable RDP** et indiquez un nom d'utilisateur et un mot de passe pour activer le Bureau à distance sur votre déploiement.


## Déploiement dans un environnement de production Azure

1.  Sélectionnez le déploiement que vous avez créé dans les étapes précédentes. Une boîte de dialogue
    s'affiche et vous fournit des informations sur ce déploiement
    ainsi que l'URL de production qui sera utilisée après le déploiement de Windows
    Azure.

	![select a deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_select_deployment.png)

2.  Sélectionnez **Deploy to Production environment**.

3.  Cliquez sur **Deploy** pour commencer le déploiement.

4.  Si c'est la première fois que vous déployez ce projet sur Azure, vous obtenez une erreur **" No web.config found "**. Sélectionnez **Yes** pour créer le fichier. Cette opération ajoute un fichier 'Web.cloud.config' à votre projet.
	
	![no web.config file found message](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_no_web_config.png)

5.  Si c'est la première fois que vous déployez ce projet sur Azure, vous obtenez une erreur **" No 'csdef' file present "**. Sélectionnez **Yes** pour créer le fichier .csdef. Cette opération ajoute un fichier ServiceDefinition.csdef à votre projet.    Il s'agit d'un fichier propre à Azure, nécessaire pour la publication de votre application. Pour plus d'informations, consultez la page [Présentation de la création d'un service hébergé pour Azure][].

6.  Vous êtes invité à sélectionner la taille de l'instance pour cette application. Sélectionnez **Small**, puis cliquez sur **Create**. Pour plus d'informations sur la taille des machines virtuelles Azure, consultez la page [Configuration de la taille des machines virtuelles][].

	![specify csdef file values](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createcsdef.png)

7.  L'entrée de déploiement affiche l'état du processus de déploiement. Une fois terminé, le déploiement apparaît comme **Active**.

	![deployment status](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deployment_status.png)

	> [AZURE.NOTE] Les projets déployés via l'IDE Cloud9 portent un GUID comme nom de déploiement dans Azure.

8.  La boîte de dialogue de déploiement comprend un lien vers l'URL de production. Une fois le déploiement terminé, cliquez sur l'URL pour accéder à votre application exécutée dans Azure.

	![Azure production URL link](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_url.png)

## Mise à jour de l'application

Lorsque vous appliquez des modifications à votre application, vous pouvez utiliser Cloud9 pour déployer cette application mise à jour sur le même service hébergé Azure.

1.  Dans le fichier server.js, mettez à jour votre code afin que le message " hello azure v2 " s'affiche à l'écran. Vous pouvez remplacer le code existant par le code mis à jour ci-dessous :

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

	Chaque service hébergé Azure prend en charge deux environnements, l'environnement intermédiaire et l'environnement de production. L'environnement intermédiaire est exactement comme l'environnement de production, à la différence que vous ne pouvez accéder à l'environnement intermédiaire qu'avec une URL basée sur le GUID, générée par Azure. Vous pouvez utiliser l'environnement intermédiaire pour tester votre application. Après avoir vérifié les modifications, vous pouvez faire passer la version intermédiaire en production en appliquant un échange d'adresses IP virtuelles (VIP), comme décrit plus loin dans ce didacticiel.

3.  Une fois votre application déployée dans l'environnement intermédiaire, l'URL intermédiaire basée sur le GUID s'affiche dans le résultat de la console, comme illustré dans la capture d'écran ci-dessous. Cliquez sur l'URL pour ouvrir votre application intermédiaire dans un navigateur.

	![console output showing staging URL](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_staging_console_output.png)

## Transfert de la mise à jour en production avec un échange d'adresses IP virtuelles

Lorsqu'un service est déployé sur l'environnement de production ou intermédiaire,
une adresse IP virtuelle (VIP) est affectée au service dans
cet environnement. Lorsque vous souhaitez déplacer un service depuis l'environnement intermédiaire
vers l'environnement de production, vous pouvez le faire
sans redéploiement en effectuant un échange d'adresses IP virtuelles. Cet échange permet d'intervertir l'environnement de production et l'environnement
intermédiaire. Un échange d'adresses IP virtuelles place votre application intermédiaire testée dans
l'environnement de production sans interruption de service de ce dernier. Pour plus
d'informations, consultez [Présentation de la gestion des déploiements dans Azure.][]

1.  Dans la boîte de dialogue déployer, cliquez sur le le lien **Open portal** pour ouvrir le
    portail de gestion Azure.

	![Link from deploy dialog to Azure Management Portal][Link from deploy dialog to Azure Management Portal]

2.  Connectez-vous au portail de gestion avec vos informations d'identification.

3.  Dans la partie gauche de la page Web, sélectionnez **Hosted Services, Storage
    Accounts & CDN**, puis cliquez sur **Hosted Services**.

	![Azure Management Portal][Azure Management Portal]

	Le volet Résultats affiche le service hébergé accompagné du nom spécifié dans Cloud9 et de deux déploiements. Le premier déploiement indique la valeur    d'**environnement** **Staging** et le second **Production**.

4.  Pour effectuer l'échange d'adresses IP virtuelles, sélectionnez le service hébergé, puis cliquez sur **Swap VIP** dans le ruban.

	![VIP SWAP](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_vipswap.png)

5.  Cliquez sur **OK** dans la boîte de dialogue d'échange d'adresses IP virtuelles qui s'affiche.

6.  Accédez à votre application de production. La version de l'application précédemment déployée dans l'environnement intermédiaire est maintenant en production.

	![Production application running on Azure](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_on_azure.png)

## Utilisation du Bureau à distance

Si vous avez activé le Bureau à distance et que vous avez spécifié un nom d'utilisateur et un mot de passe lors de la création de votre déploiement, vous pouvez utiliser le Bureau à distance pour vous connecter à votre service hébergé en sélectionnant une instance particulière, puis en sélectionnant Connect dans
le ruban.

![Connect to an instance](./media/cloud-services-nodejs-develop-deploy-cloud9/connect.png)

Lorsque vous cliquez sur Connect, vous êtes invité à ouvrir un fichier RDP ou à le télécharger. Ce fichier contient les informations nécessaires à la connexion à votre session de Bureau à distance. Lorsque vous exécutez ce fichier sur Windows, vous êtes invité à indiquer le nom d'utilisateur et le mot de passe que vous avez entrés à la création du déploiement. Vous êtes ensuite connecté au Bureau de l'instance sélectionnée.
de rôle.

> [AZURE.NOTE] Le fichier RDP utilisé pour la connexion à l'instance hébergée de votre application ne fonctionne qu'avec l'application Bureau à distance de
Windows.

## Arrêt et suppression de l'application

Azure facture les instances de rôle par heures de serveur consommées. Une fois votre application déployée, elle consomme du temps de serveur, même si les instances ne sont pas exécutées et sont arrêtées. En outre,
les déploiements intermédiaire et de production consomment du temps de serveur.

Cloud9 s'attache surtout à fournir un environnement de développement et n'offre pas de méthode permettant d'arrêter ou de supprimer une application une fois qu'elle est déployée sur Azure. Pour supprimer une application hébergée sur Azure, effectuez les opérations suivantes :

1.  Dans la boîte de dialogue de déploiement, cliquez sur le lien **Open portal** pour ouvrir le portail de gestion Azure.

	![Link from deploy dialog to Azure Management Portal](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_link.png)

2.  Connectez-vous au portail de gestion avec vos informations d'identification.

3.  Dans la partie gauche de la page Web, sélectionnez **Hosted Services, Storage Accounts & CDN**, puis cliquez sur **Hosted Services**.

4.  Sélectionnez le déploiement intermédiaire (indiqué par la valeur **Environment**). Cliquez sur **Delete** dans le ruban pour supprimer l'application.

	![delete the deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deletedeployment.png)

5.  Sélectionnez le déploiement de production, puis cliquez sur **Delete** pour supprimer également cette application.

## Ressources supplémentaires

-   [Documentation Cloud9][]


  [IDE Cloud9]: http://cloud9ide.com/ 
  [Présentation de la création d'un service hébergé pour Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
  [Configuration de la taille des machines virtuelles]: http://msdn.microsoft.com/library/windowsazure/ee814754.aspx
  [Présentation de la gestion des déploiements dans Azure.]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
  [Documentation Cloud9]: http://go.microsoft.com/fwlink/?LinkId=241421&clcid=0x409

<!--HONumber=45--> 
