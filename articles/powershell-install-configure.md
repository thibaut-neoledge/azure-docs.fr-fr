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
	ms.date="02/20/2015"
	ms.author="coreyp"/>

# Installation et configuration d’Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

Windows PowerShell permet d’effectuer de nombreuses tâches dans Azure, de façon interactive (via l’invite de commandes) ou automatique (via des scripts). Azure PowerShell est un module fournissant des cmdlets pour gérer Azure via Windows PowerShell. Ces cmdlets permettent de créer, de tester, de déployer et de gérer des solutions et des services fournis via la plateforme Azure. Dans la plupart des cas, les cmdlets permettent d’effectuer les mêmes tâches que celles du portail de gestion Azure. Par exemple, vous pouvez créer et configurer des services cloud, des machines virtuelles, des réseaux virtuels et des applications web.

Le module est distribué en tant que fichier téléchargeable et le code source est géré via un référentiel public. Un lien vers le fichier téléchargeable est fourni dans les instructions d’installation figurant plus loin dans cette rubrique. Pour plus d’informations sur le code source, consultez la page [Référentiel du code Azure PowerShell](https://github.com/Azure/azure-powershell).

Ce guide fournit des informations de base sur l’installation et la configuration de Windows Azure PowerShell pour gérer la plateforme Azure.

### <a id="Prereq"></a>Conditions préalables à l’utilisation d’Azure PowerShell

Azure est une plateforme disponible par abonnement. Cela signifie qu’un abonnement est requis pour l’utiliser. Dans la plupart des cas, cela signifie également que les cmdlets requièrent des informations d’abonnement pour effectuer les tâches associées. (Il est possible d’utiliser certaines cmdlets relatives au stockage sans ces informations.) Vous pouvez fournir ces informations en configurant votre ordinateur pour vous connecter avec votre abonnement. Vous trouverez des instructions à ce sujet dans la suite de cet article, à la section « Connexion à votre abonnement ».

> [AZURE.NOTE]Depuis la version 0.8.5, les modules Azure PowerShell nécessitent Microsoft .NET Framework 4.5.

Lors de l’installation du module, le programme d’installation recherche les logiciels requis sur votre système et installe toutes les dépendances, comme les versions adéquates de Windows PowerShell et de .NET Framework.

<a id="Install"></a>
## Installation d’Azure PowerShell

Vous pouvez télécharger et installer les modules Azure PowerShell en exécutant [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?LinkId=320376). À l’invite, cliquez sur **Exécuter**. Web Platform Installer installe les modules Azure PowerShell et toutes les dépendances. Suivez les instructions de l’invite pour terminer l’installation.

> [AZURE.NOTE]Si vous voulez simplement télécharger le programme d’installation de PowerShell, visitez https://github.com/Azure/azure-powershell/releases. Le code source pour les cmdlets PowerShell se trouve également sur ce référentiel

Pour plus d’informations sur les outils en ligne de commande disponibles pour Azure, consultez la page [Outils en ligne de commande](http://go.microsoft.com/fwlink/?LinkId=320796).

L’installation du module entraîne celle d’une console personnalisée pour Azure PowerShell. Vous pouvez exécuter les cmdlets depuis la console Windows PowerShell standard ou depuis la console Azure PowerShell.

La méthode utilisée pour l’ouverture d’une console ou de l’autre dépend de votre version de Windows :

- Sur un ordinateur exécutant au moins Windows 8 ou Windows Server 2012, vous pouvez utiliser l’outil de recherche intégré. Dans l’écran d’accueil, commencez à taper **power**. Ceci entraîne l’affichage d’une liste d’applications, notamment Windows PowerShell et Azure PowerShell. Cliquez sur une application pour ouvrir la console. (Pour épingler l’application à l’écran d’accueil, cliquez avec le bouton droit sur l’icône.)

- Sur un ordinateur exécutant une version antérieure à Windows 8 ou Windows Server 2012, utilisez le menu Démarrer. Dans le menu Démarrer, cliquez sur **Tous les programmes**, sur **Azure**, puis sur **Azure PowerShell**.

<a id="Connect"></a>
## Connexion à votre abonnement

L’utilisation d’Azure requiert un abonnement. Si vous n’êtes pas abonné, consultez la page [Prise en main d’Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

Les cmdlets requièrent vos informations d’abonnement pour pouvoir gérer vos services. Il existe plusieurs façons de fournir vos informations d’abonnement à Windows PowerShell. Vous pouvez utiliser un certificat de gestion contenant ces informations, ou vous connecter à Azure en utilisant votre compte Microsoft, professionnel ou scolaire. Lorsque vous vous connectez, Azure Active Directory (Azure AD) authentifie les informations d’identification et renvoie un jeton d’accès qui autorise Azure PowerShell à gérer votre compte.

Pour vous aider à choisir la méthode d'authentification adaptée à vos besoins, tenez compte des informations suivantes :

- La méthode Azure AD est la méthode d’authentification recommandée, car elle peut faciliter la gestion de l’accès à un abonnement. La mise à jour de la version 0.8.6 permet également l’automatisation de l’authentification Azure AD si vous utilisez un compte professionnel ou scolaire. Cette automatisation fonctionne également avec l’API Azure Resource Manager.
- Avec la méthode par certificat, les informations d’abonnement sont disponibles tant que l’abonnement et le certificat sont valides. Cependant, cette méthode complique la gestion de l’accès à un abonnement partagé (lorsque plusieurs personnes peuvent accéder au compte). Notez que l'API Azure Resource Manager n'accepte pas l'authentification par certificat.

Pour plus d’informations sur la gestion de l’authentification et de l’abonnement dans Azure, consultez [Gérer des comptes, des abonnements et des rôles d’administrateur](http://go.microsoft.com/fwlink/?LinkId=324796).

### Utilisation de la méthode Azure AD

1. Ouvrez la console Azure PowerShell, comme indiqué dans la section [Installation d’Azure PowerShell](#Install).

2. Tapez la commande suivante :

		Add-AzureAccount

3. Dans la fenêtre, tapez l’adresse de messagerie et le mot de passe associés à votre compte.

4. Azure authentifie et enregistre les informations d’identification, puis ferme la fenêtre.

5. Si vous utilisez un compte professionnel ou scolaire et la version 0.8.6 d’Azure AD ou ultérieure, vous pouvez saisir la commande suivante pour ignorer la fenêtre contextuelle. La fenêtre standard des informations d’identification de Windows PowerShell s’affichera ; vous devrez y saisir le nom d’utilisateur et le mot de passe correspondant à votre compte professionnel ou scolaire.

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

	> [AZURE.NOTE]Pour plus d’informations sur la sécurité et sur l’utilisation des informations d’identification, voir [Meilleures pratiques pour le déploiement de mots de passe et autres données sensibles dans ASP.NET et les sites Web Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) (en anglais).

	> [AZURE.NOTE]Cette méthode de connexion non interactive fonctionne uniquement avec un compte professionnel ou scolaire. Un compte professionnel ou scolaire représente un utilisateur géré par son travail ou son école et défini dans l’instance d’Azure Active Directory pour son travail ou son école. Si vous ne possédez pas de compte professionnel ou scolaire et que vous utilisez un compte Microsoft pour vous connecter à votre abonnement Azure, vous pouvez en créer un facilement en procédant comme suit.
	>
	> 1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com) et cliquez sur **Active Directory**.
	>
	> 2. S'il n'existe aucun annuaire, sélectionnez **Create your directory** et fournissez les informations demandées.
	>
	> 3. Sélectionnez votre annuaire et ajoutez un nouvel utilisateur. Ce nouvel utilisateur peut se connecter à l’aide d’un compte professionnel ou scolaire.
	>
	>     Pendant la création de l’utilisateur, une adresse de messagerie est fournie pour l’utilisateur, ainsi qu’un mot de passe temporaire. Conservez ces informations, car elles vous serviront à une étape ultérieure.
	>
	> 4. Dans le portail de gestion, sélectionnez **Paramètres**, puis **Administrateurs**. Sélectionnez **Ajouter** et ajoutez le nouvel utilisateur en tant que co-administrateur. Cela permet au compte professionnel ou scolaire de gérer votre abonnement Azure.
	>
	> 5. Pour finir, déconnectez-vous du portail Azure et reconnectez-vous en utilisant le compte professionnel ou scolaire. Si vous vous connectez pour la première fois avec ce compte, vous êtes invité à changer le mot de passe.
	>
	>Pour plus d’informations sur l’inscription à Microsoft Azure avec un compte professionnel ou scolaire, voir [Inscription à Azure en tant qu’organisation](sign-up-organization.md).

### Utilisation de la méthode par certificat

Le module Azure inclut des cmdlets qui vous aident à télécharger et à importer le certificat.

> [AZURE.NOTE]Les cmdlets du module Azure Resource Manager nécessitent l'utilisation de la méthode Azure AD (Add-AzureAccount). Ces cmdlets ne prennent pas en charge les fichiers de paramètres de publication. Pour plus d'informations sur les cmdlets du module Azure Resource Manager, consultez la page [Cmdlets du module Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkID=394765).


- La cmdlet **Get-AzurePublishSettingsFile** ouvre une page Web vers le portail de gestion Azure, d’où vous pouvez télécharger les informations d’abonnement. Ces dernières sont contenues dans un fichier .publishsettings.

- **Import-AzurePublishSettingsFile** importe le fichier .publishsettings que le module utilise. Ce fichier inclut un certificat de gestion doté d’informations d’identification de sécurité.

> [AZURE.IMPORTANT]Nous vous recommandons de supprimer le profil de publication téléchargé à l’aide de <b>Get-AzurePublishSettingsFile</b> après l’importation de ces paramètres. Comme le certificat de gestion inclut des informations d’identification sécurisées, seuls les utilisateurs autorisés doivent y accéder. Si vous avez besoin d’informations sur vos abonnements, consultez le [portail de gestion Azure](http://manage.windowsazure.com/) ou le [portail client de Microsoft Online Services](http://go.microsoft.com/fwlink/p/?LinkId=324875).

1. Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com) en utilisant les informations d’identification de votre compte Azure.

2. Ouvrez la console Azure PowerShell, comme indiqué dans la section [Installation d’Azure PowerShell](#Install).

3. Tapez la commande suivante :

		Get-AzurePublishSettingsFile

4. À l’invite, téléchargez et enregistrez le profil de publication et notez le chemin d’accès et le nom du fichier .publishsettings. Ces informations sont requises lorsque vous exécutez la cmdlet **Import-AzurePublishSettingsFile** pour importer les paramètres. L’emplacement par défaut et le format de nom de fichier sont les suivants :

			C:\Users<UserProfile>\Download\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings

5. Tapez une commande semblable à celle qui suit, en remplaçant les espaces réservés par le nom de votre compte Windows, le chemin d’accès et le nom de fichier :

		Import-AzurePublishSettingsFile C:\Users<UserProfile>\Downloads<SubscriptionName>-credentials.publishsettings

> [AZURE.NOTE]Si vous êtes ajouté à d’autres abonnements en tant que coadministrateur après l’importation de vos paramètres de publication, vous devrez répéter cette procédure pour télécharger un nouveau fichier de paramètres de publication .publishsettings, puis pour importer ces paramètres. Pour plus d’informations sur l’ajout de coadministrateurs afin de faciliter la gestion des services pour un abonnement, consultez la page [Ajout et suppression de coadministrateurs pour vos abonnements Azure](http://msdn.microsoft.com/library/windowsazure/gg456328.aspx).

### Afficher les informations détaillées du compte et de l’abonnement

Vous pouvez utiliser plusieurs comptes et abonnements avec Azure PowerShell. Vous pouvez ajouter plusieurs comptes en exécutant Add-AzureAccount à plusieurs reprises.

Pour afficher les comptes Azure disponibles, tapez :

	Get-AzureAccount

Pour afficher vos abonnements Azure, tapez :

	Get-AzureSubscription

## <a id="Ex"></a>Exemple d’utilisation des cmdlets ##

Après l’installation du module et la configuration de votre ordinateur pour vous connecter à votre abonnement, vous pouvez créer une application web Azure. Cet exemple vous aidera à utiliser pour la première fois les cmdlets Azure.

1. Démarrez la console Azure PowerShell.

2. Choisissez un nom pour votre application web. Le format de ce nom doit respecter les conventions d’attribution de nom DNS. Il doit uniquement contenir des lettres allant de « a » à « z », des chiffres allant de « 0 » à « 9 » et un tiret « - ».

	Le nom de l’application web doit être unique dans Azure. Dans cet exemple, nous utiliserons le nom « MonSite », mais veillez à choisir un nom différent, comme le nom de votre compte suivi d’un chiffre.

	Une fois le nom choisi, tapez une commande semblable à celle qui suit. Remplacez « MonSite » par le nom de votre application web.

		New-AzureWebsite mySite

	La cmdlet crée l’application web et renvoie un objet qui représente la nouvelle application web. Les propriétés de l’objet comprennent des informations utiles concernant l’application web.

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

Consultez les ressources suivantes pour obtenir de l’aide sur des cmdlets spécifiques :


-   Dans la console, vous pouvez utiliser le système d’aide intégré, accessible par la commande **Get-Help**. Le tableau suivant fournit des exemples de commandes permettant d’accéder à l’aide. Vous pouvez obtenir plus d’informations dans la console en tapant **help**.

    <table border="1" cellspacing="4" cellpadding="4">
<tbody>
<tr align="left" valign="top">
	<td><b>Commande</b></td>
	<td><b>Résultat</b></td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help</td>
	<td>Explique comment utiliser le système d’aide. <p><b>Remarque</b>&#160;: la description inclut certaines informations sur des fichiers d’aide qui ne s’appliquent pas au module Azure. Plus précisément, les fichiers d’aide sont installés en même temps que le module. Vous ne pouvez pas les télécharger séparément.</p>
</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help Azure</td>
	<td>Obtient toutes les cmdlets du module Azure.</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>language</b>>-dev</td>
	<td>Obtient des cmdlets pour développer et gérer des applications dans un langage spécifique, comme help node-dev, help php-dev ou help python-dev.</td>
</tr>
    <tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>></td>
	<td>Obtient de l'aide concernant une cmdlet Windows PowerShell. Remplacez <cmdlet> par le nom de la cmdlet.</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>> -Parameter *</td>
	<td>Obtient la description des paramètres de la cmdlet. L'astérisque (*) signifie «&#160;tous&#160;».</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>> -Examples</td>
	<td>Obtient la syntaxe et des exemples d'utilisation de la cmdlet.</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>> -Full</td>
	<td>Obtient toute l'aide relative à une cmdlet, y compris les informations techniques.</td>
</tr>
</tbody>
</table>



- Vous pouvez également obtenir des informations de référence sur les cmdlets des modules Azure PowerShell dans la bibliothèque Azure. Pour plus d’informations, consultez la page [Référence des cmdlets Azure](http://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Pour obtenir de l’aide de la communauté, essayez ces forums populaires :

- [Forum Azure sur MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212) (en anglais)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)


## <a id="Resources"></a>Ressources supplémentaires ##

Voici certaines des ressources disponibles pour apprendre à utiliser Azure et Windows PowerShell.

- Pour plus d’informations sur l’accès aux composants d’Azure Storage, voir [Utilisation d’Azure PowerShell avec Azure Storage](storage-powershell-guide-full.md).

- Pour nous envoyer des commentaires sur les cmdlets, signaler des problèmes ou accéder au code source, consultez le [référentiel du code Azure PowerShell](https://github.com/WindowsAzure/azure-sdk-tools).

- Pour en savoir plus sur l’environnement de script et en ligne de commande Windows PowerShell, consultez le [Centre de scripts TechNet](http://go.microsoft.com/fwlink/p/?LinkId=320211).

- Pour plus d’informations sur l’installation, la formation, l’utilisation et la personnalisation de Windows PowerShell, consultez la page [Création de scripts avec Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320210).

- Pour plus d’informations sur les scripts et leur exécution dans Windows PowerShell, consultez la page [Exécution de scripts](http://go.microsoft.com/fwlink/p/?LinkId=320627). Cet article inclut des informations de base sur la création de scripts et la configuration de votre ordinateur pour les exécuter.

- Pour plus d’informations sur les cmdlets pour Azure AD, consultez la page [Gestion d’Azure AD à l’aide de Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320628).





  [Microsoft Online Services Customer Portal]: https://mocp.microsoftonline.com/site/default.aspx

<!---HONumber=July15_HO4-->