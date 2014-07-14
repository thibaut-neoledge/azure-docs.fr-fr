<properties  linkid="Install-Config-Windows-Azure-PowerShell" urlDisplayName="Azure PowerShell" pageTitle="How to install and configure Azure PowerShell" description="Learn how to install and configure Azure PowerShell." umbracoNaviHide="0" disqusComments="1" editor="tysonn" manager="jeffreyg" documentationCenter="" services="" solutions="" authors="kathydav" title="How to install and configure Azure PowerShell" />

# Installation et configuration d'Azure PowerShell

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/en-us/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/en-us/manage/install-and-configure-cli/" title="CLI interplateforme">CLI interplateforme</a></div>

 Windows PowerShell permet d'effectuer de nombreuses tâches dans Azure,
de façon interactive (via l'invite de commandes) ou automatique (via des scripts). Azure PowerShell est un module fournissant des cmdlets pour gérer Azure via Windows PowerShell. Ces cmdlets permettent de créer, de tester, de déployer et de gérer des solutions et des services fournis via la plateforme Azure. Dans la plupart des cas, les cmdlets permettent d'effectuer les mêmes tâches que celles du portail de gestion Azure. Par exemple, vous pouvez créer et configurer des services cloud, des machines virtuelles, des réseaux virtuels et des sites Web.

Le module est distribué en tant que fichier téléchargeable et le code source est géré via un référentiel public. Un lien vers le fichier téléchargeable est fourni dans les instructions d'installation figurant plus loin dans cette rubrique. Pour plus d'informations sur le code source, consultez la page [Référentiel du code Azure PowerShell][1].

Ce guide fournit des informations de base sur l'installation et la configuration de Windows Azure PowerShell pour gérer la plateforme Azure.

## Sommaire

* [Conditions préalables à l'utilisation d'Azure PowerShell](#Prereq)
* [Installation d'Azure PowerShell](#Install)
* [Connexion à votre abonnement](#Connect)
* [Exemple d'utilisation des cmdlets](#Ex)
* [Accès à l'aide](#Help)
* [Ressources supplémentaires](#Resources)

### <a id="Prereq" ></a>Conditions préalables à l'utilisation d'Azure PowerShell

Azure est une plateforme disponible par abonnement. Cela signifie qu'un abonnement est requis pour l'utiliser. Dans la plupart des cas, cela signifie également que les cmdlets requièrent des informations d'abonnement pour effectuer les tâches associées. (Il est possible d'utiliser certaines cmdlets relatives au stockage sans ces informations.) Vous pouvez fournir ces informations en configurant votre ordinateur pour vous connecter avec votre abonnement. Vous trouverez des instructions à ce sujet dans la section Connexion à votre abonnement.

> [WACOM.NOTE] Il existe de nombreuses options d'abonnement. Pour plus d'informations, consultez la page [Prise en main d'Azure][2].

Lors de l'installation du module, le programme d'installation recherche les logiciels requis sur votre système et installe toutes les dépendances, comme les versions adéquates de Windows PowerShell et de .NET Framework.

<h2> <a id="Install" ></a>Installation d'Azure PowerShell</h2>


Vous pouvez télécharger et installer le module Azure PowerShell en exécutant [Microsoft Web Platform Installer][3]. À l'invite, cliquez sur **Exécuter**. Microsoft Web Platform Installer se charge et le module **Azure PowerShell** peut être installé. Web Platform Installer installe toutes les dépendances pour les cmdlets Azure PowerShell. Suivez les instructions de l'invite pour terminer l'installation.

Pour plus d'informations sur les outils en ligne de commande disponibles pour Azure, consultez la page [Outils en ligne de commande][4].

L'installation du module entraîne celle d'une console personnalisée pour Azure PowerShell. Vous pouvez exécuter les cmdlets depuis la console Windows PowerShell standard ou depuis la console Azure PowerShell.

La méthode utilisée pour l'ouverture d'une console ou de l'autre dépend de votre version de Windows :

* Sur un ordinateur exécutant au moins Windows 8 ou Windows Server 2012, vous pouvez utiliser l'outil de recherche intégré. Dans l'écran d'accueil, commencez à taper **power**. Ceci entraîne l'affichage d'une liste d'applications, notamment Windows PowerShell et Azure PowerShell. Cliquez sur une application pour ouvrir la fenêtre de la console. (Pour épingler l'application à l'écran d'accueil, cliquez avec le bouton droit sur l'icône.)


* Sur un ordinateur exécutant une version antérieure à Windows 8 ou Windows Server 2012, vous pouvez utiliser le menu Démarrer. Dans le menu Démarrer, cliquez sur **Tous les programmes**, sur **Azure**, puis sur **Azure PowerShell**.

<h2><a id="Connect" ></a>Connexion à votre abonnement</h2>


L'utilisation d'Azure requiert un abonnement. Si vous n'êtes pas abonné, consultez la page [Prise en main d'Azure][2].

Les cmdlets requièrent vos informations d'abonnement pour pouvoir gérer vos services. Dans la version .0.7 de ce module, il existe deux méthodes pour fournir ces informations. Vous pouvez télécharger et utiliser un certificat de gestion contenant les informations, ou vous connecter à Azure en utilisant votre compte Microsoft ou un ID d'organisation. Lors de votre connexion, Azure Active Directory (Azure AD) authentifie les informations d'identification.

Pour vous aider à choisir la méthode d'authentification adaptée à vos besoins, tenez compte des informations suivantes :

* La méthode Azure AD peut faciliter la gestion de l'accès à un abonnement, mais peut interrompre l'automatisation. Les informations d'identification sont disponibles pour Azure PowerShell durant
  12 heures. Après leur expiration, vous devrez vous reconnecter.
* Avec la méthode par certificat, les informations d'abonnement sont disponibles tant que l'abonnement et le certificat sont valides. Cette méthode facilite l'automatisation des tâches de longue durée. Une fois les informations téléchargées et importées, vous n'aurez pas besoin de les fournir de nouveau. Cependant, cette méthode complique la gestion de l'accès à un abonnement partagé (lorsque plusieurs personnes peuvent accéder au compte).

Pour plus d'informations sur la gestion de l'authentification et de l'abonnement dans Azure, consultez [Gérer des comptes, des abonnements et des rôles d'administrateur][5].

<h3>Utilisation de la méthode Azure AD</h3>


1.  Ouvrez la console Azure PowerShell, comme indiqué dans la section [Installation d'Azure PowerShell](#Install).

2.  Tapez la commande suivante :
    
    `Add-AzureAccount`

3.  Dans la fenêtre, tapez l'adresse de messagerie et le mot de passe associés à votre compte.

4.  Azure authentifie et enregistre les informations d'identification, puis ferme la fenêtre.

<h3>Utilisation de la méthode par certificat</h3>


Le module Azure PowerShell inclut des cmdlets qui vous aident à télécharger et à importer le certificat.

* La cmdlet **Get-AzurePublishSettingsFile** ouvre une page Web vers le portail de gestion Azure, d'où vous pouvez télécharger les informations d'abonnement. Ces dernières sont contenues dans un fichier .publishsettings.

* **Import-AzurePublishSettingsFile** importe le fichier .publishsettings que le module utilise. Ce fichier inclut un certificat de gestion doté d'informations d'identification de sécurité.
<div class="dev-callout"> 
<b>Important</b> 
<p>Il est recommandé de supprimer le profil de publication téléchargé à l’aide de <b>Get-AzurePublishSettingsFile</b> après l’importation de ces paramètres. Comme le certificat de gestion inclut des informations d’identification sécurisées, seuls les utilisateurs autorisés doivent y accéder. Si vous avez besoin d’informations sur vos abonnements, consultez le <a href="http://manage.windowsazure.com/">portail de gestion Azure</a> ou le <a href="http://go.microsoft.com/fwlink/p/?LinkId=324875">portail client de Microsoft Online Services</a>.</p> 
</div>

1.  Connectez-vous au [portail de gestion Azure][6] en utilisant les informations d'identification de votre compte Azure.

2.  Ouvrez la console Azure PowerShell, comme indiqué dans la section [Installation d'Azure PowerShell](#Install).

3.  Tapez la commande suivante :
    
    `Get-AzurePublishSettingsFile`

4.  À l'invite, téléchargez et enregistrez le profil de publication et notez le chemin d'accès et le nom du fichier .publishsettings. Ces informations sont requises lorsque vous exécutez la cmdlet **Import-AzurePublishSettingsFile** pour importer les paramètres. L'emplacement par défaut et le format de nom de fichier sont les suivants :
    
    C:\\Users\\<ProfilUtilisateur>\\Download\\[*MonAbonnement*-...]-*DateTéléchargement*-credentials.publishsettings

5.  Tapez une commande semblable à celle qui suit, en remplaçant les espaces réservés par le nom de votre compte Windows, le chemin d'accès et le nom de fichier :
    
    Import-AzurePublishSettingsFile C:\\Users\\<ProfilUtilisateur>\\Downloads\\<NomAbonnement>-credentials.publishsettings

> [WACOM.NOTE] Si vous êtes ajouté à d'autres abonnements en tant que coadministrateur après l'importation de vos paramètres de publication, vous devrez répéter cette procédure pour télécharger un nouveau fichier .publishsettings, puis pour importer ces paramètres. Pour plus d'informations sur l'ajout de coadministrateurs afin de faciliter la gestion des services pour un abonnement, consultez la page [Ajout et suppression de coadministrateurs pour vos abonnements Azure][7].

<h3> Afficher les informations détaillées du compte et de l'abonnement</h3>


Vous pouvez utiliser plusieurs comptes et abonnements avec Azure PowerShell. Vous pouvez ajouter plusieurs comptes en exécutant Add-AzureAccount à plusieurs reprises.

Pour afficher les comptes disponibles, tapez :

    `Get-AzureAccount`

Pour afficher les informations d'abonnement, tapez :

    `Get-AzureSubscription`

## <a id="Ex" ></a>Exemple d'utilisation des cmdlets

Après l'installation du module et la configuration de votre ordinateur pour vous connecter à votre abonnement, vous pouvez créer un exemple de site Web pour apprendre à utiliser les cmdlets.

1.  Ouvrez l'interpréteur de commandes Azure PowerShell, s'il n'est pas déjà ouvert.

2.  Choisissez un nom pour le site. Le format de ce nom doit respecter les conventions d'attribution de nom DNS. Il doit uniquement contenir des lettres allant de << a >> à << z >>, des chiffres allant de << 0 >> à << 9 >> et un tiret << - >>. Le nom doit également être unique dans Azure.
    
    Une fois le nom choisi, tapez une commande semblable à celle qui suit. Par exemple, pour créer un site Web en utilisant votre compte et un numéro pour pouvoir utiliser cette convention plusieurs fois, tapez la commande suivante en remplaçant l'espace réservé par le nom de votre compte :
    
    `New-AzureWebsite my-site-name-1`
    
    La cmdlet crée le site Web, puis répertorie les informations le concernant.

3.  Pour vérifier l'état du site Web, tapez :
    
    `Get-AzureWebsite`
    
    La cmdlet répertorie le nom, l'état et le nom d'hôte du nouveau site Web.

	> [WACOM.NOTE] si vous l'exécutez comme indiqué ici, cette commande répertorie les informations sur tous les sites Web associés à l'abonnement en cours.

4.  Les sites Web sont démarrés après leur création. Pour arrêter le site Web, tapez :
    
    `Stop-AzureWebsite -Name account-name-1`

5.  Réexécutez la commande Get-AzureWebsite pour vérifier que l'état du site Web est << arrêté >>.

6.  Pour terminer ce test, vous pouvez supprimer le site Web. Tapez :
    
    `Remove-AzureWebsite -Name account-name-1`
    
    Confirmez la suppression pour terminer la tâche.

## <a id="Help" ></a>Accès à l'aide

Consultez les ressources suivantes pour obtenir de l'aide sur des cmdlets spécifiques :

* Dans la console, vous pouvez utiliser le système d'aide intégré, accessible par la commande **Get-Help**. Le tableau suivant fournit des exemples de commandes permettant d'accéder à l'aide. Vous pouvez obtenir plus d'informations dans la console en tapant **help**.
  
  <table  border="1" cellspacing="4" cellpadding="4">
  <tbody>
  <tr  align="left" valign="top">
      <td><b>Commande</b>
  </td>
  
      <td><b>Résultat</b>
  </td>
  
  </tr>
  
  <tr  align="left" valign="top">
      <td>help</td>
  
      <td>Explique comment utiliser le système d’aide. <p><b>Remarque</b>
   : La description inclut des informations sur des fichiers d’aide qui ne s’appliquent pas au module Azure. Plus précisément, les fichiers d’aide sont installés en même temps que le module. Vous ne pouvez pas les télécharger séparément.</p>
  
  </td> </tr> <tr  align="left"
valign="top"> <td >help azure</td>
<td >Répertorie toutes les cmdlets du module
Azure PowerShell.</td> </tr> <tr  align="left" valign="top"> <td >help
&lt;<b>language</b>&gt;-dev</td> <td
>Répertorie les cmdlets pour le développement et
la gestion d'applications dans une langue spécifique. Par exemple, help node-dev, help php-dev ou help python-dev.</td> </tr> <tr
 align="left" valign="top"> <td
>help &lt;<b>cmdlet</b>&gt;</td> <td
>Affiche de l'aide à propos d'une cmdlet Windows
PowerShell.</td> </tr> <tr  align="left" valign="top"> <td >help
&lt;<b>cmdlet</b>&gt; -parameter \*</td>
<td >Affiche les définitions des paramètres
d'une cmdlet. Par exemple, help get-azuresubscription -parameter
\*</td> </tr> <tr  align="left"
valign="top"> <td >help &lt;<b>cmdlet</b>&gt; -examples</td> <td
> Affiche la syntaxe et la description d'exemples
de commandes pour une cmdlet.</td> </tr> <tr
 align="left" valign="top"> <td
>help &lt;<b>cmdlet</b>&gt;
-full</td> <td >Affiche les prérequis
techniques pour une cmdlet.</td> </tr> </tbody>
</table>

* Vous pouvez également obtenir des informations de référence sur les cmdlets du module Azure PowerShell dans la bibliothèque Azure. Pour plus d'informations, consultez la page [Référence des cmdlets Azure][8].

Pour obtenir de l'aide de la communauté, essayez ces forums populaires :

* [Forum Azure sur MSDN][9]
* [Stackoverflow][10]

## <a id="Resources" ></a>Ressources supplémentaires

Voici certaines des ressources disponibles pour apprendre à utiliser Azure et Windows PowerShell.

* Pour nous envoyer des commentaires sur les cmdlets, signaler des problèmes ou accéder au code source, consultez le [référentiel du code Azure PowerShell][1].

* Pour en savoir plus sur l'environnement de script et en ligne de commande Windows PowerShell, consultez le [Centre de scripts TechNet][11].

* Pour plus d'informations sur l'installation, la formation, l'utilisation et la personnalisation de Windows PowerShell, consultez la page [Création de scripts avec Windows PowerShell][12].

* Pour plus d'informations sur les scripts et leur exécution dans Windows PowerShell, consultez la page [Exécution de scripts][13]. Cet article inclut des informations de base sur la création de scripts et la configuration de votre ordinateur pour les exécuter.

* Pour plus d'informations sur les cmdlets pour Azure AD, consultez la page [Gestion d'Azure AD à l'aide de Windows PowerShell][14].



[1]: https://github.com/WindowsAzure/azure-sdk-tools
[2]: http://go.microsoft.com/fwlink/p/?LinkId=320795
[3]: http://go.microsoft.com/fwlink/p/?LinkId=320376
[4]: http://go.microsoft.com/fwlink/?LinkId=320796
[5]: http://go.microsoft.com/fwlink/?LinkId=324796
[6]: http://manage.windowsazure.com
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/gg456328.aspx
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554330.aspx
[9]: http://go.microsoft.com/fwlink/p/?LinkId=320212
[10]: http://go.microsoft.com/fwlink/?LinkId=320213
[11]: http://go.microsoft.com/fwlink/p/?LinkId=320211
[12]: http://go.microsoft.com/fwlink/p/?LinkId=320210
[13]: http://go.microsoft.com/fwlink/p/?LinkId=320627
[14]: http://go.microsoft.com/fwlink/p/?LinkId=320628