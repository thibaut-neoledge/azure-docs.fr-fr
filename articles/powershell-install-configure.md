<properties
	pageTitle="Installation et configuration d’Azure PowerShell"
	description="Découvrez comment installer et configurer Azure PowerShell."
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp-at-msft"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="coreyp"/>

# Installation et configuration d’Azure PowerShell

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

##Qu’est-ce qu’Azure PowerShell ?
Azure PowerShell est un ensemble de modules fournissant des applets de commande pour gérer Azure avec Windows PowerShell. Ces applets de commande permettent de créer, de tester, de déployer et de gérer des solutions et des services fournis via la plateforme Azure. Dans la plupart des cas, les applets de commande peuvent être utilisées pour les mêmes tâches que le portail de gestion Azure, telles que la création et la configuration de services cloud, de machines virtuelles, de réseaux virtuels et d’applications web.

<a id="Install"></a>
## Étape 1 : Installer

Voici les deux méthodes d’installation d’Azure PowerShell. Vous pouvez procéder à l’installation à partir de WebPI ou de la galerie PowerShell :

###Installation d’Azure PowerShell depuis Web PI

L’installation d’Azure PowerShell 1.0 et version ultérieure depuis WebPI est similaire à celle de la version 0.9.x. Téléchargez [Azure PowerShell](http://aka.ms/webpi-azps) et lancez l’installation. Si vous avez Azure PowerShell 0.9.x installé, la version 0.9.x sera désinstallée dans le cadre de la mise à niveau. Si vous avez installé des modules Azure PowerShell à partir de PowerShell Gallery, l’installateur supprime automatiquement les modules avant l’installation afin de garantir un environnement Azure PowerShell cohérent.

> [AZURE.NOTE] Si vous aviez précédemment installé les modules Azure à partir de PowerShell Gallery, le programme d’installation les supprimera automatiquement. Vous éviterez ainsi toute confusion au sujet des versions des modules que vous avez installés et de l’endroit où ils se trouvent. Les modules PowerShell Gallery s’installent généralement dans le répertoire **%ProgramFiles%\\WindowsPowerShell\\Modules**. En revanche, le programme d’installation WebPI installe les modules Azure dans **%ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\PowerShell**. Si une erreur se produit lors de l’installation, vous pouvez supprimer manuellement les dossiers Azure* de votre dossier **%ProgramFiles%\\WindowsPowerShell\\Modules** et retenter l’installation.

Une fois l’installation terminée, votre paramètre ```$env:PSModulePath``` doit inclure les répertoires contenant les applets de commande Azure PowerShell.

> [AZURE.NOTE] Il existe un problème connu avec PowerShell **$env: PSModulePath**. Il peut se produire lors de l’installation depuis WebPI. Si votre ordinateur exige un redémarrage suite aux mises à jour du système ou à d’autres installations, il se peut que les mises à jour de **$env: PSModulePath** n’incluent pas le chemin d’accès d’installation d’Azure PowerShell. Si cela se produit, un message « applet de commande non reconnu » peut s’afficher lorsque vous tentez d’utiliser les applets de commande Azure PowerShell après l’installation ou la mise à niveau. Dans ce cas, le redémarrage de l’ordinateur doit résoudre le problème.

Si vous recevez un message similaire à ce qui suit lorsque vous tentez de charger ou d’exécuter des applets de commande :

```
    PS C:\> Get-AzureRmResource
    Get-AzureRmResource : The term 'Get-AzureRmResource' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is
    correct and try again.
    At line:1 char:1
    + Get-AzureRmResource
    + ~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (get-azurermresourcefork:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
```

Ce problème peut être corrigé en redémarrant l’ordinateur.

###Installation d’Azure PowerShell depuis PowerShell Gallery

Installez Azure PowerShell version 1.3.0 ou version supérieure à partir de PowerShell Gallery à l’aide d’une invite de Windows PowerShell ou de l’environnement d’écriture de scripts intégré de PowerShell (ISE) avec élévation de privilèges à l’aide des commandes suivantes :

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

####En savoir plus sur ces commandes.

- **Install-Module AzureRM** installe un module cumulatif pour les applets de commande Azure Resource Manager. Le module AzureRM dépend d’une plage de versions spécifique pour chaque module Azure Resource Manager. La plage de versions incluse garantit qu’aucune modification récente du module ne peut être incluse lors de l’installation des modules AzureRM avec la même version principale. Lorsque vous installez le module AzureRM, tout module Azure Resource Manager qui n’a pas encore été installé sera téléchargé et installé à partir de PowerShell Gallery. Pour plus d’informations sur le contrôle de version sémantique utilisé par les modules Azure PowerShell, consultez [semver.org](http://semver.org). 
- **Install-Module Azure** installe le module Azure. Ce module est le module de gestion des services d’Azure PowerShell 0.9.x. Il ne doit pas représenter de modification majeure et doit pouvoir remplacer la version précédente du module Azure.

## Étape 2 : Démarrer
Vous pouvez exécuter les applets de commande depuis la console Windows PowerShell standard ou depuis l’environnement d’écriture de scripts intégré de Windows PowerShell (ISE). La méthode utilisée pour l’ouverture d’une console ou de l’autre dépend de votre version de Windows :

- Sur un ordinateur exécutant au moins Windows 8 ou Windows Server 2012, vous pouvez utiliser l’outil de recherche intégré. Dans l’écran **Démarrer**, commencez par taper power. Ceci entraîne l’affichage d’une liste d’applications, notamment Windows PowerShell. Cliquez sur une application pour ouvrir la console. (Pour épingler l’application à l’écran **Démarrer**, cliquez avec le bouton droit sur l’icône.)

- Sur un ordinateur exécutant une version antérieure à Windows 8 ou Windows Server 2012, utilisez le menu **Démarrer**. À partir du menu **Démarrer**, cliquez sur **Tous les programmes**, cliquez sur **Accessoires**, cliquez sur le dossier **Windows PowerShell**, puis cliquez sur **Windows PowerShell**.

Vous pouvez également exécuter **Windows PowerShell ISE** pour utiliser des éléments de menu et des raccourcis clavier pour effectuer la plupart des tâches que vous exécuteriez avec la console Windows PowerShell. Pour utiliser l’ISE, dans la console Windows PowerShell, Cmd.exe ou dans la zone **Exécuter** saisissez **powershell\_ise.exe**.

###Commandes pour vous aider à démarrer

    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 
	
    # To login to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster login experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## Étape 3 : Connecter
Les applets de commande nécessitent vos informations d’abonnement pour pouvoir gérer vos services. Vous pouvez acheter un abonnement Azure si vous n’en avez pas encore. Pour obtenir des instructions, consultez [Comment acheter Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Saisissez **Login-AzureRmAccount**

2. Entrez l’adresse de messagerie et le mot de passe associés à votre compte. Azure authentifie et enregistre les informations d’identification, puis ferme la fenêtre.

-Ou-

Connectez-vous à votre compte professionnel ou scolaire :

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [AZURE.NOTE] Si plusieurs clients sont associés à votre compte professionnel, spécifiez le paramètre TenantId :

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [AZURE.NOTE] Cette méthode de connexion non interactive fonctionne uniquement avec un compte professionnel ou scolaire. Un compte professionnel ou scolaire représente un utilisateur géré par son travail ou son école et défini dans l’instance d’Azure Active Directory pour son travail ou son école. Si vous ne possédez pas de compte professionnel ou scolaire et que vous utilisez un compte Microsoft pour vous connecter à votre abonnement Azure, vous pouvez en créer un facilement en procédant comme suit.

> 1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com) et cliquez sur **Active Directory**.

> 2. S'il n'existe aucun annuaire, sélectionnez **Create your directory** et fournissez les informations demandées.

> 3. Sélectionnez votre annuaire et ajoutez un nouvel utilisateur. Ce nouvel utilisateur peut se connecter à l’aide d’un compte professionnel ou scolaire. Pendant la création de l'utilisateur, une adresse de messagerie est fournie pour l'utilisateur, ainsi qu'un mot de passe temporaire. Conservez ces informations, car vous en aurez besoin lors de l’étape 5.

> 4. Dans le portail Azure Classic, sélectionnez **Paramètres**, puis **Administrateurs**. Sélectionnez **Ajouter** et ajoutez le nouvel utilisateur en tant que co-administrateur. Cela permet au compte professionnel ou scolaire de gérer votre abonnement Azure.

> 5. Pour finir, déconnectez-vous du portail Azure Classic et reconnectez-vous en utilisant le compte professionnel ou scolaire. Si vous vous connectez pour la première fois avec ce compte, vous êtes invité à changer le mot de passe.

> Pour plus d’informations sur l’inscription à Microsoft Azure avec un compte professionnel ou scolaire, voir [Inscription à Azure en tant qu’organisation](./active-directory/sign-up-organization.md).

> Pour plus d’informations sur la gestion de l’authentification et de l’abonnement dans Azure, consultez [Gérer des comptes, des abonnements et des rôles d’administrateur](http://go.microsoft.com/fwlink/?LinkId=324796).

### Afficher les informations détaillées du compte et de l’abonnement

Vous pouvez utiliser plusieurs comptes et abonnements avec Azure PowerShell. Vous pouvez ajouter plusieurs comptes en exécutant **Add-AzureAccount** à plusieurs reprises.

Pour afficher les comptes Azure disponibles, tapez **Get-AzureAccount**.

Pour afficher vos abonnements Azure, tapez **Get-AzureSubscription**.

##<a id="Help"></a>Accès à l’aide##

Consultez les ressources suivantes pour obtenir de l’aide sur des applets de commande spécifiques :


-   Dans la console, vous pouvez utiliser le système d’aide intégré, accessible par la commande **Get-Help**. 

- Pour obtenir de l’aide de la communauté, essayez ces forums populaires :

 - [Forum Azure sur MSDN (en anglais)](http://go.microsoft.com/fwlink/p/?LinkId=320212)
 - [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

##En savoir plus


Pour en savoir plus sur l’utilisation des applets de commande, consultez les ressources suivantes :

Pour obtenir des instructions de base sur l’utilisation de Windows PowerShell, voir [Utilisation Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Pour obtenir des informations de référence sur les applets de commande, voir [Référence des applets de commande Azure](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Pour obtenir des exemples de script et des instructions d’utilisation des scripts pour gérer Azure, voir [Centre de scripts](http://go.microsoft.com/fwlink/p/?LinkId=321940).

<!---HONumber=AcomDC_0330_2016-->