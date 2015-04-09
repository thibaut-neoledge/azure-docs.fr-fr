<properties
	pageTitle="Configurer des environnements intermédiaires pour les applications web dans Azure App Service
	description="Découvrez comment utiliser la publication intermédiaire pour les applications web dans Azure App Service.
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	writer="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="cephalin"/>

<a name="Overview"></a>
# Configurer des environnements intermédiaires pour les applications web dans Azure App Service

Lorsque vous déployez votre application web vers [App Service](http://go.microsoft.com/fwlink/?LinkId=529714), vous pouvez la déployer vers un emplacement de déploiement distinct et non vers l'emplacement de production par défaut lors de l'exécution du mode de plan App Service **standard** ou **Premium**. Les emplacements de déploiement sont en fait des applications web dynamiques pourvues de leur propre nom d'hôte. Les éléments de contenu et de configuration des applications web peuvent être échangés entre deux emplacements de déploiement, y compris l'emplacement de production. Le déploiement de votre application sur un emplacement de déploiement présente les avantages suivants :

- Vous pouvez valider les modifications d'une application web dans un emplacement de déploiement intermédiaire avant de l'échanger avec l'emplacement de production.

- Déployer d'abord une application web vers un emplacement et la basculer ensuite en production garantit que toutes les instances de l'emplacement sont initialisées avant d'être basculées en production. Cela élimine les temps d'arrêt lorsque vous déployez votre application web. La redirection du trafic est transparente et aucune demande n'est abandonnée durant les opérations de basculement. L'intégralité de ce flux de travail peut être automatisée en configurant [Échange automatique](#Configure-Auto-Swap-for-your-web-app) lorsque la validation préalable à l'échange n'est pas nécessaire.

- À l'issue d'un échange, l'emplacement occupé par une application web précédemment intermédiaire dispose désormais de l'application web précédemment en production. Si les modifications basculées en production ne vous conviennent pas, vous pouvez effectuer le même basculement afin de récupérer immédiatement le contenu du précédent site qui vous plaisait.

Chaque mode de plan App Service prend en charge un nombre différent d'emplacements de déploiement. Pour connaître le nombre d'emplacements pris en charge par le mode de votre application web, voir [Tarification d'App Service](/pricing/details/app-service/). 

- Si votre application web dispose de plusieurs emplacements, vous ne pouvez pas changer de mode.

- La mise à l'échelle est uniquement disponible pour les emplacements de site de production.

- La gestion des ressources liées est uniquement prise en charge pour les emplacements de site de production.

	> [AZURE.NOTE] Dans le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715) uniquement, vous pouvez éviter cet impact potentiel dans un emplacement de production en déplaçant temporairement l'emplacement autre que de production vers un autre mode de plan App Service. Notez que l'emplacement autre que de production doit une fois encore partager le même mode que l'emplacement de production avant que vous ne puissiez échanger les deux emplacements.

<a name="Add"></a>
## Ajouter un emplacement de déploiement à une application web ##

Pour que vous puissiez activer plusieurs emplacements de déploiement, l'application web doit s'exécuter en mode **standard** ou **Premium**.

1. Dans le [portail Azure en version préliminaire](https://portal.azure.com/), ouvrez le panneau de votre application web.
2. Cliquez sur **Emplacements de déploiement**, puis, dans le panneau **Emplacements de déploiement**, cliquez sur **Ajouter un emplacement**.

	![Ajouter un nouvel emplacement de déploiement][QGAddNewDeploymentSlot]

	> [AZURE.NOTE]
	> Si l'application web n'est pas déjà en mode **standard** ou **Premium**, vous allez recevoir un message indiquant les modes pris en charge pour l'activation de la publication intermédiaire. À ce stade, vous pouvez sélectionner **Mettre à niveau** et accéder à l'onglet **Mettre à l'échelle** de votre application web avant de continuer.

2. Dans le panneau **Ajouter un emplacement**, nommez l'emplacement, puis indiquez si vous souhaitez cloner la configuration de l'application web à partir d'un autre emplacement de déploiement existant. Cliquez sur la coche pour continuer.

	![Source de configuration][ConfigurationSource1]

	La première fois que vous ajoutez un emplacement, vous avez uniquement deux choix : cloner la configuration à partir de l'emplacement par défaut en production ou ne rien cloner du tout.

	Après avoir créé plusieurs emplacements, vous pourrez cloner la configuration depuis un emplacement autre que l'emplacement de production :

	![Sources de configuration][MultipleConfigurationSources]

5. Dans le panneau **Emplacements de déploiement**, cliquez sur l'emplacement de déploiement pour ouvrir un panneau pour ce dernier, avec un ensemble de mesures et de paramètres de configuration comme toute autre application web. <strong><i>nom-votre-application-web</i>-<i>nom-emplacement-de-déploiement</i></strong> apparaît en haut du panneau pour vous rappeler que vous visionnez l'emplacement de déploiement.

	![Titre de l'emplacement de déploiement][StagingTitle]

5. Cliquez sur l'URL de l'application dans le panneau de l'emplacement. Notez que l'emplacement de déploiement possède son propre nom d'hôte et qu'il s'agit d'une application dynamique. Pour limiter l'accès public à l'emplacement de déploiement, voir [Application web App Service : bloquer l'accès web aux emplacements de déploiement autres que de production](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Il n'existe pas de contenu après la création de l'emplacement de déploiement. Vous pouvez effectuer un déploiement sur l'emplacement d'une autre branche référentielle, ou d'un référentiel complètement différent. Vous pouvez également modifier la configuration de l'emplacement. Utilisez le profil de publication ou les informations d'identification associées à l'emplacement de déploiement pour les mises à jour de contenu.  Par exemple, vous pouvez [publier du contenu vers cet emplacement à l'aide de Git](web-sites-publish-source-control.md).

<a name="AboutConfiguration"></a>
## Configuration d'emplacements de déploiement ##
Lorsque vous clonez la configuration depuis un autre emplacement de déploiement, celle-ci est modifiable. Par ailleurs, après un échange, certains éléments de configuration suivent le contenu (éléments non propres à un emplacement) tandis que d'autres restent dans le même emplacement (éléments propres à un emplacement). Les listes suivantes présentent la configuration changée lorsque vous effectuez des basculements d'emplacements.

**Paramètres qui sont échangés** :

- Paramètres généraux, par exemple versions d'infrastructure, 32/64 bits, sockets web
- Paramètres d'application (peuvent être configurés pour respecter un emplacement)
- Chaînes de connexion (peuvent être configurées pour respecter un emplacement)
- Mappages de gestionnaires
- Paramètres de surveillance et de diagnostics
- Contenu WebJobs

**Paramètres qui ne sont pas échangés** :

- Points de terminaison de publication
- Noms de domaine personnalisés
- Certificats SSL et liaisons
- Paramètres de mise à l'échelle
- Planificateurs WebJobs

Pour configurer un paramètre d'application ou une chaîne de connexion pour qu'ils respectent un emplacement (non échangé), accédez au panneau **Paramètres de l'application** d'un emplacement spécifique, puis cochez la case **Paramètre d'emplacement** correspondant aux éléments de configuration qui doivent respecter l'emplacement. Notez que si vous marquez un élément de configuration comme propre à un emplacement, cet élément ne pourra pas être échangé entre tous les emplacements de déploiement associés à l'application web.

![Paramètres d'emplacement][SlotSettings]

<a name="Swap"></a>
## Pour échanger les emplacements de déploiement ##

>[AZURE.IMPORTANT] Avant de basculer une application web d'un emplacement de déploiement vers un emplacement de production, assurez-vous que tous les paramètres non propres à un emplacement sont configurés tels que vous le souhaitez dans la cible de l'échange.

1. Pour échanger des emplacements de déploiement, cliquez sur le bouton **Déplacer** dans la barre de commandes de l'application web ou dans celle d'un emplacement de déploiement. Assurez-vous que la source et la cible de l'échange sont définies correctement. En règle générale, la cible de l'échange correspond à l'emplacement de production.  

	![Bouton Déplacer][SwapButtonBar]

3. Cliquez sur **OK** pour terminer l'opération. À l'issue de l'opération, les emplacements de déploiement ont été échangés.

## Configurer l'échange automatique pour une application web ##

L'échange automatique simplifie les scénarios des opérations de développement dans lesquels vous souhaitez déployer votre application web en continu sans démarrage à froid ni temps d'arrêt pour les clients finaux. Si un emplacement de déploiement est configuré pour l'échange automatique en production, chaque fois que vous envoyez une mise à jour de votre code par une transmission de type push vers cet emplacement, App Service échange automatiquement l'application web en production après l'avoir initialisée dans l'emplacement.

>[AZURE.IMPORTANT] Lorsque vous activez l'échange automatique pour un emplacement, vérifiez que la configuration de l'emplacement est exactement celle que vous souhaitez pour l'emplacement cible (en général l'emplacement de production).

La configuration de l'échange automatique pour un emplacement est facile. Pour ce faire, procédez comme suit :

1. Dans le panneau **Emplacements de déploiement**, sélectionnez un emplacement autre que de production, puis cliquez sur **Tous les paramètres** pour le panneau de cet emplacement.  

	![][Autoswap1]

2. Cliquez sur **Paramètres de l'application**. Sélectionnez **Activé** pour **Échange automatique**, ainsi que l'emplacement cible souhaité dans **Échanger automatiquement un emplacement**, puis cliquez sur **Enregistrer** dans la barre de commandes. Assurez-vous que la configuration de l'emplacement est celle que vous souhaitez pour l'emplacement cible.

	À l'issue de l'opération, un **SUCCÈS** vert clignote dans l'onglet **Notifications**.

	![][Autoswap2]

	>[AZURE.NOTE] Pour tester l'échange automatique pour votre application web, vous pouvez sélectionner d'abord un emplacement cible autre que de production dans **Échanger automatiquement un emplacement** pour vous familiariser avec la fonctionnalité.  

3. Exécutez une transmission de code de type push vers cet emplacement de déploiement. L'échange automatique se produit peu après, et la mise à jour est appliquée dans l'URL de votre emplacement cible.

<a name="Rollback"></a>
## Pour restaurer une application de production après un échange ##
Si vous identifiez des erreurs de production après un échange d'emplacements, restaurez les deux emplacements comme ils étaient à l'origine, en les intervertissant immédiatement.

<a name="Delete"></a>
## Pour supprimer un emplacement de déploiement ##

Dans la barre de commandes du panneau d'un emplacement de déploiement, cliquez sur **Supprimer**.  

![Supprimer un emplacement de déploiement][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## Cmdlets Azure PowerShell pour les emplacements de déploiement

Azure PowerShell est un module qui fournit des cmdlets pour gérer Azure via Windows PowerShell, notamment la prise en charge de la gestion des emplacements de déploiement des applications web dans Azure App Service.

- Pour plus d'informations sur l'installation et la configuration d'Azure PowerShell et sur l'authentification d'Azure PowerShell avec votre abonnement Azure, voir [Installation et configuration de Microsoft Azure PowerShell](install-configure-powershell.md).  

- Pour répertorier les cmdlets disponibles pour Azure App Service dans PowerShell, appelez `help AzureWebsite`.

----------

### Get-AzureWebsite
La cmdlet **Get-AzureWebsite** permet d'obtenir des informations sur les applications web Azure de l'abonnement en cours, comme dans l'exemple suivant.

`Get-AzureWebsite webappslotstest`

----------

### New-AzureWebsite
Vous pouvez créer un emplacement de déploiement à l'aide de la cmdlet **New-AzureWebsite** et en spécifiant les noms de l'application web et de l'emplacement. Lors de la création de l'emplacement de déploiement, indiquez également la même région que celle de l'application web, comme dans l'exemple suivant.

`New-AzureWebsite webappslotstest -Slot staging -Location "West US"`

----------

### Publish-AzureWebsiteProject
La cmdlet **Publish-AzureWebsiteProject** permet de déployer du contenu, comme dans l'exemple suivant.

`Publish-AzureWebsiteProject -Name webappslotstest -Slot staging -Package [path].zip`

----------

### Show-AzureWebsite
Une fois les mises à jour de contenu et de configuration appliquées au nouvel emplacement, vous pouvez les valider en accédant à l'emplacement en utilisant la cmdlet **Show-AzureWebsite**, comme dans l'exemple suivant.

`Show-AzureWebsite -Name webappslotstest -Slot staging`

----------

### Switch-AzureWebsiteSlot
La cmdlet **Switch-AzureWebsiteSlot** permet d'effectuer un échange pour appliquer l'emplacement de déploiement mis à jour au site de production, comme dans l'exemple suivant. L'application de production ne subira ni temps d'arrêt, ni démarrage à froid.

`Switch-AzureWebsiteSlot -Name webappslotstest`

----------

### Remove-AzureWebsite
Si vous n'avez plus besoin d'un emplacement de déploiement, vous pouvez le supprimer en utilisant la cmdlet **Remove-AzureWebsite**, comme dans l'exemple suivant.

`Remove-AzureWebsite -Name webappslotstest -Slot staging`

----------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
## Commandes de l'interface de ligne de commande interplateforme Azure (xplat-cli) pour les emplacements de déploiement

L'interface de ligne de commande interplateforme Azure (xplat-cli) fournit des commandes interplateformes pour utiliser Azure, notamment la prise en charge de la gestion des emplacements de déploiement des applications web.

- Pour obtenir des instructions sur l'installation et la configuration de l'interface xplat-cli, notamment des informations sur la connexion de cette dernière à votre abonnement Azure, voir [Installer et configurer l'interface de ligne de commande interplateforme Azure](xplat-cli.md).

-  Pour répertorier les commandes disponibles pour Azure App Service dans l'interface xplat-cli, appelez `azure site -h`.

----------
### azure site list
Pour plus d'informations sur les applications web de l'abonnement en cours, appelez **azure site list**, comme dans l'exemple suivant.

`azure site list webappslotstest`

----------
### azure site create
Pour créer un emplacement de déploiement, appelez **azure site create** et spécifiez le nom d'une application web existante et le nom de l'emplacement à créer, comme dans l'exemple suivant.

`azure site create webappslotstest --slot staging`

Pour activer le contrôle de code source pour le nouvel emplacement, utilisez l'option **--git**, comme dans l'exemple suivant.

`azure site create --git webappslotstest --slot staging`

----------
### azure site swap
Pour appliquer l'emplacement de déploiement mis à jour à l'application de production, utilisez la commande **azure site swap** pour effectuer un échange, comme dans l'exemple suivant. L'application de production ne subira ni temps d'arrêt, ni démarrage à froid.

`azure site swap webappslotstest`

----------
### azure site delete
Pour supprimer un emplacement de déploiement dont vous n'avez plus besoin, utilisez la commande **azure site delete**, comme dans l'exemple suivant.

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE] Si vous souhaitez prendre en main Azure App Service avant de créer un compte Azure, accédez à [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez immédiatement créer une application web de démarrage de courte durée dans App Service. Aucune carte de crédit n'est requise ; vous ne prenez aucun engagement.

## Étapes suivantes ##
[Application web Azure App Service : bloquer l'accès web aux emplacements de déploiement autres que de production](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Version d'évaluation gratuite de Microsoft Azure](/pricing/free-trial/)

## Nouveautés
* Pour obtenir un guide sur la transformation de Sites web en App Service, voir : [Azure App Service et son impact sur les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour obtenir un guide sur les modifications apportées à l'ancien portail dans le nouveau portail, voir : [Référence pour naviguer dans la version préliminaire du portail](http://go.microsoft.com/fwlink/?LinkId=529715)

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
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png

<!--HONumber=49-->