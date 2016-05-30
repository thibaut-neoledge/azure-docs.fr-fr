<properties
   pageTitle="Intégration continue pour Service Fabric | Microsoft Azure"
   description="Découvrez comment configurer l’intégration continue pour une application Service Fabric à l’aide de Visual Studio Team Services (VSTS)."
   services="service-fabric"
   documentationCenter="na"
   authors="mthalman-msft"
   manager="timlt"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="03/29/2016"
   ms.author="mthalman" />

# Configuration de l’intégration continue pour une application Service Fabric à l’aide de Visual Studio Team Services

Cet article décrit les étapes de la configuration de l’intégration continue pour une application Service Fabric Azure à l’aide de Visual Studio Team Services (VSTS) de sorte que votre application soit créée, empaquetée et déployée de manière automatique. Notez que ces instructions recréent le cluster à partir de zéro à chaque fois.

Ce document reflète la procédure actuelle et est susceptible d'évoluer au fil du temps.

## Configuration requise

Pour commencer, configurez votre projet dans Visual Studio Team Services :

1. Si vous n’en avez pas déjà un, créez un compte Team Services et configurez-le en utilisant votre [compte Microsoft](http://www.microsoft.com/account).

2. Créez un projet dans Team Services à l’aide du compte Microsoft.

3. Placez la source pour votre application Service Fabric nouvelle ou existante dans ce projet.

Pour plus d’informations sur l’utilisation des projets Team Services, consultez [Se connecter à Visual Studio](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

## Configurer votre principal du service

### Configurer l’authentification pour l’automatisation

Avant de configurer l’ordinateur de build, vous devez créer un [principal du service](../resource-group-create-service-principal-portal.md) dont se servira l’agent de build pour s’authentifier auprès d’Azure. Vous devez également créer un certificat et le charger sur Azure Key Vault, car Key Vault ne prend pas en charge l’authentification du principal du service. Vous pouvez effectuer ces étapes à partir de tout ordinateur. Votre ordinateur de développement est un bon choix.

### Installer Azure PowerShell et se connecter

1.  Installez PowerShellGet.

    a. Vous pouvez ignorer cette étape si vous exécutez Windows 10 avec les dernières mises à jour (PowerShellGet est déjà installé).

    b. Dans le cas contraire, installez [Windows Management Framework 5.0](https://aka.ms/wmf5download), qui inclut PowerShellGet.

2.	Installez et mettez à jour le module AzureRM. Si vous disposez d’une version précédente d’Azure PowerShell installée, supprimez-la :

    a. Cliquez avec le bouton droit sur le bouton Démarrer, puis sélectionnez **Ajout/Suppression de programmes**.

    b. Recherchez « Azure PowerShell » et désinstallez le programme.

    c. Ouvrez une invite de commandes PowerShell en tant qu’administrateur.

    d. Installez le module AzureRM à l’aide de la commande `Install-Module AzureRM`.

3.	Désactivez (ou activez) la collecte de données Azure.

    Les applets de commande Azure vous invitent à accepter ou à refuser la collecte de données jusqu’à ce que vous fassiez un choix. Ces invites bloquent l’automatisation en attendant l’entrée utilisateur. Pour supprimer ces invites en faisant un choix à l’avance, exécutez l’une des commandes suivantes :

    - Enable-AzureRmDataCollection

    - Disable-AzureRmDataCollection

4.	Connectez-vous à Azure PowerShell :

    a. Exécutez la commande `Login-AzureRmAccount`.

    b. Dans la boîte de dialogue qui s’affiche, entrez vos informations d’identification Azure.

    c. Exécutez la commande `Get-AzureRmSubscription`.

    d. Recherchez l'abonnement que vous souhaitez utiliser.

    e. Exécutez la commande `Select-AzureRmSubscription -SubscriptionId <ID for your subscription>`.

### Créer un principal du service

1. Suivez [ces instructions](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) pour créer un principal du service et le point de terminaison de service pour votre projet.

2. Notez les valeurs qui sont imprimées à la fin des données de sortie du script. Vous en aurez besoin pour configurer votre définition de build.

### Créer un certificat et le charger dans un nouveau coffre de clés Azure

>[AZURE.NOTE] Cet exemple de script génère un certificat auto-signé, pratique non sécurisée à n’utiliser qu’à titre d’expérimentation. À la place, suivez les instructions de votre organisation pour obtenir un certificat. Ces instructions utilisent également un certificat unique pour le serveur et le client. En production, il est conseillé d’utiliser des certificats distincts pour le serveur et le client.

1. Téléchargez et extrayez [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) dans un dossier sur cet ordinateur.

2. Dans une invite PowerShell administrateur, accédez au répertoire `<extracted zip>/Manual`.

3. Exécutez le script PowerShell `CreateAndUpload-Certificate.ps1` avec les paramètres suivants :

| Paramètre | Valeur |
| --- | --- |
| KeyVaultLocation | Valeur de votre choix. Ce paramètre doit correspondre à l’emplacement dans lequel vous prévoyez de créer le cluster. |
| CertificateSecretName | Valeur de votre choix. |
| CertificateDnsName | Doit correspondre au nom DNS de votre cluster. Exemple : `mycluster.westus.cloudapp.azure.com` |
| SecureCertificatePassword | Valeur de votre choix. Ce paramètre est utilisé quand vous importez le certificat sur votre ordinateur de build. |
| KeyVaultName | Valeur de votre choix. |
| KeyVaultResourceGroupName | Valeur de votre choix. Toutefois, n’utilisez pas le nom du groupe de ressources que vous prévoyez d’utiliser pour votre cluster. |
| PfxFileOutputPath| Valeur de votre choix. Ce fichier est utilisé pour importer le certificat sur votre ordinateur de build. |

Lorsque le script se termine, il génère les trois valeurs suivantes. Notez ces valeurs, car elles sont utilisées comme variables de build.

 - `ServiceFabricCertificateThumbprint`
 - `ServiceFabricKeyVaultId`
 - `ServiceFabricCertificateSecretId`

## Configurer votre ordinateur de build

### Installez Visual Studio 2015

Si vous avez déjà configuré une machine (ou prévoyez de fournir la vôtre), installez [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) sur la machine sélectionnée.

Si vous n’avez pas encore de machine, vous pouvez rapidement configurer une machine virtuelle Azure avec Visual Studio 2015 préinstallé. Pour ce faire :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Sélectionnez la commande **Nouveau** dans l’angle supérieur gauche de l’écran.

3. Sélectionnez **Marketplace**.

4. Recherchez **Visual Studio 2015**.

5. Sélectionnez **Calculer** > **Machine virtuelle** > **À partir de la galerie**.

6. Sélectionnez l’image **Visual Studio Enterprise 2015 Update 2 avec les outils Windows universels et le Kit de développement logiciel (SDK) Azure 2.9 sur Windows Server 2012 R2**.

    >[AZURE.NOTE] Le Kit de développement logiciel (SDK) Azure n’est pas un composant obligatoire, mais actuellement aucune image n’ayant que Visual Studio 2015 installé n’est disponible.

7.	Suivez les instructions de la boîte de dialogue pour créer votre machine virtuelle.

### Installer le Kit de développement logiciel (SDK) Service Fabric

Installez le [Kit de développement logiciel (SDK) Service Fabric](service-fabric-get-started.md#install-the-runtime-sdk-and-tools) sur votre machine.

### Installation d'Azure PowerShell

Pour installer Azure PowerShell, suivez les étapes décrites dans la section précédente, Installer Azure PowerShell et se connecter. Ignorez l’étape Se connecter à Azure PowerShell.

### Inscrire les modules Azure PowerShell avec le compte de service réseau

>[AZURE.NOTE] Effectuez cette opération *avant* de démarrer l’agent de build ; sinon, la nouvelle variable d’environnement n’est pas détectée.

1. Appuyez sur la touche du logo Windows + R, tapez **regedit** et appuyez sur Entrée.

2. Cliquez avec le bouton droit sur le nœud `HKEY_Users\.Default\Environment` et sélectionnez **Nouveau** > **Valeur de chaîne extensible**.

3. Entrez `PSModulePath` pour le nom et `%PROGRAMFILES%\WindowsPowerShell\Modules` pour la valeur. Remplacez `%PROGRAMFILES%` par la valeur de la variable d’environnement `PROGRAMFILES`.

### Importer votre certificat Automation

1.	Importez le certificat sur votre ordinateur de build. Pour ce faire :

    a. Copiez le fichier PFX créé par le script CreateAndUpload-Certificate.ps1 sur votre ordinateur de build.

    b. Ouvrez une invite PowerShell administrateur et exécutez les commandes suivantes à l’aide du mot de passe transmis à `CreateAndUpload-Certificate.ps1` précédemment.

    ```powershell
    $password = Read-Host -AsSecureString
    Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
    ```

2.	Exécutez le gestionnaire de certificats :

    a. Ouvrez le Panneau de configuration dans Windows. Cliquez avec le bouton droit sur le bouton Démarrer et sélectionnez **Panneau de configuration**.

    b. Recherchez **Certificat**.

    c. Sélectionnez **Outils d’administration** > **Gérer les certificats d’ordinateur**.

3.	Accordez au compte de service réseau l’autorisation d’utiliser votre certificat Automation :

    a. Sous **Certificats - Ordinateur local**, développez **Personnel**, puis choisissez **Certificats**.

    b. Recherchez votre certificat dans la liste.

    c. Cliquez avec le bouton droit sur votre certificat, puis sélectionnez **Toutes les tâches** > **Gérer les clés privées**.

    d. Sélectionnez le bouton **Ajouter**, entrez **Service réseau**, puis choisissez **Vérifier les noms**.

    e. Sélectionnez **OK**.

    ![Capture d’écran de la procédure pour accorder l’autorisation au compte de service local](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

4.  Copiez le certificat dans le dossier `Trusted People`.

    a. Votre certificat a été importé dans **Personnel/certificats**, mais nous devons l’ajouter à **Personnes autorisées**. Cliquez avec le bouton droit sur le certificat, puis sélectionnez **Copier**. Puis, cliquez avec le bouton droit sur le dossier **Personnes autorisées** et sélectionnez **Coller**.

### Inscrire votre agent de build

1.	Téléchargez agent.zip. Pour ce faire :

    a. Connectez-vous à votre projet d’équipe, par exemple **https://[your-VSTS-account-name].visualstudio.com**.

    b. Sélectionnez l’icône en forme d’engrenage dans l’angle supérieur droit de votre écran.

    c. Sélectionnez l’onglet **Pools d’agents**.

    d. Sélectionnez **Télécharger l’agent** pour télécharger le fichier agent.zip.

    >[AZURE.NOTE] Si le téléchargement ne démarre pas, vérifiez votre bloqueur de fenêtres publicitaires.

    e. Copiez agent.zip sur l’ordinateur de build que vous avez créé précédemment.

    f. Décompressez agent.zip sur `C:\agent` (ou sur tout emplacement avec un chemin court) sur votre ordinateur de build.

    >[AZURE.NOTE] Si vous prévoyez d’utiliser des services web ASP.NET 5, nous vous recommandons de choisir le nom le plus court possible pour ce dossier pour éviter de rencontrer des erreurs **PathTooLongExceptions** pendant le déploiement. Ce problème sera résolu lors de la publication de ASP.NET Core.

2.	À partir d’une invite de commandes administrateur, exécutez `C:\agent\ConfigureAgent.cmd`. Le script vous invite à entrer les paramètres suivants :

|Paramètre|Valeur|
|---|---|
|Nom de l’agent|Acceptez la valeur par défaut, `Agent-[machine name]`.|
|URL de TFS|Entrez l’URL de votre projet d’équipe, par exemple `https://[your-VSTS-account-name].visualstudio.com`.|
|Pool d’agents|Entrez le nom de votre pool d’agents. (Si vous n’avez pas créé de pool d’agents, acceptez la valeur par défaut).|
|Dossier de travail|Acceptez la valeur par défaut. C’est le dossier dans lequel l’agent de build va effectivement créer votre application. Si vous prévoyez d’utiliser des services web ASP.NET 5, nous vous recommandons de choisir le nom le plus court possible pour ce dossier pour éviter de rencontrer des erreurs PathTooLongExceptions pendant le déploiement.|
|Installer en tant que service Windows ?|La valeur par défaut est N. Remplacez cette valeur par **Y**.|
|Compte d’utilisateur pour exécuter le service|La valeur par défaut est `NT AUTHORITY\LOCAL SERVICE`. Remplacez-la par la valeur `NT AUTHORITY\NetworkService`.|
|Mot de passe de `NT AUTHORITY\Network Service`|Le compte de service réseau ne dispose pas d’un mot de passe, mais refuse les mots de passe vides. Entrez une chaîne non vide pour le mot de passe (ce que vous entrez sera ignoré).|
|Annuler la configuration de l’agent existant ?|Acceptez la valeur par défaut, **N**.|

3.  Quand vous êtes invité à entrer des informations d’identification, entrez celles de votre compte Microsoft qui dispose des droits pour votre projet d’équipe.

4.  Vérifiez que votre agent de build a été enregistré et configurez ses fonctionnalités. Pour ce faire :

    a. Revenez à votre navigateur web, (`https://[your-VSTS-account-name].visualstudio.com/_admin/_AgentPool`), puis actualisez la page.

    b. Sélectionnez le pool d’agents que vous avez sélectionné lors de l’exécution de ConfigureAgent.ps1.

    c. Vérifiez que votre agent de build s’affiche dans la liste avec une mise en surbrillance en vert. Si la mise en surbrillance est rouge, l’agent de build rencontre des problèmes de connexion à Team Services.

    ![Capture d’écran qui affiche l’état de l’agent de build](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)

    d. Sélectionnez l’agent de build, puis sélectionnez l’onglet **Fonctionnalités**.

    e. Ajoutez une fonctionnalité nommée **azureps** avec n’importe quelle valeur. Cela indique à VSTS qu’Azure PowerShell est installé sur cette machine, ce qui est requis pour utiliser certaines des tâches de génération fournies par VSTS.


## Créer votre définition de build

>[AZURE.NOTE] La définition de build que vous créez à partir de ces instructions ne prend pas en charge plusieurs générations simultanées, même sur des machines séparées. En effet, les builds seraient en concurrence pour le même groupe de ressources/cluster. Si vous souhaitez exécuter plusieurs agents de build, vous devez modifier les instructions/scripts suivants pour éviter ces interférences.

### Ajouter un modèle Azure Resource Manager Service Fabric à votre application

1. Téléchargez `azuredeploy.json` et `azuredeploy.parameters.json` à partir de [cet exemple](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

2. Ouvrez `azuredeploy.parameters.json` et modifiez les paramètres suivants :

    |Paramètre|Valeur|
    |---|---|
    |clusterLocation|Doit correspondre à l’emplacement de votre coffre de clés. Exemple : `westus`|
    |clusterName|Doit correspondre au nom DNS de votre certificat. Par exemple, si le nom DNS du certificat est `mycluster.westus.cloudapp.net`, alors `clusterName` doit être `mycluster`.|
    |adminPassword|8-123 caractères, avec au moins 3 des types de caractères suivants : majuscules, minuscules, caractères numériques, caractères spéciaux.|
    |certificateThumbprint|À partir de la sortie de `CreateAndUpload-Certificate.ps1`|
    |sourceVaultValue|À partir de la sortie de `CreateAndUpload-Certificate.ps1`|
    |certificateUrlvalue|À partir de la sortie de `CreateAndUpload-Certificate.ps1`|

3. Ajoutez les nouveaux fichiers de contrôle de code source et envoyez vers VSTS.

### Créer la définition de build

1.	Créez une définition de build vide. Pour ce faire :

    a. Ouvrez votre projet dans Visual Studio Team Services.

    b. Sélectionnez l’onglet **Build**.

    c. Sélectionnez le signe **+** vert pour créer une définition de build.

    d. Sélectionnez **Vide**, puis **Suivant**.

    e. Vérifiez que le référentiel et la branche appropriés sont sélectionnés.

    f. Cochez la case **Intégration continue** pour garantir le déclenchement de cette build chaque fois que la branche est mise à jour.

    g. Sélectionnez la file d’attente de l’agent dans laquelle vous avez inscrit votre agent de build.

2.	Dans l’onglet **Variables**, créez les variables suivantes avec ces valeurs.

    |Variable|Valeur|Secret|Autoriser lors de la file d’attente|
    |---|---|---|---|
    |BuildConfiguration|Version finale||X|
    |BuildPlatform|x64||||

3.  Enregistrez la définition de build et nommez-la. Vous pouvez modifier ce nom ultérieurement si vous le souhaitez.

### Ajouter une étape « Restaurer les packages NuGet »

1. Dans l’onglet **Build**, sélectionnez la commande **Ajouter une étape de build…**

2. Sélectionnez **Package** > **Programme d’installation de NuGet**

3. Cliquez sur l’icône en forme de crayon en regard du nom de l’étape de build et renommez-la **Restaurer les packages NuGet**.

4. Sélectionnez le bouton **…** en regard du champ **Solution**, puis sélectionnez votre fichier .sln.

5. Enregistrez la définition de build.

### Ajouter une étape Build

1.	Dans l’onglet **Build**, sélectionnez la commande **Ajouter une étape de build…**

2.	Sélectionnez **Build** > **MSBuild**.

3.	Sélectionnez l’icône en forme de crayon en regard du nom de l’étape de build et renommez-la **Build**.

4. Sélectionnez ces valeurs :

    |Nom de paramètre|Valeur|
    |---|---|
    |Solution|Cliquez sur le bouton **...** et sélectionnez le fichier `.sln` pour votre solution.|
    |Plateforme|`$(BuildPlatform)`|
    |Configuration|`$(BuildConfiguration)`|

5.	Enregistrez la définition de build.

### Ajouter une étape Package

1.	Dans l’onglet **Build**, sélectionnez la commande **Ajouter une étape de build…**

2.	Sélectionnez **Build** > **MSBuild**.

3.	Sélectionnez l’icône en forme de crayon en regard du nom de l’étape de build et renommez-la **Package**.

4. Sélectionnez ces valeurs :

    |Nom de paramètre|Valeur|
    |---|---|
    |Solution|Cliquez sur le bouton **...** et sélectionnez le fichier `.sfproj` de votre projet d’application.|
    |Plateforme|`$(BuildPlatform)`|
    |Configuration|`$(BuildConfiguration)`|
    |Arguments MSBuild|`/t:Package`|

5.	Enregistrez la définition de build.

### <a name="RemoveClusterResourceGroup"></a> Ajouter une étape « Supprimer le groupe de ressources de cluster »

Si une build précédente n’a pas nettoyé après avoir terminé (par exemple, si la build a été annulée avant de pouvoir nettoyer), il est possible qu’un groupe de ressources existant entre en conflit avec le nouveau. Pour éviter les conflits, nettoyez tout groupe de ressources restant (et ses ressources associées) avant d’en créer un nouveau.

1.	Dans l’onglet **Build**, sélectionnez la commande **Ajouter une étape de build…**.

2.	Sélectionnez **Déployer** > **Déploiement d’un groupe de ressources Azure**.

3.	Sélectionnez l’icône en forme de crayon en regard du nom de l’étape de build et renommez-la **Supprimer le groupe de ressources de cluster**.

4. Sélectionnez ces valeurs :

    |Nom de paramètre|Valeur|
    |---|---|
    |AzureConnectionType|**Azure Resource Manager**|
    |Abonnement Azure RM|Sélectionnez le point de terminaison de connexion que vous avez créé dans la section **Créer un principal du service**.|
    |Action|**Supprimer un groupe de ressources**|
    |Groupe de ressources|Entrez un nom non utilisé. Vous devez utiliser le même nom à l'étape suivante.|
    |Continuer en cas d’erreur|Cette étape échouera si le groupe de ressources n’existe pas. Activez l’option **Continuer en cas d’erreur** dans la section **Options de contrôle** pour éviter ce problème.|

5.	Enregistrez la définition de build.

### Ajout d’une étape « Configurer un cluster sécurisé »

1.	Dans l’onglet **Build**, sélectionnez la commande **Ajouter une étape de build…**.

2.	Sélectionnez **Déployer** > **Déploiement d’un groupe de ressources Azure**.

3.	Sélectionnez l’icône en forme de crayon en regard du nom de l’étape de build et renommez-la **Configurer un cluster sécurisé**.

4. Sélectionnez ces valeurs :

    |Nom de paramètre|Valeur|
    |---|---|
    |AzureConnectionType|**Azure Resource Manager**|
    |Abonnement Azure RM|Sélectionnez le point de terminaison de connexion que vous avez créé dans la section **Créer un principal du service**.|
    |Action|**Créer ou mettre à jour un groupe de ressources**|
    |Groupe de ressources|Doit correspondre au nom que vous avez utilisé à l'étape précédente.|
    |Emplacement|Doit correspondre à l’emplacement de votre coffre de clés.|
    |Modèle|Cliquez sur le bouton **…** et sélectionnez `azuredeploy.json`.|
    |Paramètres de modèle|Cliquez sur le bouton **…** et sélectionnez `azuredeploy.parameters.json`.|

5.	Enregistrez la définition de build.

### Ajoutez une étape « Déployer »

1.	Dans l’onglet **Build**, sélectionnez la commande **Ajouter une étape de build…**.

2.	Sélectionnez **Utilitaire** > **PowerShell**.

3.	Sélectionnez l’icône en forme de crayon en regard du nom de l’étape de build et renommez-la **Déployer**.

4. Sélectionnez ces valeurs :

    |Nom de paramètre|Valeur|
    |---|---|
    |Type|**Chemin de fichier**|
    |Nom du fichier de script|Cliquez sur le bouton **...** et accédez au répertoire **Scripts** à l’intérieur de votre projet d’application. Sélectionnez `Deploy-FabricApplication.ps1`.|
    |Arguments|`-PublishProfileFile path/to/MySolution/MyApplicationProject/PublishProfiles/MyPublishProfile.xml -ApplicationPackagePath path/to/MySolution/MyApplicationProject/pkg/$(BuildConfiguration)`|

5.	Enregistrez la définition de build.

### Ajouter une étape « Vérifier »

1. Cette étape est facultative lors de la configuration initiale de cette définition de build. Mais une fois que vous avez correctement exécuté une build et vérifié l’exactitude des autres étapes de build, vous pouvez insérer votre propre étape de build de vérification ici. Elle est spécifique à votre application et est destinée à vérifier l’exactitude de l’application qui a été déployée sur le cluster.
  
### Ajouter une étape « Nettoyer » finale

1. Suivez les instructions de la section [Ajouter une étape « Supprimer le groupe de ressources de cluster »](#RemoveClusterResourceGroup). Cette étape nettoie toutes les ressources Azure configurées qui ont été créées pendant la génération.

### Essayer

Sélectionnez **Mettre la build en file d’attente** pour démarrer une build. Les builds seront également déclenchées au moment de l’archivage ou d’une opération de type push.

## Autres solutions

Les instructions précédentes créent un cluster pour chaque build et le suppriment à la fin du build. Si vous préférez que chaque build effectue une mise à niveau de l’application (vers un cluster existant) à la place, procédez comme suit :

1.	Créez manuellement un cluster de test par le biais du Portail Azure ou d’Azure PowerShell en suivant [ces instructions](service-fabric-cluster-creation-via-portal.md).

2.	Configurez votre profil de publication pour prendre en charge la mise à niveau de l’application en suivant [ces instructions](service-fabric-visualstudio-configure-upgrade.md).

4.	Supprimez les étapes de build **Supprimer le groupe de ressources de cluster** et **Configurer un cluster** de votre définition de build.

## Étapes suivantes

Pour en savoir plus sur l’intégration continue avec les applications de Service Fabric, consultez les articles suivants :

 - [Documentation relative aux builds - Accueil](https://msdn.microsoft.com/Library/vs/alm/Build/overview)
 - [Déployer un agent de build](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows)
 - [Créer et configurer une définition de build](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_0518_2016-->