<properties
	pageTitle="Développement logiciel agile avec Azure App Service"
	description="Apprenez à créer des applications complexes à grande échelle avec Azure App Service afin que le développement logiciel agile soit pris en charge."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="cephalin"/>


# Développement logiciel agile avec Azure App Service #

Dans ce didacticiel, vous allez apprendre à créer des applications complexes à grande échelle avec [Azure App Service](/services/app-service/) d’une façon qui prend en charge le [développement de logiciel agile](https://en.wikipedia.org/wiki/Agile_software_development). Cela suppose que vous savez déjà [déployer une application complexe de manière prévisible dans Microsoft Azure](app-service-deploy-complex-application-predictably.md).

Les limitations des processus techniques peuvent souvent faire obstacle à l’implémentation des méthodologies agiles. Azure App Service avec des fonctionnalités comme la [publication continue](web-sites-publish-source-control.md), les [environnements intermédiaires](web-sites-staged-publishing.md) (emplacements) et l’[analyse](web-sites-monitor.md) lorsqu’ils sont associé avec soin à l’orchestration et à la gestion du déploiement dans [Azure Resource Manager](resource-group-overview.md), peut constituer une solution idéale pour les développeurs qui intègrent le développement de logiciel agile.

Le tableau suivant comporte une courte liste de prérequis associés au développement agile et sur la façon dont les services Azure permettent de mettre en œuvre chacun d’eux.

| Prérequis | Implémentation avec Azure |
|---------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| - Génération à chaque validation<br>- Génération automatique et rapide | Lorsqu’il est configuré avec le déploiement continu, Azure App Service peut générer les ressources en direct sur une branche de développement. Chaque fois que le code est envoyé à la branche, il est généré automatiquement et exécuté en direct dans Azure.|
| - Test automatique des générations | Les tests de charge, les tests web, par exemple, peuvent être déployés avec le modèle Azure Resource Manager.|
| - Effectuer des tests dans un clone de l’environnement de production | Les modèles Azure Resource Manager permettent de créer des clones de l’environnement de production Azure (notamment les paramètres de l’application, les modèles de chaînes de connexion, la mise à l’échelle, etc.) afin d’effectuer des tests rapidement et de manière prévisible.|
| - Afficher aisément le résultat de la dernière génération | Le déploiement continu entre un référentiel et Azure signifie que vous pouvez tester le nouveau code d’une application en direct dans une application immédiatement après avoir validé vos modifications. |
| - Effectuer des validations quotidiennes dans la branche principale<br>- Automatiser le déploiement | L’intégration continue d’une application de production dans la branche principale d’un référentiel déploie automatiquement chaque validation/fusion dans la branche principale en production. |

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Procédure à suivre ##

Vous découvrirez un flux de travail de type développement-test-intermédiaire-production pour publier les modifications apportées à l’exemple d’application [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp), qui se compose de deux [applications web](/services/app-service/web/), l’une étant un serveur frontal (FE) et l’autre un serveur principal d’API Web (BE), et d’une [base de données SQL](/services/sql-database/). Vous utiliserez l’architecture de déploiement illustrée ci-dessous :

![](./media/app-service-agile-software-development/what-1-architecture.png)

Voici la situation telle qu’elle apparaît :

-	L’architecture de déploiement est divisée en trois environnements distincts (appelés [groupes de ressources](resource-group-overview.md) dans Azure), chacun disposant de son propre [plan App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md), des paramètres de [mise à l’échelle](web-sites-scale.md) et de la base de données SQL. 
-	Chaque environnement peut être géré séparément. Il peut même être couvert par des abonnements différents.
-	Les phases intermédiaire et de production sont implémentées comme deux emplacements de la même application App Service. La branche principale est configurée pour l’intégration continue avec l’emplacement intermédiaire.
-	Quand une validation pour la branche principale est vérifiée sur l’emplacement intermédiaire (avec des données de production), l’application intermédiaire vérifiée est permutée dans l’emplacement de production [sans interruption](web-sites-staged-publishing.md).

L’environnement de production et intermédiaire est défini par le modèle dans [*&lt;racine\_référentiel>*/ARMTemplates/ProdandStage.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/ProdAndStage.json).

Les environnements de développement et de test sont définis par le modèle dans [*&lt;racine\_référentiel>*/ARMTemplates/Dev.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/Dev.json).

Vous utiliserez également la stratégie de création de branchement typique, qui déplace le code entre la branche de développement et la branche de test, puis vers la branche principale (il s’agit d’un déplacement en qualité, pour ainsi dire).

![](./media/app-service-agile-software-development/what-2-branches.png)

## Éléments requis ##

-	Un compte Azure
-	Un compte [GitHub](https://github.com/)
-	Git Shell (installé avec [GitHub for Windows](https://windows.github.com/)) - cela permet d’exécuter des commandes PowerShell et Git dans la même session 
-	Dernières informations [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/0.9.4-June2015/azure-powershell.0.9.4.msi)
-	Compréhension élémentaire des concepts et outils suivants :
	-	Déploiement de modèles [Azure Resource Manager](resource-group-overview.md) (voir également [Déployer une application complexe de manière prévisible dans Microsoft Azure](app-service-deploy-complex-application-predictably.md))
	-	[Git](http://git-scm.com/documentation)
	-	[PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE]Vous avez besoin d’un compte Azure pour suivre ce didacticiel : + Vous pouvez [ouvrir un compte Azure gratuitement](/pricing/free-trial/?WT.mc_id=A261C142F) : vous obtenez alors des crédits dont vous pouvez vous servir pour essayer les services Azure payants et, une fois vos crédits épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment Web Apps. Vous pouvez [activer les avantages d’abonnement Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) : votre abonnement Visual Studio vous octroie des crédits chaque mois que vous pouvez utiliser pour des services Azure payants.
>
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Configurer votre environnement de production ##

>[AZURE.NOTE]Le script utilisé dans ce didacticiel configure automatiquement la publication continue à partir de votre référentiel GitHub. Pour ce faire, vos informations d’identification GitHub doivent déjà être stockées dans Azure, sinon les scripts de déploiement échoueront lorsque vous tenterez de configurer les paramètres de contrôle de code source pour les applications web.
>
>Pour stocker vos informations d’identification GitHub dans Azure, créez une application web dans le [portail Azure](https://portal.azure.com) et [configurez le déploiement GitHub](web-sites-publish-source-control.md#Step7). Cette opération est unique.

Dans un scénario classique d’opérations de développement, vous disposez d’une application qui s’exécute dans Azure et vous souhaitez lui apporter des modifications par le biais de la publication continue. Dans ce scénario, vous disposez d’un modèle que vous avez développé, testé et utilisé pour déployer l’environnement de production. Vous allez le configurer dans cette section.

1.	Créez votre branchement dans le référentiel [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp). Pour plus d’informations sur la création de votre branchement, voir [Branchement de référentiel](https://help.github.com/articles/fork-a-repo/). Une fois votre branchement créé, il est visible dans votre navigateur.
 
	![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.	Ouvrez une session Git Shell. Si vous n’avez pas encore Git Shell, installez [GitHub pour Windows](https://windows.github.com/).

3.	Créez un clone local de votre branchement en exécutant la commande suivante :

		git clone https://github.com/<your_fork>/ToDoApp.git 

4.	Lorsque le clone local est créé, accédez à *&lt; racine\_référentiel>*\\ARMTemplates, puis exécutez le script deploy.ps1 comme suit :

		.\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git

4.	Lorsque vous y êtes invité, tapez le nom d’utilisateur et le mot de passe souhaités pour l’accès à la base de données.

	Vous devez voir l’avancement de la configuration des différentes ressources Azure. Lorsque le déploiement est terminé, le script lance l’application dans le navigateur et émet un signal sonore convivial.

	![](./media/app-service-agile-software-development/production-2-app-in-browser.png)
 
	>[AZURE.TIP]Examinez *&lt;racine\_référentiel>*\\ARMTemplates\\Deploy.ps1 pour voir comment il génère des ressources avec des ID uniques. Vous pouvez utiliser la même approche pour créer des clones de déploiement sans vous soucier des noms de ressource en conflit.
 
6.	De retour dans votre session Git Shell, exécutez :

		.\swap –Name ToDoApp<unique_string>master

	![](./media/app-service-agile-software-development/production-4-swap.png)

7.	Lorsque le script se termine, revenez en arrière pour accéder à l’adresse du serveur frontal (http://ToDoApp*&lt;unique_string> * master.azurewebsites.net/) afin d’afficher l’application qui s’exécute en production.
 
5.	Connectez-vous au [portail Azure](https://portal.azure.com) et observez ce qui est créé.

	Les deux applications web doivent figurer dans le même groupe de ressources, et le nom de l’une d’elles doit comporter le suffixe `Api`. Si vous examinez l’affichage de groupe de ressources, vous pouvez voir également la base de données et le serveur SQL, le plan App Service et les emplacements intermédiaires pour les applications web. Parcourez les différentes ressources et comparez-les à *&lt;racine\_référentiel>*\\ARMTemplates\\ProdAndStage.json pour voir comment elles sont configurées dans le modèle.

	![](./media/app-service-agile-software-development/production-3-resource-group-view.png)

Vous venez de configurer l’environnement de production. Vous allez lancer une nouvelle mise à jour de l’application.

## Créer des branches de développement et de test ##

À présent que vous disposez d’une application complexe qui s’exécute en production dans Azure, vous allez effectuer une mise à jour dans votre application en recourant à la méthodologie agile. Dans cette section, vous allez créer les branches de développement et de test dont vous aurez besoin pour effectuer les mises à jour requises.

1.	Créez d’abord l’environnement de test. Dans votre session Git Shell, exécutez les commandes suivantes afin de créer l’environnement pour une nouvelle branche appelée **NewUpdate**. 

		git checkout -b NewUpdate
		git push origin NewUpdate 
		.\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch NewUpdate

1.	Lorsque vous y êtes invité, tapez le nom d’utilisateur et le mot de passe souhaités pour l’accès à la base de données.

	Lorsque le déploiement est terminé, le script lance l’application dans le navigateur et émet un signal sonore convivial. Vous disposez alors d’une nouvelle branche avec son propre environnement de test. Prenez un moment pour examiner plus longuement cet environnement de test :

	-	Vous pouvez le créer avec n’importe quel abonnement Azure. Cela signifie que l’environnement de production peut être géré séparément à partir de votre environnement de test.
	-	Votre environnement de test s’exécute dans Azure.
	-	Votre environnement de test est identique à l’environnement de production, à l’exception des emplacements intermédiaires et des paramètres de mise à l’échelle. Il s’agit des seules différences entre ProdandStage.json et Dev.json.
	-	Vous pouvez gérer votre environnement de test dans son propre plan App Service avec un niveau de prix différent (par exemple, **Gratuit**).
	-	La suppression de cet environnement de test est aussi simple que la suppression du groupe de ressources. Vous découvrirez [ultérieurement](#delete) comment procéder.

2.	Poursuivez avec la création d’une branche de développement en exécutant les commandes suivantes :

		git checkout -b Dev
		git push origin Dev
		.\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch Dev

3.	Lorsque vous y êtes invité, tapez le nom d’utilisateur et le mot de passe souhaités pour l’accès à la base de données.

	Prenez un moment pour examiner un certain nombre de points sur cet environnement de développement :

	-	Votre environnement de développement possède une configuration identique à l’environnement de test, car il est déployé à l’aide du même modèle.
	-	Chaque environnement de développement peut être créé avec l’abonnement Azure du développeur, ce qui permet de gérer l’environnement de test séparément.
	-	Votre environnement de développement s’exécute dans Azure.
	-	La suppression de l’environnement de développement est aussi simple que la suppression du groupe de ressources. Vous découvrirez [ultérieurement](#delete) comment procéder.

>[AZURE.NOTE]Lorsque plusieurs développeurs travaillent sur la nouvelle mise à jour, chacun d’eux peut facilement créer un environnement de branche et de développement dédié en procédant comme suit :
>
>1.	Créez leur branchement de référentiel dans GitHub (voir [Branchement de référentiel](https://help.github.com/articles/fork-a-repo/)).
>2.	Clonez le branchement sur leur ordinateur local.
>3.	Exécutez les mêmes commandes pour créer leur environnement et leur branche.

Lorsque vous avez terminé, votre branchement GitHub doit comporter trois branches :

![](./media/app-service-agile-software-development/test-1-github-view.png)

Vous devez disposer de six applications web (trois ensembles de deux applications) dans les trois groupes de ressources distincts :

![](./media/app-service-agile-software-development/test-2-all-webapps.png)
 
>[AZURE.NOTE]Notez que ProdandStage.json ordonne à l’environnement de production d’utiliser le niveau de tarification **Standard**, qui est approprié pour l’extensibilité de l’application de production.

## Générer et tester chaque validation ##

Les fichiers de modèle ProdAndStage.json et Dev.json comportent déjà les paramètres de contrôle du code source, qui par défaut configurent la publication continue de l’application web. Par conséquent, chaque validation dans la branche GitHub déclenche le déploiement automatique vers Azure à partir de cette branche. Nous allons à présent découvrir le fonctionnement de votre configuration.

1.	Assurez-vous de vous situer dans la branche Dev du référentiel local. Pour ce faire, exécutez la commande suivante dans Git Shell :

		git checkout Dev

2.	Apportez une modification simple à la couche d’interface utilisateur de l’application. Pour cela, vous allez éditer le code pour activer l’utilisation des listes [Bootstrap](http://getbootstrap.com/components/). Ouvrez *&lt;racine\_référentiel>*\\src\\MultiChannelToDo.Web\\index.cshtml et apportez les modifications mises en évidence ci-dessous :

	![](./media/app-service-agile-software-development/commit-1-changes.png)

	>[AZURE.NOTE]Si vous ne pouvez pas lire l’image ci-dessus :
	>
	>- Ligne 18, remplacez `check-list` par `list-group`.
	>- Ligne 19, remplacez `class="check-list-item"` par `class="list-group-item"`.

3.	Enregistrez la modification. De retour dans Git Shell, exécutez les commandes suivantes :

		cd <repository_root>
		git add .
		git commit -m "changed to bootstrap style"
		git push origin Dev
 
	Ces commandes git ont pour effet de « vérifier le code » comme ce serait le cas dans un autre système de contrôle de code source, tel que TFS. Lorsque vous exécutez `git push`, la nouvelle validation déclenche une transmission automatique de type push à Azure, lequel reconstruit ensuite l’application pour refléter la modification dans l’environnement de développement.

4.	Pour vérifier que cette transmission de code de type push à votre environnement de développement s’est bien produite, accédez au volet d’application web de votre environnement de développement et examinez la partie **Déploiement**. Le message de la dernière validation doit y figurer.

	![](./media/app-service-agile-software-development/commit-2-deployed.png)

5.	Cliquez alors sur **Parcourir** pour voir la nouvelle modification dans l’application en direct dans Azure.

	![](./media/app-service-agile-software-development/commit-3-webapp-in-browser.png)

	Il s’agit d’une modification relativement mineure pour l’application. Toutefois, bien souvent les modifications apportées à une application web complexe ont des effets secondaires inattendus et indésirables. Pouvoir facilement tester chaque validation dans les générations en direct vous permet d’intercepter les problèmes avant qu’ils ne s’affichent sur l’ordinateur de vos clients.

À ce stade, vous devez avoir conscience qu’en tant que développeur du projet **NewUpdate**, vous pourrez créer aisément un environnement de développement pour vous-même, puis générer chaque validation et tester chaque génération.

## Fusionner le code dans l’environnement de test ##

Lorsque vous êtes prêt à envoyer votre code à la branche NewUpdate à partir de la branche Dev, le processus git standard se déroule ainsi :

1.	Fusionnez les nouvelles validations de NewUpdate dans la branche Dev de GitHub, comme les validations créées par d’autres développeurs. Toute nouvelle validation dans GitHub déclenchera une transmission de type push et une validation dans l’environnement de développement. Vous pouvez vérifier que votre code fonctionne toujours dans la branche Dev avec les dernières informations de la branche NewUpdate.

2.	Fusionnez toutes vos nouvelles validations entre la branche Dev et la branche NewUpdate dans GitHub. Cette action déclenche une transmission de code de type push et une génération dans l’environnement de test.

Du fait que le déploiement continu est déjà configuré avec ces branches git, vous n’avez aucune action à effectuer (comme les générations d’intégration). Vous devez simplement effectuer des actions de contrôle de code source standard à l’aide de git ; Azure s’occupe ensuite de tous les processus de génération à votre place.

Maintenant, nous allons transmettre le code à la branche **NewUpdate**. Dans Git Shell, exécutez les commandes suivantes :

	git checkout NewUpdate
	git pull origin NewUpdate
	git merge Dev
	git push origin NewUpdate

Et voilà !

Accédez au panneau d’application web pour votre environnement de test afin de voir votre validation (fusionnée dans la branche NewUpdate) qui est à présent transférée à l’environnement de test. Cliquez ensuite sur **Parcourir** pour vérifier que la modification du style s’exécute désormais en direct dans Azure.

## Déployer la mise à jour en production ##

La transmission de type push du code à l’environnement intermédiaire et de production doit être similaire à la transmission de type push du code à l’environnement de test. Cette opération est très simple.

Dans Git Shell, exécutez les commandes suivantes :

	git checkout master
	git pull origin master
	git merge NewUpdate
	git push origin master

N’oubliez pas que selon la façon dont l’environnement intermédiaire et de production est programmé dans ProdandStage.json, votre nouveau code est envoyé à l’emplacement **intermédiaire** et s’y exécute. Par conséquent, si vous accédez à l’URL de l’emplacement intermédiaire, vous y verrez le nouveau code en cours d’exécution. Pour ce faire, exécutez l’applet de commande `Show-AzureWebsite` dans Git Shell.

	Show-AzureWebsite -Name ToDoApp<unique_string>master -Slot Staging
 
Une fois que vous avez vérifié la mise à jour dans l’emplacement intermédiaire, la seule chose qui reste à faire est de la faire passer en production. Dans Git Shell, il suffit d’exécuter les commandes suivantes :

	cd <repository_root>\ARMTemplates
	.\swap.ps1 -Name ToDoApp<unique_string>master

Félicitations ! Vous venez de publier une nouvelle mise à jour pour votre application web de production. De plus, vous y êtes parvenu en créant aisément des environnements de développement et de test et en générant et en testant chaque validation. Ces étapes sont essentielles pour le développement logiciel agile.

<a name="delete"></a>
## Supprimer les environnements de développement et de test ##

Comme vous avez volontairement conçu vos environnements de développement et de test en tant que groupes de ressources autonomes, il est très facile de les supprimer. Pour supprimer ceux que vous avez créés dans ce didacticiel, c’est-à-dire les branches GitHub et les artefacts Azure, il suffit d’exécuter les commandes suivantes dans Git Shell :

	git branch -d Dev
	git push origin :Dev
	git branch -d NewUpdate
	git push origin :NewUpdate
	Switch-AzureMode AzureResourceManager
	Remove-AzureResourceGroup -Name ToDoApp<unique_string>dev-group -Force -Verbose
	Remove-AzureResourceGroup -Name ToDoApp<unique_string>newupdate-group -Force -Verbose

## Résumé ##

Le développement logiciel agile est indispensable pour de nombreuses entreprises qui souhaitent adopter Azure comme plateforme d’application. Dans ce didacticiel, vous avez appris à créer et à détruire aisément des réplicas exacts ou proches de l’environnement de production, même pour les applications complexes. Vous avez également appris à exploiter la possibilité de créer un processus de développement capable de créer et de tester chaque validation dans Azure. Ce didacticiel vous a montré comment mieux utiliser conjointement Azure App Service et Azure Resource Manager pour créer une solution d’opérations de développement recourant aux méthodologies agiles. À présent, vous pouvez tirer parti de ce scénario en recourant à des techniques DevOps avancées telles que le [test dans les environnements de production](app-service-web-test-in-production-get-start.md). Pour découvrir un scénario courant de test dans les environnements de production, consultez [Déploiement avec distribution d’une version d’évaluation (test bêta) dans Azure App Service](app-service-web-test-in-production-controlled-test-flight.md).

## Autres ressources ##

-	[Déployer une application complexe de manière prévisible dans Microsoft Azure](app-service-deploy-complex-application-predictably.md)
-	[Développement Agile en pratique : trucs et astuces pour le cycle de développement moderne](http://channel9.msdn.com/Events/Ignite/2015/BRK3707)
-	[Stratégies de déploiement avancées pour les applications web Azure à l’aide des modèles Resource Manager](http://channel9.msdn.com/Events/Build/2015/2-620)
-	[Création de modèles Azure Resource Manager](resource-group-authoring-templates.md)
-	[JSONLint - Validateur JSON](http://jsonlint.com/)
-	[ARMClient – Configurer la publication GitHub sur site](https://github.com/projectKudu/ARMClient/wiki/Setup-GitHub-publishing-to-Site)
-	[Création de branches Git – Branchement et fusion basiques](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-	[Blog de David Ebbo](http://blog.davidebbo.com/)
-	[Azure PowerShell](powershell-install-configure.md)
-	[Outils en ligne de commande multiplateforme Azure](xplat-cli-install.md)
-	[Création ou modification des utilisateurs dans Azure AD](https://msdn.microsoft.com/library/azure/hh967632.aspx#BKMK_1)
-	[Projet Wiki Kudu](https://github.com/projectkudu/kudu/wiki)

<!---HONumber=AcomDC_1203_2015-->