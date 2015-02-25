<properties 
	pageTitle="Déploiement intermédiaire dans Sites Web Microsoft Azure" 
	description="Découvrez comment utiliser la publication intermédiaire sur Sites Web Microsoft Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="9/9/2014" 
	ms.author="cephalin"/>

<a name="Overview"></a>
#Déploiement intermédiaire dans Sites Web Microsoft Azure#
Au lieu d'utiliser l'emplacement de production par défaut lorsque vous déployez votre application sur des sites Web Azure, vous avez la possibilité de le faire sur un emplacement de déploiement distinct, car ce sont en réalité des sites actifs possédant leurs propres noms d'hôte. Cette option est disponible dans le plan d'hébergement Web **Standard**. De plus, vous pouvez basculer les sites et les configurations de site entre deux emplacements de déploiement, y compris l'emplacement de production. Le déploiement de votre application sur un emplacement de déploiement présente les avantages suivants :

- Vous pouvez valider les modifications d'un site dans un emplacement de déploiement intermédiaire avant de le basculer en production.

- Après basculement, le précédent site de production se retrouve sur l'emplacement du site précédemment intermédiaire. Si les modifications basculées en production ne vous conviennent pas, vous pouvez effectuer le même basculement afin de récupérer immédiatement le contenu du précédent site qui vous plaisait. 
 
- Le fait de déployer un site sur un emplacement et de le basculer ensuite en production garantit que toutes les instances de l'emplacement sont initialisées avant d'être basculées en production. Cela permet de réduire les risques de défaillances lorsque vous déployez votre site. La redirection du trafic est transparente et aucune demande n'est abandonnée durant les opérations de basculement. 

Quatre emplacements de déploiement, en plus de l'emplacement de production, sont pris en charge pour chaque site en plan **Standard**. 

## Sommaire ##
- [Ajout d'un emplacement de déploiement à un site Web](#Add)
- [À propos de la configuration des emplacements de déploiement](#AboutConfiguration)
- [Basculement des emplacements de déploiement](#Swap)
- [Basculement du site de production vers le site intermédiaire](#Rollback)
- [Suppression de l'emplacement d'un site](#Delete)
- [Cmdlets Azure PowerShell pour les emplacements de site](#PowerShell)
- [Commandes de l'interface de ligne de commande interplateforme Azure (xplat-cli) pour les emplacements de site](#CLI)

<a name="Add"></a>
##Ajout d'un emplacement de déploiement à un site Web##

Le site doit être exécuté en plan d'hébergement **Standard** pour activer plusieurs emplacements de déploiement. 

1. Sur la page Démarrage rapide ou dans la section Aperçu rapide de la page Tableau de bord de votre site Web, cliquez sur **Ajouter un nouvel emplacement de déploiement**. 
	
	![Add a new deployment slot][QGAddNewDeploymentSlot]
	
	> [AZURE.NOTE]
	> Si le site Web n'est pas déjà en mode **Standard**, le message **Vous devez être en mode standard pour activer la publication intermédiaire** s'affiche. À ce moment, vous pouvez sélectionner **Mise à niveau** et accéder à l'onglet **Mise à l'échelle** de votre site Web, avant de continuer.
	
2. Dans la boîte de dialogue **Ajouter un nouvel emplacement de déploiement**, attribuez un nom à l'emplacement et indiquez si vous souhaitez cloner la configuration du site Web depuis un autre emplacement de déploiement existant. Cliquez sur la coche pour continuer. 
	
	![Configuration Source][ConfigurationSource1]
	
	Lorsque vous créez votre tout premier connecteur, vous ne disposez que de deux possibilités : configurer le clonage de l'emplacement de production par défaut ou ne rien configurer du tout. 
	
	Après avoir créé plusieurs emplacements, vous pourrez cloner la configuration depuis un emplacement autre que l'emplacement de production :
	
	![Configuration sources][MultipleConfigurationSources]

5. Dans la liste des sites Web, développez la coche à gauche du nom de votre site Web pour afficher l'emplacement de déploiement. Il portera le nom du site suivi du nom de l'emplacement de déploiement. 
	
	![Site List with Deployment Slot][SiteListWithStagedSite]
	
4. Lorsque vous cliquez sur le nom de l'emplacement du site de déploiement, une page s'ouvre, composée de plusieurs onglets, comme sur n'importe quel autre site Web. <strong><i>nom-de-votre-site-web</i>(<i>nom-de-l-emplacement-du-déploiement</i>)</strong> apparaîtra en haut de la page du portail pour vous rappeler que vous vous trouvez sur l'emplacement du site de déploiement.
	
	![Deployment Slot Title][StagingTitle]
	
5. Cliquez sur l'URL du site mentionné dans la vue Tableau de bord. Notez que l'emplacement du déploiement dispose de son propre nom d'hôte et qu'il est également un site actif. Pour limiter l'accès du public à l'emplacement de déploiement, consultez la page [Sites Web Azure - limiter l'accès Web aux emplacements de déploiement de production](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

	-	 

Il n'y a aucun contenu. Vous pouvez effectuer un déploiement sur l'emplacement d'une autre branche référentielle, ou d'un référentiel complètement différent. Vous pouvez également modifier la configuration de l'emplacement. Utilisez le profil de publication ou les informations d'identification associées à l'emplacement de déploiement pour les mises à jour de contenu.  Par exemple, vous pouvez [publier sur cet emplacement avec Git](http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-publish-source-control/).

<a name="AboutConfiguration"></a>
## À propos de la configuration des emplacements de déploiement##
Lorsque vous clonez la configuration depuis un autre emplacement de déploiement, celle-ci est modifiable. Les listes suivantes présentent la configuration changée lorsque vous effectuez des basculements d'emplacements.

**Configuration changée lors du basculement d'emplacement** :

- Paramètres généraux :
- Chaînes de connexion
- Mappages de gestionnaires
- Paramètres de surveillance et de diagnostics

**Configuration inchangée lors du basculement d'emplacement** :

- Points de terminaison de publication
- Noms de domaine personnalisés
- Certificats SSL et liaisons
- Paramètres de mise à l'échelle

**Remarques** :

- Plusieurs emplacements de déploiement sont uniquement disponibles pour les sites en plan d'hébergement Web **Standard**.

- Lorsque votre site possède plusieurs emplacements, vous ne pouvez pas modifier le plan d'hébergement.

- Vous devez d'abord configurer un emplacement comme s'il était en production avant de procéder à son basculement.

- Un emplacement de déploiement pointe par défaut vers la même base de données que celle du site de production. Cependant, vous pouvez le configurer pour qu'il pointe vers une autre base de données en modifiant sa ou ses chaînes de connexion de base de données. Vous pouvez ensuite restaurer sa ou ses chaînes de connexions de base de données d'origine juste avant de le basculer en production.


<a name="Swap"></a>
##Basculement des emplacements de déploiement##

1. Pour basculer un emplacement de déploiement, sélectionnez-le dans la liste des sites Web que vous souhaitez basculer puis, dans la barre de commandes, cliquez sur le bouton **Swap**. 
	
	![Swap Button][SwapButtonBar]
	
2. La boîte de dialogue Basculement des déploiements s'affiche. Elle vous permet de choisir l'emplacement de site source et le site de destination.
	
	![Swap Deployments Dialog][SwapDeploymentsDialog]
	
3. Cliquez sur la coche pour terminer l'opération. Une fois l'opération terminée, l'emplacement de site est basculé.


<a name="Rollback"></a>
##Basculement du site de production vers le site intermédiaire##
Si vous identifiez des erreurs de production après un basculement d'emplacements, rétablissez ces deux emplacements comme ils étaient à l'origine, en les intervertissant immédiatement. 

<a name="Delete"></a>
##Suppression de l'emplacement d'un site##

Dans la barre de commandes située en bas de la page du portail Sites Web Azure, cliquez sur **Supprimer**. Vous pourrez choisir de supprimer le site Web et tous ses emplacements de déploiement ou de supprimer uniquement l'emplacement de déploiement. 

![Delete a Site Slot][DeleteStagingSiteButton]

**Remarques** :

- La mise à l'échelle est uniquement disponible pour les emplacements de site de production. Elle ne l'est pas pour les autres emplacements de site.

- La gestion des ressources liées est uniquement prise en charge pour les emplacements de site de production. 

- Vous pouvez toujours publier directement sur votre emplacement de production si vous le souhaitez.

- Par défaut, vos emplacements de déploiement (sites) partagent les mêmes ressources que vos emplacements de production (sites) et exécutent les mêmes machines virtuelles. Si vous démarrez un test de contrainte sur un emplacement intermédiaire, votre environnement de production sera soumis à une charge de contrainte similaire. 
	
	> [AZURE.NOTE] Dans le [portail Azure Preview](https://portal.azure.com) uniquement, vous pouvez éviter que cela se produise sur un emplacement de production en déplaçant temporairement l'emplacement hors production vers un autre plan d'hébergement Web. Notez que les emplacements de test et de production doivent une fois de plus partager le même plan d'hébergement Web avant de pouvoir basculer l'emplacement de test en production.

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##Cmdlets Azure PowerShell pour les emplacements de site 

Azure PowerShell est un module fournissant des cmdlets pour gérer Azure via Windows PowerShell, notamment la prise en charge de la gestion des emplacements de déploiement Sites Web Azure. 

- Pour plus d'informations sur l'installation et la configuration d'Azure PowerShell et sur l'authentification d'Azure PowerShell avec votre abonnement Azure, consultez l'article [Installation et configuration d'Azure PowerShell](http://www.windowsazure.com/fr-fr/documentation/articles/install-configure-powershell).  

- Pour répertorier les cmdlets disponibles pour les sites Web Azure dans PowerShell, appelez `help AzureWebsite`. 

----------

###Get-AzureWebsite
La cmdlet **Get-AzureWebsite** permet d'obtenir des informations sur les sites Web Azure de l'abonnement en cours, comme le montre l'exemple suivant. 

`Get-AzureWebsite siteslotstest`

----------

###New-AzureWebsite
Vous pouvez créer un emplacement de site pour n'importe quel site Web en mode Standard en utilisant la cmdlet **New-AzureWebsite** et en indiquant les noms du site et de l'emplacement. Lors de la création de l'emplacement de déploiement, indiquez la même région que celle du site, comme le montre l'exemple suivant. 

`New-AzureWebsite siteslotstest -Slot staging -Location "West US"`

----------

###Publish-AzureWebsiteProject
La cmdlet **Publish-AzureWebsiteProject** permet de déployer du contenu, comme dans l'exemple suivant. 

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

----------

###Show-AzureWebsite
Une fois les mises à jour de contenu et de configuration appliquées au nouvel emplacement, vous pouvez les valider en accédant à l'emplacement en utilisant la cmdlet **Show-AzureWebsite**, comme le montre l'exemple suivant.

`Show-AzureWebsite -Name siteslotstest -Slot staging`

----------

###Switch-AzureWebsiteSlot
La cmdlet **Switch-AzureWebsiteSlot** peut effectuer un basculement pour appliquer l'emplacement de déploiement mis à jour au site de production, comme le montre l'exemple suivant. Le site de production ne subira ni temps mort, ni démarrage à froid. 

`Switch-AzureWebsiteSlot -Name siteslotstest`

----------

###Remove-AzureWebsite
Si vous n'avez plus besoin d'un emplacement de déploiement, vous pouvez le supprimer en utilisant la cmdlet **Remove-AzureWebsite**, comme le montre l'exemple suivant.

`Remove-AzureWebsite -Name siteslotstest -Slot staging` 

----------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
##Commandes de l'interface de ligne de commande interplateforme Azure (xplat-cli) pour les emplacements de site

L'interface de ligne de commande interplateforme Azure (xplat-cli) fournit des commandes interplateformes fonctionnant avec Azure, notamment la prise en charge de la gestion des emplacements de déploiement sur les sites Web Azure. 

- Pour obtenir des instructions sur l'installation et la configuration de l'interface xplat-cli, et notamment des informations sur la connexion de cette dernière à votre abonnement Azure, consultez l'article [Installation et configuration de l'interface de ligne de commande interplateforme Azure](http://www.windowsazure.com/fr-fr/documentation/articles/xplat-cli). 

-  Pour répertorier les commandes disponibles pour les sites Web Azure dans l'interface xplat-cli, appelez `azure site -h`. 

----------
###azure site list
Pour plus d'informations sur les sites Web Azure de l'abonnement en cours, appelez **azure site list**, comme le montre l'exemple suivant.
 
`azure site list siteslotstest`

----------
###azure site create
Pour créer un emplacement de site pour chaque site Web en mode Standard, appelez **azure site create** et indiquez le nom d'un site existant, ainsi que le nom de l'emplacement à créer, comme le montre l'exemple suivant.

`azure site create siteslotstest --slot staging`

Pour activer le contrôle de code source pour le nouvel emplacement, utilisez l'option **--git**, comme dans l'exemple suivant.
 
`azure site create --git siteslotstest --slot staging`

----------
###azure site swap
Pour appliquer l'emplacement de déploiement mis à jour au site de production, utilisez la commande **azure site swap** pour effectuer un basculement, comme le montre l'exemple suivant. Le site de production ne subira ni temps mort, ni démarrage à froid. 

`azure site swap siteslotstest`

----------
###azure site delete
Pour supprimer un emplacement de déploiement dont vous n'avez plus besoin, utilisez la commande **azure site delete**, comme dans l'exemple suivant.

`azure site delete siteslotstest --slot staging`

----------
## Étapes suivantes ##
[Sites Web Azure - limiter l'accès Web aux emplacements de déploiement de production](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Version d'évaluation gratuite de Microsoft Azure](http://azure.microsoft.com/fr-fr/pricing/free-trial/)


<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png


<!--HONumber=42-->
