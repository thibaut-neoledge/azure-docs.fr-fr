<properties
	pageTitle="Déploiement Git local vers Azure App Service"
	description="Découvrez comment activer le déploiement Git local vers Azure App Service"
	services="app-service"
	documentationCenter=""
	authors="dariagrigoriu"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/13/2016"
	ms.author="dariagrigoriu"/>
    
# Déploiement Git local vers Azure App Service

Ce didacticiel vous montre comment déployer votre application vers [Azure App Service] depuis un référentiel Git sur votre ordinateur local. App Service prend en charge cette approche avec l'option de déploiement **Git local** dans le [portail Azure]. La plupart des commandes Git décrites dans cet article sont exécutées automatiquement pendant la création d'une application App Service avec l'[interface de ligne de commande Azure] comme décrit [ici](app-service-web-get-started.md).

## Configuration requise

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- Git. Vous pouvez télécharger le fichier binaire d’installation [ici](http://www.git-scm.com/downloads).
- Connaissances élémentaires de Git.
- Un compte Microsoft Azure Si vous ne possédez pas de compte, vous pouvez [vous inscrire à une version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial) ou [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details).

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## <a id="Step1"></a>Étape 1 : création d'un référentiel local

Effectuez les tâches suivantes pour créer un nouveau référentiel Git.

1. Lancez un outil de ligne de commande, tel que **GitBash** (Windows) ou **Bash** (Unix Shell). Sur les systèmes OS X, la ligne de commande est accessible depuis l'application **Terminal**.

2. Accédez au répertoire où figure le contenu à déployer.

3. Utilisez la commande suivante pour initialiser un nouveau référentiel Git :

		git init

## <a id="Step2"></a>Étape 2 : valider votre contenu

App Service prend en charge des applications créées dans différents langages de programmation.

1. Si votre référentiel inclut déjà du contenu, ignorez ce point et passez au point 2 ci-dessous. Si votre référentiel n'inclut pas encore de contenu, remplissez-le simplement avec un fichier .html statique comme suit :

    - À l'aide d'un éditeur de texte, créez un fichier nommé **index.html** à la racine du référentiel Git
    - Ajoutez le texte suivant dans le fichier index.html et enregistrez ce dernier : *Hello Git!*
        
2. Dans la ligne de commande, vérifiez que vous êtes bien à la racine de votre référentiel Git. Puis utilisez la commande suivante pour ajouter des fichiers à votre référentiel :

		git add -A 

4. Ensuite, validez les modifications apportées au référentiel au moyen de la commande suivante :

		git commit -m "Hello Azure App Service"

## <a id="Step3"></a>Étape 3 : activer le référentiel de l'application App Service

Pour activer un référentiel Git pour votre application App Service, procédez comme suit.

1. Connectez-vous au [portail Azure].

2. Dans le panneau de votre application App Service, cliquez sur **Paramètres > Source du déploiement**. Cliquez successivement sur **Choisir une source**, **Référentiel Git local** et **OK**.

	![Référentiel Git local](./media/app-service-deploy-local-git/local_git_selection.png)

3. Si vous configurez un référentiel pour la première fois dans Azure, vous devez créer des informations d’identification de connexion pour ce référentiel. Vous les utiliserez pour vous connecter au référentiel Azure et pour envoyer les modifications depuis votre référentiel Git local. Dans le panneau de votre application, cliquez sur **Paramètres > Informations d’identification du déploiement**, puis configurez le nom d’utilisateur et le mot de passe de votre déploiement. Quand vous avez terminé, cliquez sur **Enregistrer**.

	![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a id="Step4"></a>Étape 4 : déploiement de votre projet

Pour publier votre application vers App Service à l’aide de Git local, procédez comme suit :

1. Dans le portail Azure, dans le panneau de votre application, cliquez sur **Paramètres > Propriétés** pour l'**URL Git**.

	![](./media/app-service-deploy-local-git/git_url.png)

	**URL Git** est la référence hors programme vers laquelle vous effectuez le déploiement à partir de votre référentiel local. Vous utiliserez cette URL dans la procédure suivante.

2. À l'aide de la ligne de commande, vérifiez que vous êtes bien à la racine de votre référentiel Git local.

3. Utilisez `git remote` pour ajouter la référence hors programme répertoriée dans **URL Git** à l’étape 1. Votre commande ressemble à ce qui suit :

		git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git         
    > [AZURE.NOTE] La commande **remote** ajoute une référence nommée dans un référentiel distant. Dans cet exemple, une référence nommée « azure » est créée pour le référentiel de votre application web.

4. Diffusez votre contenu vers App Service à l'aide de la nouvelle référence **azure** distante que vous venez de créer.

		git push azure master

	Vous êtes invité à indiquer le mot de passe créé précédemment lorsque vous réinitialisez vos informations d'identification de déploiement dans le portail Azure. Entrez le mot de passe (notez que Gitbash ne génère pas d'astérisques dans la console lorsque vous tapez votre mot de passe).
       
5. Revenez à votre application dans le portail Azure. Une entrée de journal de votre dernière diffusion push doit apparaître dans le panneau **Déploiements**.

	![](./media/app-service-deploy-local-git/deployment_history.png)

6. Cliquez sur le bouton **Parcourir** en haut du panneau de l'application pour vérifier que le contenu a été déployé.
    
## <a id="Step5"></a>Résolution des problèmes

Voici les erreurs ou les problèmes rencontrés couramment lors de l’utilisation de Git pour publier vers une application App Service dans Azure :

****

**Symptôme** : Impossible d'accéder à '[URL\_du\_site]' : Impossible de se connecter à [Adresse\_scm]

**Cause** : Cette erreur peut se produire si l'application n'est pas opérationnelle.

**Résolution** : démarrez l’application dans le portail Azure. Le déploiement Git ne fonctionne pas tant que l’application n’est pas en cours d’exécution.


****

**Symptôme** : Impossible de résoudre l'hôte « nom\_hôte »

**Cause** : Cette erreur peut se produire si les informations d'adresse entrées au moment de la création du référentiel distant « azure » sont incorrectes.

**Résolution** : Utilisez la commande `git remote -v` pour répertorier tous les référentiels distants avec l'URL associée. Vérifiez que l'URL du référentiel distant « azure » est correcte. Si nécessaire, supprimez et recréez ce référentiel distant au moyen de l’URL correcte.

****

**Symptôme** : aucune référence en commun, ni spécifiée ; aucune action effectuée. Perhaps you should specify a branch such as 'master'.

**Cause** : Cette erreur peut se produire si vous ne spécifiez pas de branche au moment de l'exécution d'une opération git push et que vous n'avez pas défini la valeur push.default utilisée par Git.

**Résolution** : Exécutez de nouveau l'opération Push, en spécifiant la branche principale. Par exemple :

	git push azure master

****

**Symptôme** : src refspec [nom\_branche] ne correspond à aucun élément.

**Cause** : Cette erreur peut se produire si vous tentez d'effectuer une opération Push sur une autre branche que la branche principale sur le référentiel distant « azure ».

**Résolution** : Exécutez de nouveau l'opération Push, en spécifiant la branche principale. Par exemple :

	git push azure master

****

**Symptôme** : erreur : des modifications ont été validées dans le référentiel distant, mais votre application web n’a pas été mise à jour.

**Cause** : Cette erreur peut se produire si vous déployez une application Node.js contenant un fichier package.json spécifiant des modules obligatoires supplémentaires.

**Résolution** : Des messages supplémentaires contenant « npm ERR! » doivent être consignés avant cette erreur et peuvent fournir davantage de contexte sur la défaillance. Voici les causes connues de cette erreur et le message « npm ERR! » correspondant :

* **Fichier package.json incorrect** : npm ERR! Couldn’t read dependencies.

* **Native module that does not have a binary distribution for Windows**:

	* npm ERR! `cmd "/c" "node-gyp rebuild"` failed with 1

		OU

	* npm ERR! [nom-module@version] preinstall: `make || gmake`


## Ressources supplémentaires

* [Documentation Git](http://git-scm.com/documentation)
* [Documentation du projet Kudu](https://github.com/projectkudu/kudu/wiki)
* [Déploiement continu vers Azure App Service](app-service-continuous-deployment.md)
* [Comment utiliser PowerShell pour Azure](../powershell-install-configure.md)
* [Utilisation des outils en ligne de commande Azure](../xplat-cli-install.md)

[Azure App Service]: https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/
[Azure Developer Center]: http://azure.microsoft.com/develop/overview/
[portail Azure]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[interface de ligne de commande Azure]: https://azure.microsoft.com/fr-FR/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart

<!---HONumber=AcomDC_0803_2016-->