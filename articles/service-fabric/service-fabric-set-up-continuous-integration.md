<properties
   pageTitle="Intégration continue pour Service Fabric | Microsoft Azure"
   description="Découvrez comment configurer l’intégration continue pour une application Service Fabric à l’aide de Visual Studio Team Services."
   services="service-fabric"
   documentationCenter="na"
   authors="cawams"
   manager="timlt"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="10/16/2015"
   ms.author="cawa" />

# Configurer l’intégration continue pour une application Azure Service Fabric à l’aide de Visual Studio Team Services

Cet article passe en revue la configuration de l’intégration continue pour une application Service Fabric à l’aide de Visual Studio Team Services de sorte que votre application puisse être créée, empaquetée et déployée de manière automatique. Notez que ce document reflète l’expérience actuelle et est supposé évoluer à mesure que le développement progresse. En outre, ces instructions recréent le cluster à partir de zéro à chaque fois.

## Composants requis

Pour commencer, configurez votre projet dans Visual Studio Team Services :

1. Si vous n’en avez pas déjà un, créez un compte Team Services en utilisant votre [compte Microsoft](http://www.microsoft.com/account).
2. Créez un projet dans Team Services à l’aide du compte Microsoft.
3. Placez la source pour votre application Service Fabric nouvelle ou existante dans ce projet.

Consultez [Se connecter à Visual Studio](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online) pour plus d’informations sur l’utilisation des projets Team Services.

## Étapes de configuration

### Configurer l’authentification pour l’automatisation

Avant de configurer l’ordinateur de build, vous devez créer un « principal du service » dont se sert Automation pour s’authentifier auprès d’Azure. Vous devez également créer un certificat et le charger dans un coffre de clés, car Azure Key Vault ne prend pas en charge l’authentification du principal du service. Vous pouvez effectuer ces étapes à partir de tout ordinateur. Votre ordinateur de développement est un bon choix.

### Installer Azure PowerShell et se connecter

1.	Installez Azure PowerShell.
    - Installez PowerShellGet. Pour ce faire, installez [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729), qui inclut PowerShellGet.

    >[AZURE.NOTE]Vous pouvez ignorer cette étape si vous exécutez Windows 10 avec les dernières mises à jour.

2.	Installez et mettez à jour le module AzureRM.
    1.  Si vous disposez d’une version précédente d’Azure PowerShell installée, supprimez-la. Cliquez avec le bouton droit sur le bouton Démarrer, puis sélectionnez **Ajout/Suppression de programmes**. Recherchez « Azure PowerShell » et désinstallez le programme.
    2.  Lancez une invite de commandes PowerShell.
    3.	Installez le module AzureRM à l’aide de la commande `Install-Module AzureRM`.
    4.	Mettez à jour le module AzureRM à l’aide de la commande `Update-AzureRM`.

3.	Désactivez (ou activez) la collecte de données Azure.

    Les applets de commande Azure vous invitent à accepter ou à refuser la collecte de données jusqu’à ce que vous fassiez un choix. Ces invites bloquent l’automatisation en attendant l’entrée utilisateur. Pour supprimer ces invites en faisant un choix à l’avance, exécutez l’une des commandes suivantes :

    - Enable-AzureRmDataCollection
    - Disable-AzureRmDataCollection

4.	Connectez-vous à Azure PowerShell.
    1. Lancez une invite PowerShell administrateur et exécutez la commande `Login-AzureRMAccount`.
    2. Dans la boîte de dialogue qui s’affiche, entrez vos informations d’identification Azure.
    3. Exécutez la commande `Get-AzureRmSubscription`.
    4. Recherchez l’abonnement que vous souhaitez utiliser, puis exécutez la commande `Select-AzureRmSubscription -SubscriptionId <id for your subscription>`.

### Créer un principal du service

1.	Procédez à l’extraction de [ServiceFabricCIManualScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703773) sur un dossier de cet ordinateur.
2.	Dans une invite de commandes PowerShell administrateur, accédez au répertoire dans lequel vous avez extrait ServiceFabricCIManualScripts.zip.
3.	Choisissez un mot de passe pour le principal du service à l’aide de la commande suivante. N’oubliez pas ce mot de passe, car il sera utilisé comme une variable de build.

    ```
    $password = Read-Host -AsSecureString
    ```
4.	Exécutez le script PowerShell Create-ServicePrincipal.ps1 avec les paramètres suivants :

    |Paramètre|Valeur|
    |---|---|
    |ServicePrincipalDisplayName|Nom de votre choix.|
    |ServicePrincipalHomePage|URI de votre choix. Il n’est pas nécessaire qu’il existe réellement.|
    |ServicePrincipalIdentifierUri|URI unique de votre choix. Il n’est pas nécessaire qu’il existe réellement.|
    |ServicePrincipalSecurePassword|$password|

    Lorsque le script se termine, il génère les trois valeurs suivantes. Notez les valeurs, car elles sont utilisées comme variables de build.

    - ServicePrincipalId
    - ServicePrincipalTenantId
    - ServicePrincipalSubscriptionId

### Créer un certificat et le charger dans un nouveau coffre de clés

1.	Dans une invite PowerShell administrateur, accédez au répertoire dans lequel vous avez extrait ServiceFabricCIManualScripts.zip.
2.	Exécutez le script PowerShell CreateAndUpload-Certificate.ps1 avec les paramètres suivants.

    |Paramètre|Valeur|
    |---|---|
    |ServiceFabricKeyVaultLocation|Valeur de votre choix. Doit correspondre à l’emplacement dans lequel vous prévoyez de créer le cluster.|
    |ServiceFabricCertificateSecretName|Valeur de votre choix.|
    |ServiceFabricSecureCertificatePassword|Valeur de votre choix. Utilisée lorsque vous importez le certificat sur votre ordinateur de build.|
    |ServiceFabricKeyVaultResourceGroupName|Valeur de votre choix. Toutefois, n’utilisez pas le nom du groupe de ressources que vous prévoyez d’utiliser pour votre cluster.|
    |ServiceFabricKeyVaultName|Valeur de votre choix.|
    |ServiceFabricPfxFileOutputPath|Valeur de votre choix. Ce fichier est utilisé pour importer le certificat sur votre ordinateur de build.|

    Lorsque le script se termine, il génère les trois valeurs suivantes. Notez ces valeurs, car elles sont utilisées comme variables de build.

    - ServiceFabricCertificateThumbprint
    - ServiceFabricKeyVaultId
    - ServiceFabricCertificateSecretId


## Configurer votre ordinateur de build

Notez que la définition de build que vous créez à partir de ces instructions ne prend pas en charge la génération en parallèle, même sur des machines séparées. (En effet, les builds seraient en concurrence pour le même groupe de ressources/cluster). Si vous souhaitez exécuter plusieurs agents de build, vous devez modifier les instructions/scripts suivants pour éviter ces interférences.

### Installez Visual Studio 2015

1.	Si vous avez déjà configuré une machine (ou prévoyez de fournir la vôtre), installez [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) sur cette machine.
2.	Si vous n’avez pas encore de machine, vous pouvez rapidement configurer une machine virtuelle Azure avec Visual Studio 2015 préinstallé. Pour ce faire :
    1.	Connectez-vous au [portail Azure](http://portal.azure.com).
    2.	Sélectionnez la commande **Nouveau** dans l’angle supérieur gauche de l’écran.
    3.	Sélectionnez **Marketplace**.
    4.	Recherchez **Visual Studio 2015**.
    5.	Sélectionnez **Calculer** > **Machine virtuelle** > **À partir de la galerie**.
    6.	Sélectionnez l’image **Visual Studio Enterprise 2015 avec le Kit de développement logiciel (SDK) Azure 2.7 sur Windows Server 2012 R2**.

        >[AZURE.NOTE] Le Kit de développement logiciel (SDK) Azure n’est pas un composant obligatoire, mais aucune image n’ayant que Visual Studio 2015 installé n’est disponible.

    7.	Suivez les instructions de la boîte de dialogue pour créer votre machine virtuelle. (Nous vous recommandons de choisir une machine virtuelle de série D pour optimiser les performances du disque et de l’UC.)

### Installer le Kit de développement logiciel (SDK) Service Fabric

Installez le [Kit de développement logiciel (SDK) Service Fabric](https://azure.microsoft.com/campaigns/service-fabric/).

### Inscrire le référentiel NuGet du Kit de développement logiciel (SDK) Service Fabric

1.	Créez le répertoire `%SYSTEMDRIVE%\Windows\ServiceProfiles\LocalService\AppData\Roaming\NuGet` sur l’ordinateur de build (s’il n’existe pas déjà).
2.	Si un fichier appelé NuGet.config existe déjà dans ce répertoire, ouvrez-le et ajoutez l’élément suivant en tant qu’enfant de `<packageSources>`.

    ```
    <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />`
    ```

3.	Si NuGet.config n’existe pas, créez-le avec le contenu suivant. Remplacez `<path to service fabric SDK>` par le chemin d’accès au Kit de développement logiciel (SDK) Service Fabric sur l’ordinateur de build.

>[AZURE.NOTE] Par défaut, `<path to service fabric SDK>` correspond à `%ProgramFiles%\Microsoft SDKs\Service Fabric`.

    NuGet.config:

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <packageSources>
        <!-- Add this repository to the list of available repositories -->
        <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />
      </packageSources>
    </configuration>
    ```

### Installation d'Azure PowerShell

Pour installer Azure PowerShell, suivez les étapes décrites dans la section précédente (**Installer Azure PowerShell et se connecter**). Ignorez l’étape 4 (**Se connecter à Azure PowerShell**).

### Inscrire les modules Azure PowerShell avec le compte de service local

1. Appuyez sur Win+R, puis tapez **regedit** et appuyez sur Entrée.
2. Cliquez avec le bouton droit sur le nœud `HKEY_Users\.Default\Environment` et sélectionnez **Nouveau > Valeur de chaîne extensible**.
3. Entrez `PSModulePath` pour le nom et `%PROGRAMFILES%\WindowsPowerShell\Modules` pour la valeur.

>[AZURE.NOTE] Effectuez cette opération *avant* de démarrer l’agent de build ; sinon, la nouvelle variable d’environnement n’est pas détectée.

### Importer votre certificat Automation

1.	Importez le certificat sur votre ordinateur de build. Pour ce faire :
    1. Copiez le fichier PFX créé par le script CreateAndUpload-Certificate.ps1 sur votre ordinateur de build.
    2. Ouvrez un compte PowerShell administrateur et exécutez les commandes suivantes à l’aide du mot de passe passé à GenerateCertificate.ps1 précédemment :

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```  

2. Exécutez le gestionnaire de certificats.

    1. Ouvrez le Panneau de configuration Windows. Cliquez avec le bouton droit sur le bouton Démarrer et sélectionnez **Panneau de configuration**.
    2. Recherchez **Certificat**.
    3. Sélectionnez **Outils d’administration** > **Gérer les certificats d’ordinateur**.

3. Accordez au compte de service local l’autorisation d’utiliser votre certificat Automation.

    1.	Sous **Certificats - Ordinateur local**, développez **Personnel**, puis choisissez **Certificats**.
    2.	Recherchez votre certificat dans la liste.
    3.	Cliquez avec le bouton droit sur votre certificat, puis sélectionnez **Toutes les tâches** > **Gérer les clés privées**.
    4.	Appuyez sur le bouton **Ajouter**, entrez **Service local**, puis choisissez **Vérifier les noms**.
    5.	Sélectionnez le bouton **OK**, puis fermez le gestionnaire de certificats.

![Image de l’interface utilisateur du gestionnaire de certificat Windows](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

### Inscrire votre agent de build

1.	Téléchargez agent.zip. Pour ce faire :
    1.	Connectez-vous à votre projet d’équipe, par exemple **https://[your-Team-Services-account-name].visualstudio.com**.
1.	Sélectionnez l’icône en forme d’engrenage dans l’angle supérieur droit de votre écran.
    1.	À partir du Panneau de configuration, sélectionnez l’onglet **Pools d’agents**.
    1.	Sélectionnez **Télécharger l’agent** pour télécharger le fichier agent.zip.
    1.	Copiez agent.zip sur l’ordinateur de build que vous avez créé précédemment.
    1.	Décompressez agent.zip sur `C:\agent` (ou à tout emplacement avec un chemin d’accès court) sur votre ordinateur de build.

        >[AZURE.NOTE]Si vous prévoyez de créer des services web ASP.NET 5, nous vous recommandons de choisir le nom le plus court possible pour ce dossier pour éviter de rencontrer des erreurs **PathTooLongExceptions** pendant le déploiement.

2.	À partir d’une invite de commandes PowerShell administrateur, exécutez `C:\agent\ConfigureAgent.ps1`. Le script vous invite à entrer les paramètres suivants :

    |Paramètre|Valeur|
    |---|---|
    |Nom de l’agent|Acceptez la valeur par défaut, `Agent-[machine name]`.
    |URL de TFS|Entrez l’URL de votre projet d’équipe, par exemple `https://[your-Team-Services-account-name].visualstudio.com`.
    |Pool d’agents|Entrez le nom de votre pool d’agents. (Si vous n’avez pas créé de pool d’agents, acceptez la valeur par défaut).|
    |Dossier de travail|Acceptez la valeur par défaut. C’est le dossier dans lequel l’agent de build va effectivement créer votre application. Remarque : si vous prévoyez de créer des services web ASP.NET 5, nous vous recommandons de choisir le nom le plus court possible pour ce dossier pour éviter de rencontrer des erreurs PathTooLongExceptions pendant le déploiement.|
    |Installer en tant que service Windows ?|La valeur par défaut est N. Remplacez cette valeur par Y.|
    |Compte d’utilisateur pour exécuter le service|Acceptez la valeur par défaut, `NT AUTHORITY\LocalService)`.|
    |Annuler la configuration de l’agent existant ?|Acceptez la valeur par défaut, **N**.|

3. Vous êtes invité à saisir des informations d’identification. Entrez les informations d’identification de votre compte Microsoft qui dispose des droits pour votre projet d’équipe.
4. Vérifiez que votre agent de build a été inscrit. Pour ce faire :

    1. Revenez à votre navigateur web (qui doit se trouver à la page `https://[your-Team-Services-account-name].visualstudio.com/_admin/_AgentPool`), puis actualisez la page.
    1. Sélectionnez le pool d’agents que vous avez sélectionné lors de l’exécution de ConfigureAgent.ps1 précédemment.
    1. Vérifiez que votre agent de build s’affiche dans la liste avec une mise en surbrillance en vert. Si la mise en surbrillance est rouge, l’agent de build rencontre des problèmes de connexion à Team Services.

![Onglet **Pools d’agents** du Panneau de configuration](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)


## Configurer votre définition de build

### Ajouter les scripts d’intégration continue au contrôle de code source pour votre application

1.	Procédez à l’extraction de [ServiceFabricCIAutomationScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703775) sur tout dossier dans le contrôle de code source.
2.	Archivez les fichiers obtenus.

### Créer la définition de build

1.	Créez une définition de build vide. Pour ce faire :

    1.	Ouvrez votre projet dans Visual Studio Team Services.
    1.	Sélectionnez l’onglet **Build**.
    1.	Sélectionnez le symbole **+** vert pour vous connecter pour créer une nouvelle définition de build.
    1.	Sélectionnez **Vide**, puis sélectionnez le bouton **Suivant**.
    1.  Vérifiez que le référentiel et la branche appropriés sont sélectionnés.
    1.  Sélectionnez la file d’attente de l’agent dans laquelle vous avez inscrit votre agent de build, puis cochez la case **Intégration continue**.
2.	Sous l’onglet **Variables**, créez les variables suivantes avec ces valeurs :

    |Variable|Valeur|Secret|Autoriser lors de la file d’attente|
    |---|---|---|---|
    |BuildConfiguration|Version finale||X|
    |BuildPlatform|x64|||
    |ServicePrincipalPassword|Le mot de passe que vous avez transmis à CreateServicePrincipal.ps1|X||
    |ServicePrincipalId|À partir de la sortie de CreateServicePrincipal.ps1|||
    |ServicePrincipalTenantId|À partir de la sortie de CreateServicePrincipal.ps1|||
    |ServicePrincipalSubscriptionId|À partir de la sortie de CreateServicePrincipal.ps1|||
    |ServiceFabricCertificateThumbprint|À partir de la sortie de GenerateCertificate.ps1|||
    |ServiceFabricKeyVaultId|À partir de la sortie de GenerateCertificate.ps1|||
    |ServiceFabricCertificateSecretId|À partir de la sortie de GenerateCertificate.ps1|||
    |ServiceFabricClusterResourceGroupName|Nom de votre choix.|||
    |ServiceFabricClusterName|Nom de votre choix.|||
    |ServiceFabricClusterLocation|Nom de votre choix correspondant à l’emplacement de votre coffre de clés.|||
    |ServiceFabricClusterAdminPassword|Nom de votre choix.|X||
    |ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
    |ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>` Remarque : le point de terminaison de connexion dans votre profil de publication est ignoré. Le point de terminaison de connexion pour votre cluster temporaire est utilisé à la place.|||
    |ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
    |ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` Cela devrait être le dossier contenant votre fichier .sfproj.||||

3.	Dans l’onglet **Déclencheurs**, sélectionnez les options **Intégration continue** et **Modifications par lots**.
4.	Sous l’onglet **Général**, cliquez sur la file d’attente dans laquelle vous avez inscrit vos agents de build.
5.	Enregistrez la définition de build et nommez-la. (Vous pouvez modifier ce nom ultérieurement si vous le souhaitez.)

### Ajouter une étape Build

1.	Dans l’onglet **Build**, sélectionnez la commande **Ajouter une étape de build…**
2.	Sélectionnez **Build** > **MSBuild**.
3.	Cliquez sur l’icône en forme de crayon en regard du nom de l’étape de build et renommez-la **Build**.
4.	Sélectionnez le bouton **…** en regard du champ **Solution**, puis sélectionnez votre fichier .sln.
5.	Entrez `$(BuildPlatform)` pour **Plateforme**.
6.	Entrez `$(BuildConfiguration)` pour **Configuration**.
7.	Cochez la case **Restaurer des packages NuGet** (si elle n’est pas déjà sélectionnée).
8.	Enregistrez la définition de build.

### Ajouter une étape Package

1.	Dans l’onglet **Build**, sélectionnez la commande **Ajouter une étape de build…**
2.	Sélectionnez **Build** > **MSBuild**.
3.	Cliquez sur l’icône en forme de crayon en regard du nom de l’étape de build et renommez-la **Package**.
4.	Choisissez le bouton **…** en regard du champ **Solution**, puis sélectionnez le fichier .sfproj de votre projet d’application.
5.	Entrez `$(BuildPlatform)` pour **Plateforme**.
6.	Entrez `$(BuildConfiguration)` pour **Configuration**.
7.	Entrez `/t:Package` pour **MSBuild Arguments**.
8.	Décochez la case **Restaurer des packages NuGet** (si elle n’est pas déjà décochée).
9.	Enregistrez la définition de build.

### Ajouter une étape Supprimer le groupe de ressources de cluster

Si un build précédent n’a pas nettoyé après lui (par exemple, si le build a été annulé avant de pouvoir nettoyer), il est possible qu’un groupe de ressources existant entre en conflit avec le nouveau. Pour éviter les conflits, nettoyez tout groupe de ressources restant (et ses ressources associées) avant d’en créer un nouveau :

1.	Dans l’onglet **Build**, sélectionnez la commande **Ajouter une étape de build…**
2.	Sélectionnez **Utilitaire** > **PowerShell**.
3.	Cliquez sur l’icône en forme de crayon en regard du nom de l’étape de build et renommez-la **Supprimer le groupe de ressources de cluster**.
4.	Sélectionnez la commande **...** en regard de **Nom du fichier script**. Accédez à l’emplacement où vous avez extrait les scripts d’automatisation, puis sélectionnez **Remove-ClusterResourceGroup.ps1**.
5.	Pour **Arguments**, entrez `-ServicePrincipalPassword "$(ServicePrincipalPassword)"`.
6.	Enregistrez la définition de build.

### Ajouter une étape Configurer et déployer sur le cluster sécurisé

1.	Dans l’onglet **Build**, sélectionnez la commande **Ajouter une étape de build…**
2.	Sélectionnez **Utilitaire** > **PowerShell**.
3.	Cliquez sur l’icône en forme de crayon en regard du nom de l’étape de build et renommez-la **Configurer et déployer sur le cluster sécurisé**.
4.	Sélectionnez le bouton **...** en regard de **Nom du fichier script**. Accédez à l’emplacement où vous avez extrait les scripts d’automatisation, puis sélectionnez **ProvisionAndDeploy-SecureCluster.ps1**.
5.	Pour Arguments, entrez `-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"`.
6.	Enregistrez la définition de build.

### Ajouter une étape Supprimer le groupe de ressources de cluster

Maintenant que vous avez terminé avec le cluster temporaire, vous pouvez le nettoyer. Si vous ne le faites pas, vous allez continuer à être facturé pour l’utilisation du cluster temporaire. Cette procédure supprime le groupe de ressources, ce qui supprime le cluster et toutes les autres ressources dans le groupe :

1.	Dans l’onglet **Build**, sélectionnez la commande **Ajouter une étape de build…**
2.	Sélectionnez **Utilitaire** > **PowerShell**.
3.	Cliquez sur l’icône en forme de crayon en regard du nom de l’étape de build et renommez-la **Supprimer le groupe de ressources de cluster**.
4.	Sélectionnez le bouton **...** en regard de **Nom du fichier script**. Accédez à l’emplacement où vous avez extrait les scripts d’automatisation, puis sélectionnez **RemoveClusterResourceGroup.ps1**.
5.	Pour Arguments, entrez `-ServicePrincipalPassword "$(ServicePrincipalPassword)`.
6.	Sous **Options de contrôle**, cochez la case **Toujours exécuter**.
7.	Enregistrez la définition de build.

### Essayez !

Sélectionnez la commande **Mettre en file d’attente le build** pour démarrer un build. Les builds sont également déclenchées lors de l’archivage.


## Autres solutions

Les instructions précédentes créent un cluster pour chaque build et le suppriment à la fin du build. Si vous préférez que chaque build effectue une mise à niveau de l’application (vers un cluster existant), procédez comme suit :

1.	Créez manuellement un cluster de test par le biais du portail Azure ou d’Azure PowerShell. Vous pouvez vous rapporter au script ProvisionAndDeploy-SecureCluster.ps1 en guise de référence.
2.	Configurez votre profil de publication pour prendre en charge la mise à niveau de l’application en suivant [ces instructions](service-fabric-visualstudio-configure-upgrade.md).

3.	Remplacez l’étape **Configurer et déployer sur le cluster sécurisé** par une étape qui appelle directement Deploy-FabricApplication.ps1 (et le transmet à votre profil de publication).
4.	Supprimez les deux étapes de build **Supprimer le groupe de ressources de cluster** de votre définition de build.

## Étapes suivantes

Pour en savoir plus sur l’intégration continue avec les applications de Service Fabric, consultez les articles suivants :

- [Documentation relative aux builds - Accueil](https://msdn.microsoft.com/Library/vs/alm/Build/overview)
- [Déployer un agent de build](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows)
- [Créer et configurer une définition de build](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!----HONumber=AcomDC_0114_2016-->