<properties 
	pageTitle="Installation et configuration d’Azure PowerShell" 
	description="Découvrez comment installer et configurer Azure PowerShell." 
	editor="tysonn" 
	manager="stevenka" 
	documentationCenter="" 
	services="" 
	authors="coreyp69"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/26/2014" 
	ms.author="coreyp"/>

# Installation et configuration d’Azure PowerShell

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/fr-fr/manage/install-and-configure-cli/" title="Interface de ligne de commande interplateforme">Interface de ligne de commande interplateforme</a></div>

Windows PowerShell permet d’effectuer de nombreuses tâches dans Azure, de façon interactive (via l’invite de commandes) ou automatique (via des scripts). Azure PowerShell est un module fournissant des cmdlets pour gérer Azure via Windows PowerShell. Ces cmdlets permettent de créer, de tester, de déployer et de gérer des solutions et des services fournis via la plateforme Azure. Dans la plupart des cas, les cmdlets permettent d’effectuer les mêmes tâches que celles du portail de gestion Azure. Par exemple, vous pouvez créer et configurer des services cloud, des machines virtuelles, des réseaux virtuels et des sites Web.

Le module est distribué en tant que fichier téléchargeable et le code source est géré via un référentiel public. Un lien vers le fichier téléchargeable est fourni dans les instructions d’installation figurant plus loin dans cette rubrique. Pour plus d’informations sur le code source, consultez la page [Référentiel du code Azure PowerShell][Référentiel du code Azure PowerShell].

Ce guide fournit des informations de base sur l’installation et la configuration de Windows Azure PowerShell pour gérer la plateforme Azure.

## Sommaire

-   [Conditions préalables à l’utilisation d’Azure PowerShell][Conditions préalables à l’utilisation d’Azure PowerShell]
-   [Installation d'Azure PowerShell][Installation d'Azure PowerShell]
-   [Connexion à votre abonnement][Connexion à votre abonnement]
-   [Utilisation des cmdlets : exemple][Utilisation des cmdlets : exemple]
-   [Accès à l’aide][Accès à l’aide]
-   [Ressources supplémentaires][Ressources supplémentaires]

### <span id="Prereq"></span></a>Conditions préalables à l’utilisation d’Azure PowerShell

Azure est une plateforme disponible par abonnement. Cela signifie qu’un abonnement est requis pour l’utiliser. Dans la plupart des cas, cela signifie également que les cmdlets requièrent des informations d’abonnement pour effectuer les tâches associées. (Il est possible d’utiliser certaines cmdlets relatives au stockage sans ces informations.) Vous pouvez fournir ces informations en configurant votre ordinateur pour vous connecter avec votre abonnement. Vous trouverez des instructions à ce sujet dans la section Connexion à votre abonnement.

> [WACOM.NOTE] Avec la version 0.8.5, les modules Azure PowerShell nécessitent Microsoft .NET Framework 4.5.

Lors de l’installation du module, le programme d’installation recherche les logiciels requis sur votre système et installe toutes les dépendances, comme les versions adéquates de Windows PowerShell et de .NET Framework.

## <span id="Install"></span></a> Installation d'Azure PowerShell

Vous pouvez télécharger et installer les modules Azure PowerShell en exécutant [Microsoft Web Platform Installer][Microsoft Web Platform Installer]. À l’invite, cliquez sur **Exécuter**. Web Platform Installer installe les modules Azure PowerShell et toutes les dépendances. Suivez les instructions de l’invite pour terminer l’installation.

Pour plus d’informations sur les outils en ligne de commande disponibles pour Azure, consultez la page [Outils en ligne de commande][Outils en ligne de commande].

L’installation du module entraîne celle d’une console personnalisée pour Azure PowerShell. Vous pouvez exécuter les cmdlets depuis la console Windows PowerShell standard ou depuis la console Azure PowerShell.

La méthode utilisée pour l’ouverture d’une console ou de l’autre dépend de votre version de Windows :

-   Sur un ordinateur exécutant au moins Windows 8 ou Windows Server 2012, vous pouvez utiliser l’outil de recherche intégré. Dans l’écran d’accueil, commencez à taper **power**. Ceci entraîne l’affichage d’une liste d’applications, notamment Windows PowerShell et Azure PowerShell. Cliquez sur une application pour ouvrir la console. (Pour épingler l’application à l’écran d’accueil, cliquez avec le bouton droit sur l’icône.)

-   Sur un ordinateur exécutant une version antérieure à Windows 8 ou Windows Server 2012, utilisez le menu Démarrer. Dans le menu Démarrer, cliquez sur **Tous les programmes**, sur **Azure**, puis sur **Azure PowerShell**.

## <span id="Connect"></span></a> Connexion à votre abonnement

L’utilisation d’Azure requiert un abonnement. Si vous n’êtes pas abonné, consultez la page [Prise en main d’Azure][Prise en main d’Azure].

Les cmdlets requièrent vos informations d’abonnement pour pouvoir gérer vos services. Il existe plusieurs façons de fournir vos informations d'abonnement à Windows PowerShell. Vous pouvez utiliser un certificat de gestion contenant ces informations, ou vous connecter à Azure en utilisant votre compte Microsoft ou un ID d’organisation. Lorsque vous vous connectez, Azure Active Directory (Azure AD) authentifie les informations d'identification et renvoie un jeton d'accès qui autorise Azure PowerShell à gérer votre compte.

Pour vous aider à choisir la méthode d'authentification adaptée à vos besoins, tenez compte des informations suivantes :

-   La méthode Azure AD est la méthode d'authentification recommandée, car elle peut faciliter la gestion de l’accès à un abonnement. La mise à jour de la version 0.8.6 permet également l'automatisation de l'authentification Azure AD si vous utilisez un compte professionnel. Cette automatisation fonctionne également avec l'API Azure Resource Manager.
-   Avec la méthode par certificat, les informations d’abonnement sont disponibles tant que l’abonnement et le certificat sont valides. Cependant, cette méthode complique la gestion de l’accès à un abonnement partagé (lorsque plusieurs personnes peuvent accéder au compte). Notez que l'API Azure Resource Manager n'accepte pas l'authentification par certificat.

Pour plus d’informations sur la gestion de l’authentification et de l’abonnement dans Azure, consultez [Gérer des comptes, des abonnements et des rôles d’administrateur][Gérer des comptes, des abonnements et des rôles d’administrateur].

### Utilisation de la méthode Azure AD

1.  Ouvrez la console Azure PowerShell, comme indiqué dans la rubrique [Installation d'Azure PowerShell][Installation d'Azure PowerShell].

2.  Tapez la commande suivante :

    `Add-AzureAccount`

3.  Dans la fenêtre, tapez l’adresse de messagerie et le mot de passe associés à votre compte.

4.  Azure authentifie et enregistre les informations d’identification, puis ferme la fenêtre.

5.  Si vous utilisez un compte professionnel et la version 0.8.6 d'Azure AD, vous pouvez saisir la commande suivante pour ignorer la fenêtre contextuelle. La fenêtre standard des informations d'identification de Windows PowerShell s'affichera, sur laquelle vous devrez saisir le nom d'utilisateur et le mot de passe correspondant à votre compte professionnel.

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

6.  Si vous utilisez cette méthode dans un script d'automatisation et que vous souhaitez qu'aucune fenêtre contextuelle ne s'affiche, utilisez l'extrait suivant :

        $userName = "<your organizational account user name>"
        $securePassword = ConvertTo-SecureString -String "<your organizational account password>" -AsPlainText -Force
        $cred = New-Object System.Management.Automation.PSCredential($userName, $securePassword)
        Add-AzureAccount -Credential $cred 

    > [WACOM.NOTE] Cette méthode de connexion non interactive fonctionne uniquement avec un compte professionnel. Il s'agit d'un utilisateur géré par votre organisation et défini dans le client Azure Active Directory de vos organisations. Si vous ne possédez pas de compte professionnel et que vous utilisez un compte Microsoft pour vous connecter à votre abonnement Azure, vous pouvez en créer un facilement en procédant comme suit.
    >
    > 1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure] et cliquez sur **Active Directory**.
    >
    > 2.  S'il n'existe aucun annuaire, sélectionnez **Create your directory** et fournissez les informations demandées.
    >
    > 3.  Sélectionnez votre annuaire et ajoutez un nouvel utilisateur. Il s'agit d'un compte professionnel.
    >
    >     Pendant la création de l'utilisateur, une adresse de messagerie est fournie pour l'utilisateur, ainsi qu'un mot de passe temporaire. Conservez ces informations, car elles vous serviront à une étape ultérieure.
    >
    > 4.  Dans le portail de gestion, sélectionnez **Paramètres**, puis **Administrateurs**. Sélectionnez **Ajouter** et ajoutez le nouvel utilisateur en tant que co-administrateur. Cela permet au compte professionnel de gérer votre abonnement Azure.
    >
    > 5.  Pour finir, déconnectez-vous du portail Azure et reconnectez-vous en utilisant le nouveau compte professionnel. Si vous vous connectez pour la première fois avec ce compte, vous êtes invité à changer le mot de passe.
    >
    > Pour plus d'informations sur les comptes professionnels avec Microsoft Azure, consultez la page [Inscription à Microsoft Azure en tant qu'organisation][Inscription à Microsoft Azure en tant qu'organisation].

### Utilisation de la méthode par certificat

Le module Azure inclut des cmdlets qui vous aident à télécharger et à importer le certificat.

-   La cmdlet **Get-AzurePublishSettingsFile** ouvre une page Web vers le
    portail de gestion Azure, d’où vous pouvez
    télécharger les informations d’abonnement. Ces dernières sont contenues dans un fichier .publishsettings.

-   **Import-AzurePublishSettingsFile** importe le fichier .publishsettings que le module utilise. Ce fichier inclut un certificat de gestion doté d’informations d’identification de sécurité.

<div class="dev-callout"> 
<b>Remarque</b>
<p>Les cmdlets du module Azure Resource Manager n&eacute;cessitent l'utilisation de la m&eacute;thode Azure AD (Add-AzureAccount). Ces cmdlets ne prennent pas en charge les fichiers de param&egrave;tres de publication. Pour plus d'informations sur les cmdlets du module Azure Resource Manager, consultez la page <a href="http://go.microsoft.com/fwlink/?LinkID=394765">Cmdlets du module Azure Resource Manager</a>.</p> 
</div>

<div class="dev-callout"> 
<b>Important</b> 
<p>Il est recommand&eacute; de supprimer le profil de publication que vous
avez t&eacute;l&eacute;charg&eacute; &agrave; l'aide de <b>Get-AzurePublishSettingsFile</b> apr&egrave;s avoir import&eacute; ces
param&egrave;tres. Comme le certificat de gestion inclut des informations d&rsquo;identification s&eacute;curis&eacute;es,
seuls les utilisateurs autoris&eacute;s doivent y acc&eacute;der. Si vous avez besoin d'informations
sur vos abonnements, consultez le <a href="http://manage.windowsazure.com/">portail de gestion Azure</a> ou le <a href="http://go.microsoft.com/fwlink/p/?LinkId=324875">portail client de Microsoft Online Services</a>.</p> 
</div>

1.  Connectez-vous au [portail de gestion Azure][2] en utilisant les informations d’identification de votre compte Azure.

2.  Ouvrez la console Azure PowerShell, comme indiqué dans la rubrique [Installation d'Azure PowerShell][Installation d'Azure PowerShell].

3.  Tapez la commande suivante :

    `Get-AzurePublishSettingsFile`

4.  À l’invite, téléchargez et enregistrez le profil de publication et notez le chemin d’accès et le nom du
    fichier .publishsettings. Ces informations sont requises lorsque vous exécutez la cmdlet
    **Import-AzurePublishSettingsFile** pour importer les paramètres. L’emplacement
    par défaut et le format de nom de fichier sont les suivants :

    `C:\UsersC:\\Users\<UserProfile>\\Download\\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings`lt;UserProfile\>\\Download\\[\*MySubscription\*-...]-\*downloadDate\*-credentials.publishsettings</code>

5.  Tapez une commande semblable à celle qui suit, en remplaçant les espaces réservés par le nom de votre compte Windows, le chemin d’accès et le nom de fichier :

    `Import-AzurePublishSettingsFile C:\UsersImport-AzurePublishSettingsFile C:\Users\<UserProfile>\Downloads\<SubscriptionName>-credentials.publishsettings`lt;UserProfile\>\\Downloads`Import-AzurePublishSettingsFile C:\Users\<UserProfile>\Downloads\<SubscriptionName>-credentials.publishsettings`lt;SubscriptionName\>-credentials.publishsettings</code>

> [WACOM.NOTE] Si vous êtes ajouté à d’autres abonnements en tant que coadministrateur après l’importation de vos paramètres de publication, vous devrez répéter cette procédure pour télécharger un nouveau fichier .publishsettings, puis pour importer ces paramètres. Pour plus d’informations sur l’ajout de coadministrateurs afin de faciliter la gestion des services pour un abonnement, consultez la page [Ajout et suppression de coadministrateurs pour vos abonnements Azure][Ajout et suppression de coadministrateurs pour vos abonnements Azure].

### Afficher les informations détaillées du compte et de l’abonnement

Vous pouvez utiliser plusieurs comptes et abonnements avec Azure PowerShell. Vous pouvez ajouter plusieurs comptes en exécutant Add-AzureAccount à plusieurs reprises.

Pour afficher les comptes Azure disponibles, tapez :

    Get-AzureAccount

Pour afficher vos abonnements Azure, tapez :

    Get-AzureSubscription

## <span id="Ex"></span></a>Utilisation des cmdlets : exemple

Après l’installation du module et la configuration de votre ordinateur pour vous connecter à votre abonnement, vous pouvez créer un site Web Azure. Cet exemple vous aidera à utiliser pour la première fois les cmdlets Azure.

1.  Démarrez la console Azure PowerShell.

2.  Choisissez un nom pour votre site Web. Le format de ce nom doit respecter les conventions d’attribution de nom DNS. Il doit uniquement contenir des lettres allant de « a » à « z », des chiffres allant de « 0 » à « 9 » et un tiret « - ».

    Le nom du site Web doit être unique dans Azure. Dans cet exemple, nous utiliserons le nom « MonSite », mais veillez à choisir un nom différent, comme le nom de votre compte suivi d'un chiffre.

    Une fois le nom choisi, tapez une commande semblable à celle qui suit. Remplacez « MonSite » par le nom de votre site Web.

    `New-AzureWebsite mySite`

    La cmdlet crée le site Web et renvoie un objet qui représente le nouveau site Web. Les propriétés de l'objet comprennent des informations utiles concernant le site Web.

3.  Pour obtenir des informations sur le site Web, tapez cette commande. Vous obtiendrez alors des informations sur tous les sites Web de votre abonnement, y compris sur celui que vous venez de créer.

    `Get-AzureWebsite`

4.  Pour obtenir plus d'informations sur votre site Web, ajoutez le nom du site Web dans la commande. Veillez à remplacer « MonSite » par le nom de votre site Web.

    `Get-AzureWebsite -Name mySite`

5.  Les sites Web sont démarrés après leur création. Pour arrêter le site Web, tapez cette commande en y ajoutant le nom de votre site Web.

    `Stop-AzureWebsite -Name mySite`

6.  Pour vérifier que l'état du site est « arrêté », exécutez de nouveau la commande Get-AzureWebsite.

    `Get-AzureWebsite`

7.  Pour terminer ce test, vous pouvez supprimer le site Web. Type :

    `Remove-AzureWebsite -Name mySite`

8.  Pour terminer cette tâche, vous pouvez confirmer que le site Web est supprimé.

    `Get-AzureWebsite -Name mySite`

## <span id="Help"></span></a>Accès à l’aide

Consultez les ressources suivantes pour obtenir de l’aide sur des cmdlets spécifiques :

-   Dans la console, vous pouvez utiliser le système d’aide intégré, accessible par la commande **Get-Help**. Le tableau suivant fournit des exemples de commandes permettant d’accéder à l’aide. Vous pouvez obtenir plus d’informations dans la console en tapant **help**.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Commande</strong></td>
    <td align="left"><strong>Résultat</strong></td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help</td>
    <td align="left">Explique comment utiliser le système d’aide.
    <p><strong>Remarque</strong> : La description inclut des informations sur des fichiers d’aide qui ne s’appliquent pas au module Azure. Plus précisément, les fichiers d’aide sont installés en même temps que le module. Vous ne pouvez pas les télécharger séparément.</p></td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help Azure</td>
    <td align="left">Obtient toutes les cmdlets du module Azure.</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>language</strong>&gt;-dev</td>
    <td align="left">Obtient des cmdlets pour développer et gérer des applications dans un langage spécifique, comme help node-dev, help php-dev ou help python-dev.</td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt;</td>
    <td align="left">Obtient de l'aide concernant une cmdlet Windows PowerShell. Remplacez <cmdlet> par le nom de la cmdlet.</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Parameter *</td>
    <td align="left">Obtient la description des paramètres de la cmdlet. L'astérisque (*) signifie « tous ».</td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Examples</td>
    <td align="left">Obtient la syntaxe et des exemples d'utilisation de la cmdlet.</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Full</td>
    <td align="left">Obtient toute l'aide relative à une cmdlet, y compris les informations techniques.</td>
    </tr>
    </tbody>
    </table>

-   Vous pouvez également obtenir des informations de référence sur les cmdlets des modules Azure PowerShell dans la bibliothèque Azure. Pour plus d’informations, consultez la page [Référence des cmdlets Azure][Référence des cmdlets Azure].

Pour obtenir de l’aide de la communauté, essayez ces forums populaires :

-   [Forum Azure sur MSDN][Forum Azure sur MSDN]
-   [Stackoverflow][Stackoverflow]

## <span id="Resources"></span></a>Ressources supplémentaires

Voici certaines des ressources disponibles pour apprendre à utiliser Azure et Windows PowerShell.

-   Pour nous envoyer des commentaires sur les cmdlets, signaler des problèmes ou accéder au code source, consultez le [référentiel du code Azure PowerShell][Référentiel du code Azure PowerShell].

-   Pour en savoir plus sur l’environnement de script et en ligne de commande Windows PowerShell, consultez le [Centre de scripts TechNet][Centre de scripts TechNet].

-   Pour plus d’informations sur l’installation, la formation, l’utilisation et la personnalisation de Windows PowerShell, consultez la page [Création de scripts avec Windows PowerShell][Création de scripts avec Windows PowerShell].

-   Pour plus d’informations sur les scripts et leur exécution dans Windows PowerShell, consultez la page [Exécution de scripts][Exécution de scripts]. Cet article inclut des informations de base sur la création de scripts et la configuration de votre ordinateur pour les exécuter.

-   Pour plus d’informations sur les cmdlets pour Azure AD, consultez la page [Gestion d’Azure AD à l’aide de Windows PowerShell][Gestion d’Azure AD à l’aide de Windows PowerShell].

  [Référentiel du code Azure PowerShell]: https://github.com/WindowsAzure/azure-sdk-tools
  [Conditions préalables à l’utilisation d’Azure PowerShell]: #Prereq
  [Installation d'Azure PowerShell]: #Install
  [Connexion à votre abonnement]: #Connect
  [Utilisation des cmdlets : exemple]: #Ex
  [Accès à l’aide]: #Help
  [Ressources supplémentaires]: #Resources
  [Microsoft Web Platform Installer]: http://go.microsoft.com/fwlink/p/?LinkId=320376
  [Outils en ligne de commande]: http://go.microsoft.com/fwlink/?LinkId=320796
  [Prise en main d’Azure]: http://go.microsoft.com/fwlink/p/?LinkId=320795
  [Gérer des comptes, des abonnements et des rôles d’administrateur]: http://go.microsoft.com/fwlink/?LinkId=324796
  [portail de gestion Azure]: https://manage.windowsazure.com
  [Inscription à Microsoft Azure en tant qu'organisation]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
  [2]: http://manage.windowsazure.com
  [Ajout et suppression de coadministrateurs pour vos abonnements Azure]: http://msdn.microsoft.com/library/windowsazure/gg456328.aspx
  [Référence des cmdlets Azure]: http://msdn.microsoft.com/library/windowsazure/jj554330.aspx
  [Forum Azure sur MSDN]: http://go.microsoft.com/fwlink/p/?LinkId=320212
  [Stackoverflow]: http://go.microsoft.com/fwlink/?LinkId=320213
  [Centre de scripts TechNet]: http://go.microsoft.com/fwlink/p/?LinkId=320211
  [Création de scripts avec Windows PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320210
  [Exécution de scripts]: http://go.microsoft.com/fwlink/p/?LinkId=320627
  [Gestion d’Azure AD à l’aide de Windows PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320628

<!--HONumber=46--> 
