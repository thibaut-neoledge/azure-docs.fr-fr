<properties
   pageTitle="Intégration continue pour Service Fabric | Microsoft Azure"
   description="Découvrez comment configurer l’intégration continue pour une application Service Fabric à l’aide de Visual Studio Team Services (VSTS)."
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
   ms.date="01/27/2016"
   ms.author="cawa" />

# Configuration de l’intégration continue pour une application Service Fabric à l’aide de Visual Studio Team Services (VSTS)

Cet article décrit les étapes de la configuration de l’intégration continue pour une application Service Fabric à l’aide de Visual Studio Team Services (VSTS) de sorte que votre application soit créée, empaquetée et déployée de manière automatique. Notez que ces instructions recréent le cluster à partir de zéro à chaque fois.

Ce document reflète la procédure actuelle et est susceptible d'évoluer au fil du temps.

## Configuration requise

Pour commencer, configurez votre projet dans Visual Studio Team Services :

1. Si vous n’en avez pas déjà un, créez un compte Team Services et configurez-le en utilisant votre [compte Microsoft](http://www.microsoft.com/account).

2. Créez un nouveau projet dans Team Services à l’aide du compte Microsoft.

3. Placez la source pour votre application Service Fabric nouvelle ou existante dans ce projet.

Consultez [Se connecter à Visual Studio](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online) pour plus d’informations sur l’utilisation des projets Team Services.

## Configurer votre principal du service

### Configurer l’authentification pour l’automatisation

Avant de configurer l’ordinateur de build, vous devez créer un [principal du service](../resource-group-create-service-principal-portal.md) dont se servira l’agent de build pour s’authentifier auprès d’Azure. Vous devez également créer un certificat et le sur un coffre de clés Azure, car Key Vault ne prend pas en charge l’authentification du principal du service. Vous pouvez effectuer ces étapes à partir de tout ordinateur. Votre ordinateur de développement est un bon choix.

### Installer Azure PowerShell et se connecter

1.	Installez Azure PowerShell.
2. Installez PowerShellGet. Pour ce faire, installez [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729), qui inclut PowerShellGet.

    >[AZURE.NOTE] Vous pouvez ignorer cette étape si vous exécutez Windows 10 avec les dernières mises à jour.

3.	Installez et mettez à jour le module AzureRM. Si vous disposez d’une version précédente d’Azure PowerShell installée, supprimez-la :

    a. Cliquez avec le bouton droit sur le bouton Démarrer, puis sélectionnez **Ajout/Suppression de programmes**.

    b. Recherchez « Azure PowerShell » et désinstallez le programme.

    c. Lancez une invite de commandes PowerShell.

    d. Installez le module AzureRM à l’aide de la commande `Install-Module AzureRM`.

    e. Mettez à jour le module « AzureRM » à l’aide de la commande `Update-AzureRM`.

3.	Désactivez (ou activez) la collecte de données Azure.

    Les applets de commande Azure vous invitent à accepter ou à refuser la collecte de données jusqu’à ce que vous fassiez un choix. Ces invites bloquent l’automatisation en attendant l’entrée utilisateur. Pour supprimer ces invites en faisant un choix à l’avance, exécutez l’une des commandes suivantes :

    - Enable-AzureRmDataCollection

    - Disable-AzureRmDataCollection

4.	Connectez-vous à Azure PowerShell.

    a. Exécutez la commande `Login-AzureRmAccount`.

    b. Dans la boîte de dialogue qui s’affiche, entrez vos informations d’identification Azure.

    c. Exécutez la commande `Get-AzureRmSubscription`.

    d. Recherchez l'abonnement que vous souhaitez utiliser.

    e. Exécutez la commande `Select-AzureRmSubscription -SubscriptionId <id for your subscription>`.

### Créer un principal du service

1.	Téléchargez et extrayez [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) dans un dossier sur cet ordinateur.

2.	Dans une invite de commandes PowerShell administrateur, accédez au répertoire `Powershell\Manual` au sein de l’archive extraite.

3.	Choisissez un mot de passe pour le principal du service à l’aide de la commande suivante. N’oubliez pas ce mot de passe, car il sera utilisé comme une variable de build.

    ```
    $password = Read-Host -AsSecureString
    ```
4.	Exécutez le script PowerShell Create-ServicePrincipal.ps1 avec les paramètres suivants :

|Paramètre|Valeur|
|---|---|
|DisplayName|Nom de votre choix.|
|HomePage|URI de votre choix. Il n’est pas nécessaire qu’il existe réellement.|
|IdentifierUri|URI unique de votre choix. Il n’est pas nécessaire qu’il existe réellement.|
|SecurePassword|$password|

Lorsque le script se termine, il génère les trois valeurs suivantes. Notez les valeurs, car elles sont utilisées comme variables de build.

*  `ServicePrincipalId`
*  `ServicePrincipalTenantId`
*  `ServicePrincipalSubscriptionId`

### Créer un certificat et le charger dans un nouveau coffre de clés Azure

>[AZURE.NOTE] Cet exemple de script génère un certificat auto-signé, pratique non sécurisée à n’utiliser qu’à titre d’expérimentation. À la place, suivez les instructions de votre organisation pour obtenir un certificat.

1.	Dans une invite PowerShell administrateur, accédez au répertoire d'où vous avez extrait `Manual.zip`.

2.	Exécutez le script PowerShell `CreateAndUpload-Certificate.ps1` avec les paramètres suivants :

| Paramètre | Valeur |
| --- | --- |
| KeyVaultLocation | Valeur de votre choix. Ce paramètre doit correspondre à l’emplacement dans lequel vous prévoyez de créer le cluster. |
| CertificateSecretName | Valeur de votre choix. |
| SecureCertificatePassword | Valeur de votre choix. Ce paramètre est utilisé lorsque vous importez le certificat sur votre ordinateur de build. |
| KeyVaultResourceGroupName | Valeur de votre choix. Toutefois, n’utilisez pas le nom du groupe de ressources que vous prévoyez d’utiliser pour votre cluster. |
| KeyVaultName | Valeur de votre choix. |
| PfxFileOutputPath|Valeur de votre choix. Ce fichier est utilisé pour importer le certificat sur votre ordinateur de build. |

Lorsque le script se termine, il génère les trois valeurs suivantes. Notez ces valeurs, car elles sont utilisées comme variables de build.

* `ServiceFabricCertificateThumbprint`
* `ServiceFabricKeyVaultId`
* `ServiceFabricCertificateSecretId`

## Configurer votre ordinateur de build

### Installez Visual Studio 2015

Si vous avez déjà configuré une machine (ou prévoyez de fournir la vôtre), installez [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) sur la machine sélectionnée.

Si vous n’avez pas encore de machine, vous pouvez rapidement configurer une machine virtuelle Azure avec Visual Studio 2015 préinstallé. Pour ce faire :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Sélectionnez la commande **Nouveau** dans l’angle supérieur gauche de l’écran.

3. Sélectionnez **Marketplace**.

4. Recherchez **Visual Studio 2015**.

5. Sélectionnez **Calculer** > **Machine virtuelle** > **À partir de la galerie**.

6. Sélectionnez l’image **Visual Studio Enterprise 2015 Update 1 avec le Kit de développement logiciel (SDK) Azure 2.8 sur Windows Server 2012 R2**.

    >[AZURE.NOTE] Le Kit de développement logiciel (SDK) Azure n’est pas un composant obligatoire, mais actuellement aucune image n’ayant que Visual Studio 2015 installé n’est disponible.

7.	Suivez les instructions de la boîte de dialogue pour créer votre machine virtuelle.

### Installer le Kit de développement logiciel (SDK) Service Fabric

Installez le [Kit de développement logiciel (SDK) Service Fabric](https://azure.microsoft.com/campaigns/service-fabric/) sur votre machine.

### Installation d'Azure PowerShell

Pour installer Azure PowerShell, suivez les étapes décrites dans la section précédente **Installer Azure PowerShell et se connecter**. Ignorez la sous-section **Se connecter à Azure PowerShell**.

### Inscrire les modules Azure PowerShell avec le compte de service local

>[AZURE.NOTE] Effectuez cette opération *avant* de démarrer l’agent de build, sinon il ne sélectionnera pas la nouvelle variable d’environnement.

1. Appuyez sur Win + R, puis tapez **regedit** et appuyez sur Entrée.

2. Cliquez avec le bouton droit sur le nœud `HKEY_Users\.Default\Environment` et sélectionnez **Nouveau > Valeur de chaîne extensible**.

3. Entrez `PSModulePath` pour le nom et `%PROGRAMFILES%\WindowsPowerShell\Modules` pour la valeur. Remplacez `%PROGRAMFILES%` par la valeur de la variable d’environnement `PROGRAMFILES`.

### Importer votre certificat Automation

1.	Importez le certificat sur votre ordinateur de build. Pour ce faire :

    a. Copiez le fichier PFX créé par le script CreateAndUpload-Certificate.ps1 sur votre ordinateur de build.

    b. Ouvrez une invite PowerShell administrateur et exécutez les commandes suivantes à l’aide du mot de passe passé à `CreateAndUpload-Certificate.ps1` précédemment.

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```

2.	Exécutez le gestionnaire de certificats.

    a. Ouvrez le panneau de configuration Windows. Cliquez avec le bouton droit sur le bouton Démarrer et sélectionnez **Panneau de configuration**.

    b. Recherchez **Certificat**.

    c. Sélectionnez **Outils d’administration** > **Gérer les certificats d’ordinateur**.

3.	Accordez au compte de service local l’autorisation d’utiliser votre certificat Automation.

    a. Sous **Certificats - Ordinateur local**, développez **Personnel**, puis sélectionnez **Certificats**.

    b. Recherchez votre certificat dans la liste.

    c. Cliquez avec le bouton droit sur votre certificat, puis sélectionnez **Toutes les tâches** > **Gérer les clés privées**.

    d. Sélectionnez le bouton **Ajouter**, puis entrez **Service local** et cliquez sur **Vérifier les noms**.

    e. Sélectionnez le bouton **OK**, puis fermez le gestionnaire de certificats.

![](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

### Inscrire votre agent de build

1.	Téléchargez agent.zip. Pour ce faire :

    a. Connectez-vous à votre projet d’équipe, par exemple ****https://[your-VSTS-account-name].visualstudio.com**.

    b. Sélectionnez l’icône en forme d’engrenage dans l’angle supérieur droit de votre écran.

    c. À partir du Panneau de configuration, sélectionnez l’onglet **Pools d’agents**.

    d. Sélectionnez **Télécharger l’agent** pour télécharger le fichier agent.zip.

    e. Copiez agent.zip sur l’ordinateur de build que vous avez créé précédemment.

    f. Décompressez agent.zip sur `C:\agent` (ou à tout emplacement avec un chemin d’accès court) sur votre ordinateur de build.

        >[AZURE.NOTE] If you plan on building ASP.NET 5 Web Services, it's recommended that you  choose the shortest name possible for this folder to avoid running into **PathTooLongExceptions** errors during deployment.

2.	À partir d’une invite de commandes administrateur, exécutez `C:\agent\ConfigureAgent.cmd`. Le script vous invite à entrer les paramètres suivants :

|Paramètre|Valeur|
|---|---|
|Nom de l’agent|Acceptez la valeur par défaut, `Agent-[machine name]`.|
|URL de TFS|Entrez l’URL de votre projet d’équipe, par exemple, `https://[your-VSTS-account-name].visualstudio.com`.|
|Pool d’agents|Entrez le nom de votre pool d’agents. (Si vous n’avez pas créé de pool d’agents, acceptez la valeur par défaut).|
|Dossier de travail|Acceptez la valeur par défaut. C’est le dossier dans lequel l’agent de build va effectivement créer votre application. Remarque : si vous prévoyez de créer des services web ASP.NET 5, il est recommandé de choisir le nom le plus court possible pour ce dossier pour éviter de rencontrer des erreurs PathTooLongExceptions lors du déploiement.|
|Installer en tant que service Windows ?|La valeur par défaut est N. Remplacez cette valeur par **Y**.|
|Compte d’utilisateur pour exécuter le service|Acceptez la valeur par défaut, `NT AUTHORITY\LocalService`.|
|Annuler la configuration de l’agent existant ?|Acceptez la valeur par défaut, **N**.|

3.  Vous êtes invité à saisir des informations d’identification. Entrez les informations d’identification de votre compte Microsoft qui dispose des droits pour votre projet d’équipe.

4.  Vérifiez que votre agent de build a été inscrit. Pour ce faire :

    a. Revenez à votre navigateur web (qui doit se trouver à la page `https://[your-VSTS-account-name].visualstudio.com/_admin/_AgentPool`), puis actualisez la page.

    b. Sélectionnez le pool d’agents que vous avez sélectionné lors de l’exécution de ConfigureAgent.ps1 précédemment.

    c. Vérifiez que votre agent de build s’affiche dans la liste avec une mise en surbrillance en vert. Si la mise en surbrillance est rouge, l’agent de build rencontre des problèmes de connexion à Team Services.

![](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)


## Créer votre définition de build

>[AZURE.NOTE] La définition de build que vous créez à partir de ces instructions ne prend pas en charge plusieurs générations simultanées, même sur des machines séparées. En effet, les builds seraient en concurrence pour le même groupe de ressources/cluster. Si vous souhaitez exécuter plusieurs agents de build, vous devez modifier les instructions/scripts suivants pour éviter ces interférences.

### Ajouter les scripts d’intégration continue au contrôle de code source pour votre application

1.	Extrayez [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) dans un dossier sur votre ordinateur. Copiez le contenu de `Powershell\Automation` dans un dossier de contrôle de code source.

2.	Archivez les fichiers obtenus.

### Créer la définition de build

1.	Créez une définition de build vide. Pour ce faire :

    a. Ouvrez votre projet dans Visual Studio Team Services.

    b. Sélectionnez l’onglet **Build**.

    c. Sélectionnez le symbole **+** vert pour vous connecter pour créer une nouvelle définition de build.

    d. Sélectionnez **Vide**, puis sélectionnez le bouton **Suivant**.

    e. Vérifiez que le référentiel et la branche appropriés sont sélectionnés.

    f. Sélectionnez la file d’attente de l’agent dans laquelle vous avez inscrit votre agent de build, et cochez la case **Intégration continue**.

2.	Dans l’onglet **Variables**, créez les variables suivantes avec ces valeurs.

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
|ServiceFabricClusterName|Nom de votre choix.|||
|ServiceFabricClusterResourceGroupName|Nom de votre choix.|||
|ServiceFabricClusterLocation|Nom de votre choix correspondant à l’emplacement de votre coffre de clés.|||
|ServiceFabricClusterAdminPassword|Nom de votre choix.|X||
|ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
|ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>` Remarque : le point de terminaison de connexion dans votre profil de publication est ignoré. Le point de terminaison de connexion pour votre cluster temporaire est utilisé à la place.|||
|ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
|ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` Cela devrait être le dossier contenant votre fichier .sfproj.||||

3.  Enregistrez la définition de build et nommez-la. (Vous pouvez modifier ce nom ultérieurement si vous le souhaitez.)

### Ajouter une étape Build
@<Author GitHub alias> – Vérifiez la copie de votre article, répondez aux questions que j'ai saisies dans les commentaires et dites-moi si j'ai modifié le sens technique à un endroit quelconque.

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

4.	Choisissez le bouton **…** en regard du champ **Solution** et sélectionnez le fichier .sfproj de votre projet d’application.

5.	Entrez `$(BuildPlatform)` pour **Plateforme**.

6.	Entrez `$(BuildConfiguration)` pour **Configuration**.

7.	Entrez `/t:Package` pour **MSBuild Arguments**.

8.	Décochez la case **Restaurer des packages NuGet** (si elle n’est pas déjà décochée).

9.	Enregistrez la définition de build.

### Ajouter une étape « Supprimer le groupe de ressources de cluster »

Si un build précédent n’a pas nettoyé après lui (par exemple, si le build a été annulé avant de pouvoir nettoyer), il est possible qu’un groupe de ressources existant entre en conflit avec le nouveau. Pour éviter les conflits, nettoyez tout groupe de ressources restant (et ses ressources associées) avant d’en créer un nouveau.

1.	Dans l’onglet **Build**, sélectionnez la commande **Ajouter une étape de build…**

2.	Sélectionnez **Utilitaire** > **PowerShell**.

3.	Cliquez sur l’icône en forme de crayon en regard du nom de l’étape de build et renommez-la **Supprimer le groupe de ressources de cluster**.

4.	Sélectionnez la commande **...** en regard de **Nom du fichier script**. Accédez à l’emplacement où vous avez extrait les scripts d’automatisation, puis sélectionnez **Remove-ClusterResourceGroup.ps1**.

5.	Pour **Arguments**, entrez `-ServicePrincipalPassword "$(ServicePrincipalPassword)"`.

6.	Enregistrez la définition de build.

### Ajouter une étape « Configurer et déployer sur le cluster sécurisé »

1.	Dans l’onglet **Build**, sélectionnez la commande **Ajouter une étape de build…**

2.	Sélectionnez **Utilitaire** > **PowerShell**.

3.	Cliquez sur l’icône en forme de crayon en regard du nom de l’étape de build et renommez-la **Configurer et déployer sur le cluster sécurisé**.

4.	Sélectionnez le bouton **...** en regard de **Nom du fichier script**. Accédez à l’emplacement où vous avez extrait les scripts d’automatisation, puis sélectionnez **ProvisionAndDeploy-SecureCluster.ps1**.

5.	Pour **Arguments**, entrez `-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"`.

6.	Enregistrez la définition de build.

### Ajouter une étape « Supprimer le groupe de ressources de cluster »

Maintenant que vous avez terminé avec le cluster temporaire, vous pouvez le nettoyer. Si vous ne le faites pas, vous allez continuer à être facturé pour l’utilisation du cluster temporaire. Cette étape supprime le groupe de ressources, ce qui supprime le cluster et toutes les autres ressources dans le groupe.

>[AZURE.NOTE] Il existe une différence entre cette étape et l’étape « Supprimer le groupe de ressources de cluster » précédente : pour celle-ci, l’option « Toujours exécuter » doit être activée.

1.	Dans l’onglet **Build**, sélectionnez la commande **Ajouter une étape de build…**

2.	Sélectionnez **Utilitaire** > **PowerShell**.

3.	Cliquez sur l’icône en forme de crayon en regard du nom de l’étape de build et renommez-la **Supprimer le groupe de ressources de cluster**.

4.	Sélectionnez le bouton **...** en regard de **Nom du fichier script**. Accédez à l’emplacement où vous avez extrait les scripts d’automatisation, puis sélectionnez **RemoveClusterResourceGroup.ps1**.

5.	Pour **Arguments**, entrez `-ServicePrincipalPassword "$(ServicePrincipalPassword)`."

6.	Sous **Options de contrôle**, cochez la case **Toujours exécuter**.

7.	Enregistrez la définition de build.

### Essayer

Cliquez sur **Mettre la build en file d’attente** pour démarrer une build. Les builds seront également déclenchées au moment de l’archivage ou d’une opération de type push.


## Autres solutions

Les instructions précédentes créent un cluster pour chaque build et le suppriment à la fin du build. Si vous préférez que chaque build effectue une mise à niveau de l’application (vers un cluster existant) à la place, procédez comme suit :

1.	Créez manuellement un cluster de test par le biais du portail Azure ou d’Azure PowerShell. Vous pouvez mentionner le script `ProvisionAndDeploy-SecureCluster.ps1` en tant que référence.

2.	Configurez votre profil de publication pour prendre en charge la mise à niveau de l’application en suivant [ces instructions](service-fabric-visualstudio-configure-upgrade.md).

3.	Remplacez l’étape **Configurer et déployer sur le cluster sécurisé** par une étape qui appelle directement Deploy-FabricApplication.ps1 (et le transmet à votre profil de publication).

4.	Supprimez les deux étapes de build **Supprimer le groupe de ressources de cluster** de votre définition de build.

## Étapes suivantes

Pour en savoir plus sur l’intégration continue avec les applications de Service Fabric, consultez les articles suivants :

- [Documentation relative aux builds - Accueil](https://msdn.microsoft.com/Library/vs/alm/Build/overview)
- [Déployer un agent de build](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows)
- [Créer et configurer une définition de build](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_0316_2016-->