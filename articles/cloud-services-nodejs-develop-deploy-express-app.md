<properties  linkid="dev-nodejs-basic-web-app-with-express" urlDisplayName="Web App with Express" pageTitle="Web App with Express (Node.js) - Azure Tutorial" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="An tutorial that builds on the cloud service tutorial, and demonstrates how to use the Express module." metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Build a Node.js web application using Express on an Azure Cloud Service" authors="" solutions="" manager="" editor="" />

# Création d'une application Web Node.js avec Express sur un service cloud Azure

Node.js inclut un ensemble minimal de fonctionnalités dans le runtime principal. Les développeurs utilisent souvent des modules tiers pour fournir des fonctionnalités supplémentaires lors du développement d'une application Node.js. Dans ce didacticiel, vous allez créer une application en utilisant le module [Express][1], qui fournit une infrastructure MVC pour créer des applications Web Node.js.

Voici une capture d'écran de l'application terminée :

![Navigateur Web affichant Bienvenue sur Express dans
Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## Création d'un projet de service cloud

Procédez comme suit pour créer un projet de service cloud nommé << expressapp >> :

1.  À partir du menu **Démarrer** ou de l'**écran d'accueil**, recherchez **Azure PowerShell**. Enfin, cliquez avec le bouton droit sur **Azure PowerShell** et sélectionnez **Exécuter en tant qu'administrateur**.
    
    ![Icône Azure
    PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
    
    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2.  Remplacez les répertoires du répertoire **c:\node**, puis entrez les commandes suivantes pour créer une solution nommée **expressapp** et un rôle Web nommé **WebRole1** :
    
         PS C:\node> New-AzureServiceProject expressapp
         PS C:\Node> Add-AzureNodeWebRole

## Installation d'Express

1.  Installez le module Express en exécutant la commande suivante :
    
         PS C:\node\expressapp> npm install express -g
    
    Le résultat de la commande npm doit ressembler à l'exemple ci-dessous :
    
    ![Windows PowerShell affichant le résultat de la commande npm
    install
    express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2.  Remplacez les répertoires du répertoire **WebRole1** et utilisez la commande express pour créer une application :
    
         PS C:\node\expressapp\WebRole1> express
    
    Vous êtes invité à remplacer votre application précédente. Tapez **y** ou **yes** pour continuer. Express génère le fichier app.js et une structure de dossiers pour la création de votre application.
    
    ![Résultat de la commande
    express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)

3.  Supprimez le fichier **server.js**, puis remplacez le nom du fichier **app.js** généré par **server.js**.
    
        PS C:\node\expressapp\WebRole1> del server.js
        PS C:\node\expressapp\WebRole1> ren app.js server.js

4.  Pour installer les dépendances supplémentaires définies dans le fichier package.json, entrez la commande suivante :
    
        PS C:\node\expressapp\WebRole1> npm install
    
    ![Résultat de la commande npm
    install](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

5.  Utilisez la commande suivante pour exécuter l'application dans l'émulateur Windows Azure :
    
        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
    
    ![Page Web contenant Bienvenue sur
    Express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## Modification de la vue

À présent, modifiez la vue pour afficher le message << Bienvenue sur Express dans Azure >>.

1.  Entrez la commande suivante pour ouvrir le fichier index.jade :
    
        PS C:\node\expressapp\WebRole1> notepad views/index.jade
    
    ![Contenu du fichier
    index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
    
    Jade est le moteur de vue par défaut des applications Express. Pour plus d'informations sur le moteur de vue Jade, consultez
    [http://jade-lang.com][2].

2.  Modifiez la dernière ligne du texte en ajoutant **dans Azure**.
    
    ![Dernières lignes du fichier index.jade : p Bienvenue
    sur \#{title} dans
    Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Enregistrez le fichier et quittez le Bloc-notes.

4.  Actualisez votre navigateur pour afficher vos modifications.
    
    ![Navigateur Web affichant la page Bienvenue sur Express dans
    Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Après avoir testé l'application, utilisez la cmdlet **Stop-AzureEmulator** pour arrêter l'émulateur.

## Publication de l'application dans Azure

Dans la fenêtre Azure PowerShell, utilisez la cmdlet **Publish-AzureServiceProject** pour déployer l'application dans un service cloud.

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Une fois le déploiement terminé, votre navigateur s'ouvre et affiche la page Web.

![Navigateur Web affichant la page Express. L'URL indique que la page
est maintenant hébergée sur Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)



[1]: http://expressjs.com/
[2]: http://jade-lang.com