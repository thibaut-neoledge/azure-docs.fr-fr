<properties
	pageTitle="Configurer des environnements intermédiaires pour les applications web dans Azure App Service"
	description="Découvrez comment utiliser la publication intermédiaire pour les applications web dans Azure App Service."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	writer="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2015"
	ms.author="cephalin"/>

# Configurer des environnements intermédiaires pour les applications web dans Azure App Service
<a name="Overview"></a>

Lorsque vous déployez votre application web dans [App Service](http://go.microsoft.com/fwlink/?LinkId=529714), vous pouvez cibler un emplacement de déploiement autre que l’emplacement de production par défaut en mode **Standard** ou **Premium**. Les emplacements de déploiement sont en fait des applications web dynamiques pourvues de leur propre nom d’hôte. Les éléments de contenu et de configuration des applications web peuvent être échangés entre deux emplacements de déploiement, y compris l’emplacement de production. Le déploiement de votre application sur un emplacement de déploiement présente les avantages suivants :

- Vous pouvez valider les modifications d’une application web dans un emplacement de déploiement intermédiaire avant de l’échanger avec l’emplacement de production.

- Déployer d’abord une application web vers un emplacement et la basculer ensuite en production garantit que toutes les instances de l’emplacement sont initialisées avant d’être basculées en production. Cela élimine les temps d’arrêt lorsque vous déployez votre application web. La redirection du trafic est transparente et aucune demande n'est abandonnée durant les opérations de basculement. Ce flux de travail peut être entièrement automatisé en configurant [Échange automatique](#configure-auto-swap-for-your-web-app) lorsqu’aucune validation n’est requise avant l’échange.

- À l’issue d’un échange, l’emplacement occupé par une application web précédemment intermédiaire dispose désormais de l’application web précédemment en production. Si les modifications basculées en production ne vous conviennent pas, vous pouvez effectuer le même basculement afin de récupérer immédiatement le contenu du précédent site qui vous plaisez.

Chaque mode de plan App Service prend en charge un nombre différent d’emplacements de déploiement. Pour connaître le nombre d’emplacements pris en charge par le mode de votre application web, consultez la page [Tarification d’App Service](/pricing/details/app-service/).

- Si votre application web dispose de plusieurs emplacements, vous ne pouvez pas changer de mode.

- La mise à l'échelle est uniquement disponible pour les emplacements de site de production.

- La gestion des ressources liées est uniquement prise en charge pour les emplacements de site de production. Dans le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715) uniquement, vous pouvez éviter cet impact potentiel sur un emplacement de production en déplaçant temporairement l’emplacement autre que de production vers un autre mode de plan App Service. Notez que l’emplacement autre que de production doit une fois encore partager le même mode que l’emplacement de production avant que vous ne puissiez échanger les deux emplacements.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="Add"></a>
## Ajouter un emplacement de déploiement à une application web ##

Pour que vous puissiez activer plusieurs emplacements de déploiement, l’application web doit s’exécuter en mode **Standard** ou **Premium**.

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez le panneau de votre application web.
2. Cliquez sur **Emplacements de déploiement**. Puis, dans le panneau **Emplacements de déploiement**, cliquez sur **Ajouter un emplacement**.

	![Add a new deployment slot][QGAddNewDeploymentSlot]

	> [AZURE.NOTE]Si l’application web n’est pas en mode **Standard** ou **Premium**, vous recevez un message indiquant les modes pris en charge pour l’activation de la publication intermédiaire. À ce stade, vous pouvez sélectionner **Mettre à niveau** et accéder à l’onglet **Mettre à l’échelle** de votre application web avant de continuer.

2. Dans le panneau **Ajouter un emplacement**, nommez l’emplacement, puis indiquez si vous souhaitez cloner la configuration de l’application web à partir d’un autre emplacement de déploiement. Cliquez sur la coche pour continuer.

	![Source de configuration][ConfigurationSource1]

	La première fois que vous ajoutez un emplacement, vous avez uniquement deux choix : cloner la configuration à partir de l’emplacement par défaut en production ou ne rien cloner du tout.

	Après avoir créé plusieurs emplacements, vous pourrez cloner la configuration depuis un emplacement autre que l'emplacement de production :

	![Sources de configuration][MultipleConfigurationSources]

5. Dans le panneau **Emplacements de déploiement**, cliquez sur l’emplacement de déploiement pour ouvrir le panneau correspondant avec des métriques et une configuration, à l’instar de n’importe quelle application web. **nom-votre-application-web-nom-emplacement-déploiement** apparaît en haut du panneau pour vous rappeler que c’est l’emplacement de déploiement qui est indiqué.

	![Titre de l'emplacement de déploiement][StagingTitle]

5. Cliquez sur l’URL de l’application dans le panneau de l’emplacement. Notez que l’emplacement de déploiement possède son propre nom d’hôte et qu’il s’agit d’une application dynamique. Pour limiter l’accès public à l’emplacement de déploiement, consultez la page [Application web App Service : bloquer l’accès web aux emplacements de déploiement autres que de production](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Il n’existe pas de contenu après la création de l’emplacement de déploiement. Vous pouvez effectuer un déploiement sur l'emplacement d'une autre branche référentielle, ou d'un référentiel complètement différent. Vous pouvez également modifier la configuration de l'emplacement. Utilisez le profil de publication ou les informations d'identification associées à l'emplacement de déploiement pour les mises à jour de contenu. Par exemple, vous pouvez [publier sur cet emplacement avec Git](web-sites-publish-source-control.md).

<a name="AboutConfiguration"></a>
## Configuration d’emplacements de déploiement ##
Lorsque vous clonez la configuration depuis un autre emplacement de déploiement, celle-ci est modifiable. Par ailleurs, après un échange, certains éléments de configuration suivent le contenu (éléments non propres à un emplacement) tandis que d’autres restent dans le même emplacement (éléments propres à un emplacement). Les listes suivantes présentent la configuration changée lorsque vous effectuez des basculements d'emplacements.

**Paramètres échangés** :

- Paramètres généraux, par exemple versions d’infrastructure, 32/64 bits, sockets web
- Paramètres d’application (peuvent être configurés pour respecter un emplacement)
- Chaînes de connexion (peuvent être configurées pour respecter un emplacement)
- Mappages de gestionnaires
- Paramètres de surveillance et de diagnostics
- Contenu WebJobs

**Paramètres non échangés** :

- Points de terminaison de publication
- Noms de domaine personnalisés
- Certificats SSL et liaisons
- Paramètres de mise à l'échelle
- Planificateurs WebJobs

Pour lier un paramètre d’application ou une chaîne de connexion à un emplacement (aucun échange), accédez au panneau **Paramètres de l’application** d’un emplacement, puis cochez la case **Paramètre d’emplacement** correspondant aux éléments de configuration à lier à cet emplacement. Notez que si vous marquez un élément de configuration comme propre à un emplacement, cet élément ne pourra pas être échangé entre tous les emplacements de déploiement associés à l’application web.

![Paramètres d’emplacement][SlotSettings]

<a name="Swap"></a>
## Pour échanger des emplacements de déploiement ##

>[AZURE.IMPORTANT]Avant de basculer une application web depuis un emplacement de déploiement vers un emplacement de production, assurez-vous que tous les paramètres non propres à un emplacement sont configurés comme vous le souhaitez dans la cible de l’échange.

1. Pour échanger des emplacements de déploiement, cliquez sur le bouton **Échanger** dans la barre de commandes de l’application web ou dans celle d’un emplacement de déploiement. Assurez-vous que la source et la cible de l’échange sont définies correctement. En règle générale, la cible de l’échange correspond à l’emplacement de production.  

	![Bouton Swap][SwapButtonBar]

3. Cliquez sur **OK** pour terminer l’opération. À l’issue de l’opération, les emplacements de déploiement ont été échangés.

## Configurer l’échange automatique pour une application web ##

L’échange automatique simplifie les scénarios des opérations de développement dans lesquels vous souhaitez déployer votre application web en continu sans démarrage à froid ni temps d’arrêt pour les clients finaux. Si un emplacement de déploiement est configuré pour l’échange automatique en production, chaque fois que vous envoyez une mise à jour de votre code par une transmission de type push vers cet emplacement, App Service échange automatiquement l’application web en production après l’avoir initialisée dans l’emplacement.

>[AZURE.IMPORTANT]Lorsque vous activez l’échange automatique pour un emplacement, vérifiez que la configuration de l’emplacement est exactement celle que vous souhaitez pour l’emplacement cible (en général, l’emplacement de production).

La configuration de l’échange automatique pour un emplacement est facile. Pour ce faire, procédez comme suit :

1. Dans le panneau **Emplacements de déploiement**, sélectionnez un emplacement autre que de production, puis cliquez sur **Tous les paramètres** pour le panneau de cet emplacement.  

	![][Autoswap1]

2. Cliquez sur **Paramètres de l’application**. Sélectionnez **Activé** dans **Échange automatique**, ainsi que l’emplacement cible souhaité dans **Échanger automatiquement un emplacement**, puis cliquez sur **Enregistrer** dans la barre de commandes. Assurez-vous que la configuration de l’emplacement est celle que vous souhaitez pour l’emplacement cible.

	Dans l’onglet **Notifications**, la mention **RÉUSSITE** clignote en vert une fois l’opération terminée.

	![][Autoswap2]

	>[AZURE.NOTE]Pour tester l’échange automatique sur votre application web, commencez par sélectionner un emplacement cible autre que de production dans **Échanger automatiquement un emplacement** pour vous familiariser avec la fonctionnalité.

3. Exécutez une transmission de code de type push vers cet emplacement de déploiement. L’échange automatique se produit peu après, et la mise à jour est appliquée dans l’URL de votre emplacement cible.

<a name="Multi-Phase"></a>
## Utiliser l’échange multiphase pour votre application web ##

L’échange multiphase est disponible pour simplifier la validation dans le contexte des éléments de configuration conçu conformément à un emplacement tel que des chaînes de connexion. Dans ces cas, il peut être utile d’appliquer de tels éléments de configuration entre la cible d’échange et la source d’échange et de les valider avant que l’échange n’entre réellement en vigueur. Une fois que les éléments de configuration de cibles d’échange sont appliqués à la source d’échange, les actions disponibles effectuent l’échange ou bien rétablissent la configuration d’origine de la source d’échange ce qui a également pour effet d’annuler l’échange. Des exemples pour les applets de commande Azure PowerShell disponibles pour l’échange multiphase figurent dans les applets de commande Azure PowerShell de la section des emplacements de déploiement.

<a name="Rollback"></a>
## Pour rétablir une application de production après un échange ##
Si vous identifiez des erreurs de production après un basculement d'emplacements, rétablissez ces deux emplacements comme ils étaient, en les intervertissant immédiatement.

<a name="Delete"></a>
## Pour supprimer un emplacement de déploiement##

Dans la barre de commandes du panneau d’un emplacement de déploiement, cliquez sur **Supprimer**.

![Supprimer un emplacement de déploiement][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## Cmdlets Azure PowerShell pour les emplacements de déploiement

Azure PowerShell est un module qui fournit des cmdlets pour gérer Azure via Windows PowerShell, notamment la prise en charge de la gestion des emplacements de déploiement des applications web dans Azure App Service.

- Pour plus d’informations sur l’installation et la configuration d’Azure PowerShell et sur l’authentification d’Azure PowerShell avec votre abonnement Azure, consultez la page [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md).  

- Afin d’utiliser le nouveau mode Azure Resource Manager pour les applets de commande PowerShell, commencez par ce qui suit : `Switch-AzureMode -Name AzureResourceManager`.

----------

### Créer une application web

`New-AzureWebApp -ResourceGroupName [resource group name] -Name [web app name] -Location [location] -AppServicePlan [app service plan name]`

----------

### Créer un emplacement de déploiement pour une application web

`New-AzureWebApp -ResourceGroupName [resource group name] -Name [web app name] -SlotName [deployment slot name] -Location [location] -AppServicePlan [app service plan name]`

----------

### Initier un échange multiphase et appliquer la configuration d’emplacement cible à l’emplacement source

`$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}` `Invoke-AzureResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01`

----------

### Rétablir la première phase d’échange d’un échange multiphase et restaurer la configuration d’emplacement source

`Invoke-AzureResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01`

----------

### Échanger des emplacements de déploiement

`$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}` `Invoke-AzureResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01`

----------

### Supprimer un emplacement de déploiement

`Remove-AzureResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [web app name]/[slot name] -ApiVersion 2015-07-01`

----------

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>
## Commandes de l’interface de ligne de commande Azure pour les emplacements de déploiement

L’interface de ligne de commande Azure fournit des commandes interplateformes fonctionnant avec Azure, notamment la prise en charge de la gestion des emplacements de déploiement des applications Web.

- Pour obtenir des instructions sur l’installation et la configuration de l’interface de ligne de commande Azure, et notamment des informations sur la connexion de cette dernière à votre abonnement Azure, consultez la rubrique [Installation et configuration de l’interface de ligne de commande Azure](../xplat-cli-install.md).

-  Pour répertorier les commandes disponibles pour Azure App Service dans l’interface de ligne de commande Azure, appelez `azure site -h`.

----------
### azure site list
Pour plus d’informations sur les applications web dans l’abonnement en cours, appelez **azure site list**, comme dans l’exemple suivant.

`azure site list webappslotstest`

----------
### azure site create
Pour créer un emplacement de déploiement, appelez **azure site create** et spécifiez le nom d’une application web et de l’emplacement à créer, comme dans l’exemple suivant.

`azure site create webappslotstest --slot staging`

Pour activer le contrôle de code source pour le nouvel emplacement, utilisez l'option **--git**, comme dans l'exemple suivant.

`azure site create --git webappslotstest --slot staging`

----------
### azure site swap
Pour appliquer l’emplacement de déploiement mis à jour à l’application de production, utilisez la commande **azure site swap** pour effectuer un échange, comme dans l’exemple suivant. L’application de production ne subira ni temps d’arrêt, ni démarrage à froid.

`azure site swap webappslotstest`

----------
### azure site delete
Pour supprimer un emplacement de déploiement dont vous n'avez plus besoin, utilisez la commande **azure site delete**, comme dans l'exemple suivant.

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Étapes suivantes ##
[Application web Azure App Service : bloquer l’accès web aux emplacements de déploiement autres que de production](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Version d’évaluation gratuite de Microsoft Azure](/pricing/free-trial/)

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- IMAGES -->
[QGAddNewDeploymentSlot]: ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]: ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png
 

<!---HONumber=AcomDC_1203_2015-->