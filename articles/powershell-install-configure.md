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
	ms.date="12/02/2015"
	ms.author="coreyp"/>

# Installation et configuration d’Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

##Qu’est-ce qu’Azure PowerShell ?#
Azure PowerShell est un module fournissant des applets de commande pour gérer Azure avec Windows PowerShell. Ces applets de commande permettent de créer, de tester, de déployer et de gérer des solutions et des services fournis via la plateforme Azure. Dans la plupart des cas, les applets de commande peuvent être utilisées pour les mêmes tâches que le portail de gestion Azure, telles que la création et la configuration de services cloud, de machines virtuelles, de réseaux virtuels et d’applications web.

Le module et le code source sont disponibles au téléchargement sur un référentiel public :

- [PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi)
- [Code source Azure PowerShell 1.0.1](https://github.com/Azure/azure-powershell/archive/v1.0.1-November2015.zip)

<a id="Install"></a>
## Étape 1 : Installer
Téléchargez et installez [PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi) <a id="Connect"></a>

## Étape 2 : Démarrer
Le module installe une console personnalisée pour Azure PowerShell. Vous pouvez exécuter les applets de commande depuis la console Windows PowerShell standard ou depuis la console Azure PowerShell.

## Étape 3 : Connecter
Les applets de commande nécessitent vos informations d’abonnement pour pouvoir gérer vos services. Vous pouvez acheter un abonnement Azure si vous n’en avez pas encore. Pour obtenir des instructions, consultez [Comment acheter Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Tapez **Add-AzureAccount**.

2. Entrez l’adresse de messagerie et le mot de passe associés à votre compte. Azure authentifie et enregistre les informations d’identification, puis ferme la fenêtre.

-Ou-

Connectez-vous à votre compte professionnel ou scolaire :

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

> [AZURE.NOTE]Cette méthode de connexion non interactive fonctionne uniquement avec un compte professionnel ou scolaire. Un compte professionnel ou scolaire représente un utilisateur géré par son travail ou son école et défini dans l’instance d’Azure Active Directory pour son travail ou son école. Si vous ne possédez pas de compte professionnel ou scolaire et que vous utilisez un compte Microsoft pour vous connecter à votre abonnement Azure, vous pouvez en créer un facilement en procédant comme suit.

> 1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com) et cliquez sur **Active Directory**.

> 2. S'il n'existe aucun annuaire, sélectionnez **Create your directory** et fournissez les informations demandées.

> 3. Sélectionnez votre annuaire et ajoutez un nouvel utilisateur. Ce nouvel utilisateur peut se connecter à l’aide d’un compte professionnel ou scolaire. Pendant la création de l'utilisateur, une adresse de messagerie est fournie pour l'utilisateur, ainsi qu'un mot de passe temporaire. Conservez ces informations, car vous en aurez besoin lors de l’étape 5.

> 4. Dans le portail de gestion, sélectionnez **Paramètres**, puis **Administrateurs**. Sélectionnez **Ajouter** et ajoutez le nouvel utilisateur en tant que co-administrateur. Cela permet au compte professionnel ou scolaire de gérer votre abonnement Azure.

> 5. Pour finir, déconnectez-vous du portail Azure et reconnectez-vous en utilisant le compte professionnel ou scolaire. Si vous vous connectez pour la première fois avec ce compte, vous êtes invité à changer le mot de passe.

> Pour plus d’informations sur l’inscription à Microsoft Azure avec un compte professionnel ou scolaire, voir [Inscription à Azure en tant qu’organisation](sign-up-organization.md).

### Afficher les informations détaillées du compte et de l’abonnement

Vous pouvez utiliser plusieurs comptes et abonnements avec Azure PowerShell. Vous pouvez ajouter plusieurs comptes en exécutant **Add-AzureAccount** à plusieurs reprises.

Pour afficher les comptes Azure disponibles, tapez **Get-AzureAccount**.

Pour afficher vos abonnements Azure, tapez **Get-AzureSubscription**.

## Étape 4 : tester <a id="Ex"></a>


Après l’installation du module et la configuration de votre ordinateur pour vous connecter à votre abonnement, vous pouvez créer une application web Azure pour vérifier que tout fonctionne correctement.

1. Démarrez la console Azure PowerShell.

2. Choisissez un nom pour votre application web. Le format de ce nom doit respecter les conventions d’attribution de nom DNS. Il doit uniquement contenir des lettres allant de « a » à « z », des chiffres allant de « 0 » à « 9 » et un tiret « - ».

	Le nom de l’application web doit être unique dans Azure. Dans cet exemple, nous utiliserons le nom « MonSite », mais veillez à choisir un nom différent, comme le nom de votre compte suivi d’un chiffre.

	Une fois le nom choisi, tapez une commande semblable à celle qui suit. Remplacez « MonSite » par le nom de votre application web.

		New-AzureWebsite mySite

	L’applet de commande crée l’application web et renvoie un objet qui représente la nouvelle application web. Les propriétés de l’objet comprennent des informations utiles concernant l’application web.

3. Pour obtenir des informations sur l’application web, tapez cette commande. Vous obtiendrez alors des informations sur toutes les applications web de votre abonnement, y compris sur celle que vous venez de créer.

		Get-AzureWebsite

4. Pour obtenir plus d’informations sur votre application web, ajoutez le nom de l’application web dans la commande. Veillez à remplacer « MonSite » par le nom de votre application web.

		Get-AzureWebsite -Name mySite

5. Les applications web sont démarrées après leur création. Pour arrêter l’application web, tapez cette commande en y ajoutant le nom de votre application web.

		Stop-AzureWebsite -Name mySite

6. Pour vérifier que l'état du site est « arrêté », exécutez de nouveau la commande Get-AzureWebsite.

		Get-AzureWebsite

7. Pour terminer ce test, supprimez l’application web. Type :

		Remove-AzureWebsite -Name mySite

7. Pour terminer cette tâche, confirmez que l’application web est supprimée.

		Get-AzureWebsite -Name mySite

##<a id="Help"></a>Accès à l’aide##

Consultez les ressources suivantes pour obtenir de l’aide sur des applets de commande spécifiques :


-   Dans la console, vous pouvez utiliser le système d’aide intégré, accessible par la commande **Get-Help**. 



- Vous pouvez également obtenir des informations de référence sur les applets de commande des modules Azure PowerShell dans la bibliothèque Azure. Pour plus d’informations, consultez la page [Référence des applets de commande Azure](http://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Pour obtenir de l’aide de la communauté, essayez ces forums populaires :

- [Forum Azure sur MSDN (en anglais)](http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

<!---HONumber=AcomDC_1210_2015-->