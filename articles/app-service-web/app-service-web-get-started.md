<properties 
	pageTitle="Prise en main des applications web dans Azure App Service." 
	description="Découvrez la facilité avec laquelle vous pouvez exécuter des applications web dans App Service et ce en déployant un exemple d’application en quelques étapes seulement. Devenez développeur en 5 minutes ; obtenez immédiatement des résultats." 
	services="app-service\web"
	documentationCenter=""
	authors="cephalin" 
	manager="wpickett" 
	editor="" 
/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article"
	ms.date="05/12/2016" 
	ms.author="cephalin"
/>
	
# Prise en main des applications web dans Azure App Service.

[AZURE.INCLUDE [onglets](../../includes/app-service-web-get-started-nav-tabs.md)]

Dans ce didacticiel, vous découvrirez comment déployer votre première application web vers [Azure App Service](../app-service/app-service-value-prop-what-is.md). App Service vous permet de créer des applications web, des [backends d’applications mobiles](/documentation/learning-paths/appservice-mobileapps/) et des [applications API Apps](../app-service-api/app-service-api-apps-why-best-platform.md).

Avec une intervention limitée de votre part, vous allez pouvoir :

- déployer un exemple d’application web (vous avez le choix entre ASP.NET, PHP, Node.js, Java ou Python) ;
- exécuter votre application en quelques secondes ;
- mettre à jour votre application web de la même façon que vous transférez des validations [Git](https://git-scm.com/docs/git-push).

Vous jetterez également un premier regard sur le [portail Azure](https://portal.azure.com) et ses différentes fonctionnalités.

## Configuration requise

- [Installez Git](http://www.git-scm.com/downloads). 
- [Installation de l’interface de ligne de commande Azure](../xplat-cli-install.md) 
- Obtenir un compte Microsoft Azure. Si vous n’avez pas de compte, vous pouvez [demander un essai gratuit](/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de votre abonnement Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Visualisez une application web en action. [Essayez App Service](http://go.microsoft.com/fwlink/?LinkId=523751) tout de suite et créez une première application temporaire. Aucune carte de crédit ni aucun engagement ne sont nécessaires.

## Déployer une application web

Déployons à présent une application web dans Azure App Service

1. Ouvrez une nouvelle invite de commandes Windows, une fenêtre PowerShell, un interpréteur de commandes Linux ou un terminal OS X. Exécutez `git --version` et `azure --version` pour vérifier que Git et l’interface de ligne de commande Azure sont installés sur votre ordinateur. 

    ![Tester l’installation des outils de l’interface de ligne de commande pour votre première application web dans Azure](./media/app-service-web-get-started/1-test-tools.png)

    Si les outils ne sont pas installés, consultez la [Configuration requise](#Prerequisites) pour accéder aux liens de téléchargement.

1. `CD` dans un répertoire de travail et clonez l’exemple d’application comme suit :

        git clone <github_sample_url>

    ![Cloner l’exemple de code d’application pour votre première application web dans Azure](./media/app-service-web-get-started/2-clone-sample.png)

    Pour *&lt;github\_sample\_url>*, utilisez l’une des URL suivantes, en fonction de l’infrastructure souhaitée :

    - HTML+CSS+JS : [https://github.com/Azure-Samples/app-service-web-html-get-started.git](https://github.com/Azure-Samples/app-service-web-html-get-started.git)
    - ASP.NET : [https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git](https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git)
    - PHP (CodeIgniter) : [https://github.com/Azure-Samples/app-service-web-php-get-started.git](https://github.com/Azure-Samples/app-service-web-php-get-started.git)
    - Node.js (Express) : [https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git](https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git) 
    - Java : [https://github.com/Azure-Samples/app-service-web-java-get-started.git](https://github.com/Azure-Samples/app-service-web-java-get-started.git)
    - Python (Django) : [https://github.com/Azure-Samples/app-service-web-python-get-started.git](https://github.com/Azure-Samples/app-service-web-python-get-started.git)

2. `CD` dans le référentiel de votre exemple d’application. Par exemple,

        cd app-service-web-html-get-started

3. Connectez-vous au portail Azure :

        azure login
    
    Appuyez-vous sur le message d’aide pour poursuivre le processus de connexion.
    
    ![Se connecter à Azure pour créer votre première application web](./media/app-service-web-get-started/3-azure-login.png)

4. Créez la ressource d’application d’App Service dans Azure avec un nom d’application unique à l’aide de la commande suivante. Lorsque vous y êtes invité, spécifiez le numéro de la région souhaitée.

        azure site create --git <app_name>
    
    ![Créer la ressource Azure pour votre première application web dans Azure](./media/app-service-web-get-started/4-create-site.png)
    
    >[AZURE.NOTE] Si vous n’avez jamais configuré les informations d’identification de déploiement pour votre abonnement Azure, vous serez invité à les créer. Ces informations d’identification (et non pas les informations d’identification de votre compte Azure) sont utilisées par App Service uniquement dans le cadre des déploiements Git et des connexions FTP.
    
    Votre application est maintenant créée dans Azure. Votre répertoire actuel a été initialisé avec Git et est connecté au nouvel App Service en tant que Git distant. Vous pouvez accéder à l’URL de l’application (http://&lt;app_name>.azurewebsites.net) pour visualiser la belle page HTML par défaut, mais essayons d’abord d’y amener votre propre code.

4. À présent, déployez votre exemple de code dans la nouvelle application App Service de la même manière que vous transfériez n’importe quel code avec Git :

        git push azure master 

    ![Pousser du code dans votre première application web dans Azure](./media/app-service-web-get-started/5-push-code.png)
    
    Si vous avez utilisé une des infrastructures de langage, vous verrez une sortie différente de celle montrée ci-dessus. Cela provient du fait que `git push` injecte non seulement du code dans Azure, mais déclenche également des tâches de déploiement dans le moteur de déploiement. Si vous disposez de fichiers package.json (Node.js) ou requirements.txt (Python) dans la racine (référentiel) du projet, ou si vous avez un fichier packages.config dans votre projet ASP.NET, les scripts de déploiement restaurent les packages dont vous avez besoin. Vous pouvez également [activer l’extension Composer](web-sites-php-mysql-deploy-use-git.md#composer) afin de traiter automatiquement les fichiers composer.json dans votre application PHP.

Félicitations, vous avez déployé votre application vers Azure App Service.

## Exécuter votre application en temps réel

Pour voir votre application en cours d’exécution dans Azure, exécutez cette commande à partir de n’importe quel répertoire de votre référentiel :

    azure site browse

## Mettre à jour votre application

Vous pouvez désormais procéder à un transfert depuis la racine (référentiel) de votre projet à tout moment à l’aide de Git. Vous pouvez ainsi effectuer des mises à jour du site web en activité. Vous pouvez utiliser la même procédure que celle que vous avez utilisée lors du déploiement de votre application dans Azure pour la première fois. Par exemple : chaque fois que vous voulez transférer la modification que vous avez testée localement, exécutez simplement les commandes suivantes à partir de la racine (référentiel) du projet :
    
    git add .
    git commit -m "<your_message>"
    git push azure master

## Voir votre application sur le portail Azure

À présent, accédons au portail Azure pour voir ce que vous avez créé :

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec le compte Microsoft associé à votre abonnement Azure.

2. Dans la barre à gauche, cliquez sur **App Services**.

3. Cliquez sur l’application que vous venez de créer pour ouvrir sa page dans le portail (nommée [panneau](../azure-portal-overview.md)). Le panneau **Paramètres** est également ouvert par défaut, pour plus de commodité.

    ![Vue du portail de votre première application web dans Azure](./media/app-service-web-get-started/portal-view.png)

Le panneau de portail de votre application App Service contient un ensemble de paramètres et d’outils que vous pouvez configurer, surveiller et sécuriser. Vous pouvez également les utiliser pour dépanner votre application. Prenez un moment pour vous familiariser avec cette interface en effectuant des tâches simples (le numéro de la tâche correspond au numéro de la capture d’écran) :

1. Arrêtez l’application.
2. Redémarrez l’application.
3. Cliquez sur le lien **Groupe de ressources** pour afficher toutes les ressources déployées dans le groupe de ressources.
4. Cliquez sur **Paramètres** > **Propriétés** pour voir d’autres informations relatives à votre application.
5. Cliquez sur **Outils** pour accéder aux outils utiles pour la surveillance et le dépannage.  

## Étapes suivantes

- Faites passer votre application Azure à la vitesse supérieure. Sécurisez-la grâce à l’authentification. Mettez-la à l’échelle en fonction de la demande. Configurez des alertes de performance. Tout ceci en seulement quelques clics. Consultez l’article [Prise en main d’Azure App Services, 2e partie](app-service-web-get-started-2.md).
- Outre Git et l’interface de ligne de commandes Azure, d’autres méthodes existent pour déployer des applications web vers Azure (voir [Déploiement de votre application dans Azure App Service](../app-service-web/web-sites-deploy.md)). Trouvez le processus de développement et de déploiement adapté à votre infrastructure de langue en sélectionnant votre infrastructure en haut de l’article.

<!---HONumber=AcomDC_0518_2016-->