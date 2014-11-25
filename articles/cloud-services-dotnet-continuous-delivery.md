<properties linkid="dev-net-common-tasks-continuous-delivery" urlDisplayName="Continuous Delivery" pageTitle="Continuous delivery for cloud services with TFS in Azure" metaKeywords="Azure continuous delivery, continuous delivery sample code, continuous delivery PowerShell" description="Learn how to set up continuous delivery for Azure cloud apps. Code samples for MSBuild command-line statements and PowerShell scripts." metaCanonical="" services="" documentationCenter="" title="Continuous Delivery for Cloud Services in Azure" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen" />

# Remise continue pour Cloud Services dans Azure

Le processus décrit dans cet article vous explique comment configurer la
remise continue pour les applications cloud Azure. Il vous permet de créer
automatiquement des packages et de les déployer dans Azure
après chaque intégration du code. Le processus de génération du package décrit dans cet
article est équivalent à la commande Package de Visual Studio et les étapes de la
publication sont les mêmes que pour la commande Publish dans Visual Studio.
Cet article présente les méthodes que vous allez utiliser pour créer un serveur de builds
avec des instructions de ligne de commande MSBuild et des scripts Windows PowerShell.
Il explique également comment configurer les définitions Visual Studio Team
Foundation Server - Team Build à utiliser avec les commandes MSBuild
et les scripts PowerShell. Ce processus est personnalisable en fonction de votre environnement
de génération et des environnements Azure cibles.

Vous pouvez également utiliser Visual Studio Online, une version de TFS hébergée sur Azure, pour effectuer ces opérations plus facilement. Pour plus d'informations, consultez la page [Remise continue pour Azure avec Visual Studio Online][Remise continue pour Azure avec Visual Studio Online].

Avant de commencer, vous devez publier votre application à partir de Visual Studio.
Ceci vous assure que toutes les ressources sont disponibles et initialisées lorsque vous
tentez d'automatiser le processus de publication.

Cette procédure comprend les étapes suivantes :

-   [Étape 1 : configuration du serveur de builds][Étape 1 : configuration du serveur de builds]
-   [Étape 2 : génération d'un package à l'aide des commandes MSBuild][Étape 2 : génération d'un package à l'aide des commandes MSBuild]
-   [Étape 3 : génération d'un package avec TFS Team Build (facultatif)][Étape 3 : génération d'un package avec TFS Team Build (facultatif)]
-   [Étape 4 : publication d'un package à l'aide d'un script PowerShell][Étape 4 : publication d'un package à l'aide d'un script PowerShell]
-   [Étape 5 : publication d'un package à l'aide de TFS Team Build (facultatif)][Étape 5 : publication d'un package à l'aide de TFS Team Build (facultatif)]

## <a name="step1"> </a><span class="short-header">Configuration du serveur de builds</span>Étape 1 : configuration du serveur de builds

Avant de pouvoir créer un package Azure à l'aide de MSBuild, vous devez
installer les logiciels et les outils nécessaires sur le serveur de builds.

Visual Studio ne doit pas obligatoirement être installé sur le serveur de builds. Si
vous souhaitez utiliser Team Foundation Build Service pour gérer votre serveur
de builds, suivez les instructions de la documentation [Team Foundation Build Service][Team Foundation Build Service]
.

1.  Sur le serveur de builds, installez [.NET Framework 4][.NET Framework 4] ou [.NET Framework 4.5][.NET Framework 4.5], qui
    comprend MSBuild.
2.  Installez les outils [Azure Authoring Tools][Azure Authoring Tools] (recherchez
    WindowsAzureAuthoringTools-x86.msi ou WindowsAzureAuthoringTools-x64.msi
    en fonction du processeur de votre serveur de builds).
3.  Installez les [bibliothèques Windows Azure][bibliothèques Windows Azure] (recherchez WindowsAzureLibsForNet-x86.msi ou WindowsAzureLibsForNet-x64.msi).
4.  Copiez le fichier Microsoft.WebApplication.targets depuis une installation Visual Studio
    vers le serveur de builds. Si Visual Studio est
    installé sur l'ordinateur, le fichier se trouve dans le répertoire C:\\Program Files
    (x86)\\MSBuild\\Microsoft\\VisualStudio\\v11.0\\WebApplications (v12.0
    pour Visual Studio 2013). Vous
    devez le copier dans le même répertoire sur le serveur de builds.
5.  Installez les outils [Azure Tools for Visual Studio][bibliothèques Windows Azure].
    Recherchez WindowsAzureTools.VS110.exe pour créer des projets Visual Studio 2012 et WindowsAzureTools.VS120.exe pour créer des projets Visual Studio 2013.

## <a name="step2"> </a><span class="short-header">Génération d'un package à l'aide de MSBuild</span>Étape 2 : génération d'un package à l'aide des commandes MSBuild

Cette section décrit la création d'une commande MSBuild qui génère un
package Azure. Exécutez cette étape sur le serveur de builds afin de vérifier que
tout est correctement configuré et que la commande MSBuild fonctionne
comme vous le souhaitez. Vous pouvez ajouter cette ligne de commande aux scripts existants
sur le serveur de builds ou l'utiliser dans une définition de build
TFS, comme décrit dans la section qui suit. Pour plus
d'informations sur les paramètres de ligne de commande et MSBuild, consultez la page [Référence de la ligne de commande MSBuild][Référence de la ligne de commande MSBuild].

1.  Si Visual Studio est installé sur le serveur de builds, cliquez sur
    **Démarrer**, sur
    **Tous les programmes**, puis sélectionnez
    **Visual Studio Command Prompt** dans le dossier
    **Visual Studio Tools**
    .

    Si Visual Studio n'est pas installé sur le serveur de builds, ouvrez
    une invite de commandes et assurez-vous que MSBuild.exe est bien accessible sur le chemin
    d'accès. MSBuild est installé avec .NET Framework dans
    %WINDIR%\\Microsoft.NET\\Framework\\*Version*. Par exemple, pour
    ajouter MSBuild.exe à la variable d'environnement PATH quand .NET
    Framework 4 est installé, tapez la commande suivante à l'invite de
    commandes :

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework.0"

2.  À l'invite de commandes, accédez au dossier qui contient le fichier de projet
    Azure que vous voulez compiler.

3.  Exécutez msbuild avec l'option /target:Publish comme dans l'exemple
    suivant :

        MSBuild /target:Publish

    Cette option peut être abrégée en /t:Publish. L'option /t:Publish
    de MSBuild ne doit pas être confondue avec les commandes Publish
    disponibles dans Visual Studio lorsque le Kit de développement logiciel (SDK) Azure
    est installé. L'option /t:Publish ne génère que les packages
    Azure. Elle ne déploie pas les packages comme les commandes Publish
    de Visual Studio.

    Vous pouvez aussi spécifier le nom du projet comme paramètre
    MSBuild. S'il n'est pas spécifié, le répertoire actif est utilisé. Pour plus
    d'informations sur les options de ligne de commande MSBuild, consultez la page [Référence de la ligne
    de commande MSBuild][Référence de la ligne
    de commande MSBuild].

4.  Recherchez la sortie. Par défaut, cette commande crée un répertoire en
    relation avec le dossier racine du projet, par exemple
    *\<ProjectDir\>*\\bin\\*\<Configuration\>*\\app.publish\\. Lorsque vous
    générez un projet Azure, vous générez deux fichiers, le fichier
    de package et le fichier de configuration qui l'accompagne :

    -   Project.cspkg
    -   ServiceConfiguration.*TargetProfile*.cscfg

    Par défaut, tous les projets Azure comprennent un
    fichier de configuration de service (fichier.cscfg) pour les versions locales (débogage)
    et un autre pour les versions cloud (intermédiaire ou de production), mais vous
    pouvez ajouter ou supprimer les fichiers de configuration de service selon vos besoins. Lorsque vous
    générez un package dans Visual Studio, il vous est demandé quels
    fichiers de configuration de service vous voulez inclure avec le package.

5.  Spécifiez le fichier de configuration de service Lorsque vous générez un package
    avec MSBuild, le fichier de configuration de service local est inclus
    par défaut. Pour inclure un autre fichier de configuration de service, définissez la propriété
    TargetProfile de la commande MSBuild, comme dans l'exemple
    suivant :

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Spécifiez l'emplacement de la sortie. Définissez le chemin d'accèsavec l'option
    /p:PublishDir=*Directory*\\, en incluant la barre
    oblique inverse de fin, comme dans l'exemple suivant :

        MSBuild /target:Publish /p:PublishDir=\myserver\drops\

    Une fois que vous avez conçu et testé une ligne de commande
    MSBuild appropriée pour générer vos projets et les combiner dans un package Azure,
    vous pouvez ajouter cette ligne de commande à vos scripts. Si votre serveur
    de builds utilise des scripts personnalisés, ce processus dépend des
    particularités de votre processus personnalisé. Si vous utilisez TFS comme
    environnement de génération, vous pouvez suivre les instructions de l'étape
    qui suit pour ajouter la création du package Azure à votre processus.

## <a name="step3"> </a><span class="short-header">Création d'un package à l'aide de TFS </span>Étape 3 : génération d'un package avec TFS Team Build (facultatif)

Si Team Foundation Server (TFS) est configuré comme contrôleur de build
et que le serveur de builds est configuré comme ordinateur de builds TFS, vous pouvez configurer
une compilation automatisée pour votre package Azure. Pour plus d'informations sur
la configuration et l'utilisation de Team Foundation Server comme système de génération, consultez la page
[Vue d'ensemble de Team Foundation Build][Vue d'ensemble de Team Foundation Build]. En particulier, la
procédure suivante part du principe que vous avez configuré votre serveur de builds
comme décrit dans [Configuration d'un ordinateur de builds][Configuration d'un ordinateur de builds].

Pour configurer TFS pour générer des packages Azure, procédez
comme suit :

1.  Dans Visual Studio sur votre ordinateur de développement, dans le menu
    Affichage, sélectionnez **Team Explorer** ou choisissez Ctrl+\\, Ctrl+M. Dans la
    fenêtre de Team Explorer, développez le nœud **Builds**, cliquez avec le bouton droit sur **Toutes
    les définitions de build**, puis cliquez sur **Nouvelle définition de build** :

    ![][0]

2.  Cliquez sur l'onglet **Processus**. Sous l'onglet Processus, choisissez le modèle
    par défaut, sous Éléments à générer,
    choisissez le projet et développez la section **Avancé** de la grille.

3.  Choisissez **Arguments MSBuild** et définissez les arguments de
    ligne de commande MSBuild comme décrit à l'étape 2 plus haut. Par exemple,
    saisissez **/t:Publish /p:PublishDir=\\\\myserver\\drops\\** pour générer un
    package et copier les fichiers associés dans
    \\\\myserver\\drops\\ :

    ![][1]

    **Remarque :** le fait de copier les fichiers dans un partage public simplifie
    le déploiement manuel des packages à partir de votre ordinateur de développement.

4.  Cliquez sur l'onglet **Déclencher** et spécifiez les conditions pour
    lesquelles le package doit être généré. Par exemple, spécifiez
    **Intégration continue** pour générer le package à chaque intégration
    de contrôle du code source.

5.  Choisissez l'onglet **Valeurs par défaut des builds**, et sous Contrôleur de build, vérifiez
    le nom du serveur de builds. De même, choisissez l'option **Copier la sortie
    de la génération dans le dossier de dépôt suivant** et spécifiez l'emplacement
    souhaité.

6.  Testez le fonctionnement de votre processus en intégrant une modification à votre
    projet ou en ajoutant une build dans la file d'attente. Pour ajouter une nouvelle build dans la file d'attente, dans
    Explorateur d'équipes, cliquez avec le bouton droit sur **Toutes les définitions de build**, puis
    choisissez **Mettre la nouvelle build en file d'attente**.

## <a name="step4"> </a><span class="short-header">Publication avec Powershell</span>Étape 4 : publication d'un package à l'aide d'un script PowerShell

Cette section décrit la création d'un script Windows PowerShell qui
publie le résultat du package de l'application cloud dans Azure à l'aide
de paramètres facultatifs. Ce script peut être appelé après l'étape de compilation
dans votre automatisation de build personnalisée. Il peut également être appelé depuis
les activités de workflow du modèle de processus dans Visual Studio TFS Team Build.

1.  Installez les [cmdlets Azure PowerShell][cmdlets Azure PowerShell] (v0.6.1 ou version ultérieure).
    Au cours de la phase de configuration des cmdlets, choisissez l'installation comme composant logiciel enfichable.
    Cette installation, officiellement prise en charge, remplace l'ancienne version
    proposée via CodePlex, bien que les versions étaient numérotées 2.x.x.

2.  Démarrez Azure PowerShell dans le menu Démarrer. Si vous démarrez de cette façon,
    les cmdlets Azure PowerShell sont chargées.

3.  À l'invite de commandes PowerShell, vérifiez que les cmdlets PowerShell sont bien chargées
    en tapant la commande partielle Get-Azure et en appuyant sur la touche Tab pour compléter
    l'instruction.

    Vous devez voir différentes commandes Azure PowerShell.

4.  Vérifiez que vous pouvez vous connecter à votre abonnement Azure en
    important vos informations d'abonnement à partir du fichier .publishsettings.

    Import-AzurePublishSettingsFile c:\\scripts\\WindowsAzure\\default.publishsettings

    Entrez ensuite la commande

    Get-AzureSubscription

    Ceci affiche les informations sur votre abonnement. Vérifiez que tout est correct.

5.  Enregistrez le modèle de script fourni [à la fin de cet article][à la fin de cet article] dans
    votre dossier de scripts sous
    c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.

6.  Vérifiez la section des paramètres de ce script. Ajoutez des valeurs ou
    modifiez les valeurs par défaut. Ces valeurs peuvent de toute manière être ignorées en
    indiquant des paramètres explicites.

7.  Assurez-vous que les comptes valides de service cloud et de stockage créés
    dans votre abonnement peuvent être utilisés par le script de publication. Le
    compte de stockage (stockage d'objets blob) est utilisé pour télécharger et stocker
    de façon temporaire le package de déploiement et le fichier de configuration pendant
    la création du déploiement.

    -   Pour créer un service cloud, vous pouvez appeler ce script ou utiliser
        le portail de gestion Azure. Le nom du service cloud
        sera utilisé comme préfixe dans le nom de domaine complet.
        Il doit donc être unique.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Pour créer un compte de stockage, vous pouvez appeler ce script ou utiliser
        le portail de gestion Azure. Le nom du compte de stockage
        sera utilisé comme préfixe dans le nom de domaine complet.
        Il doit donc être unique. Vous pouvez essayer d'utiliser le même nom que le
        service cloud.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

8.  Appelez ce script directement depuis Azure PowerShell ou ajoutez
    ce script à votre automatisation de build hôte afin qu'il arrive après la génération du
    package.

    **WARNING:** le script supprime ou remplace toujours vos déploiements
    existants par défaut s'ils sont détectés. Ceci est nécessaire pour
    permettre la remise continue automatique là où il n'est pas possible de demander
    à l'utilisateur d'intervenir.

    **Exemple de scénario 1 :** déploiement continu d'un service dans
    l'environnement intermédiaire :

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    Cette opération est normalement suivie d'un test de vérification et d'un échange d'adresses
    IP virtuelles. Cet échange d'adresses IP virtuelles peut se faire via le
    portail de gestion Azure ou à l'aide de la cmdlet Move-Deployment.

    **Exemple de scénario 2 :** déploiement continu d'un service de test
    dédié dans l'environnement de production

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Bureau à distance :**

    Si le Bureau à distance est activé dans votre projet Azure, vous
    devrez effectuer des opérations supplémentaires pour vous assurer
    que le bon certificat de service cloud est téléchargé dans tous les services cloud ciblés
    par ce script.

    Recherchez les valeurs d'empreinte numérique de certificat attendues pour vos rôles. Ces
    valeurs sont visibles dans la section Certificats du fichier de configuration de
    cloud (ServiceConfiguration.Cloud.cscfg). Elles
    sont également visibles dans la boîte de dialogue Configuration Bureau à distance de Visual
    Studio lorsque vous affichez les options et consultez le certificat sélectionné.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Téléchargez les certificats Bureau à distance (opération de configuration unique)
    à l'aide du script de cmdlet suivant :

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MYAdd-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)lt;THUMBPRINT>)

    Par exemple :

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    Vous pouvez également exporter le fichier de certificat PFX avec une clé privée
    et télécharger les certificats sur chaque service cloud ciblé
    à l'aide du portail de gestion Azure. Pour plus d'informations, consultez l'article
    suivant :
    [<http://msdn.microsoft.com/fr-fr/library/windowsazure/gg443832.aspx>][<http://msdn.microsoft.com/fr-fr/library/windowsazure/gg443832.aspx>].

    **Mise à niveau du déploiement et suppression du déploiement -\> Nouveau déploiement**

    Le script exécute par défaut un déploiement de mise à niveau
    ($enableDeploymentUpgrade = 1) si aucun paramètre n'est transmis ou si la
    valeur 1 est transmise de manière explicite. Pour les instances uniques, ceci présente
    l'avantage de prendre moins de temps qu'un déploiement complet. Pour les instances
    qui ont besoin d'une haute disponibilité, ceci présente également l'avantage de
    laisser s'exécuter certaines instances pendant que d'autres sont mises à niveau (mise à niveau
    progressive du domaine). De plus, votre adresse IP virtuelle n'est pas supprimée.

    Le déploiement de mise à niveau peut être désactivé dans le script
    ($enableDeploymentUpgrade = 0) ou en transmettant
    le paramètre -enableDeploymentUpgrade 0, ce qui modifie le comportement du
    script : il supprime d'abord les déploiements existants, puis
    crée un nouveau déploiement.

    **Avertissement :** le script supprime ou remplace toujours vos déploiements
    existants par défaut s'ils sont détectés. Ceci est nécessaire pour
    permettre la remise continue automatique là où il n'est pas possible de demander
    à l'utilisateur ou à l'opérateur d'intervenir.

## <a name="step5"> </a><span class="short-header">Publication avec TFS </span>Étape 5 : publication d'un package à l'aide de TFS Team Build (facultatif)

Cette étape permet de lier TFS Team Build au script créé à l'étape 4,
qui gère la publication du package généré dans Azure. Ceci
implique de modifier le modèle de processus utilisé par votre définition de build afin
qu'il exécute une activité Publish à la fin du workflow. Cette activité
Publish exécute votre commande PowerShell en transmettant des paramètres à partir
de la build. La sortie des cibles MSBuild et du script de publication sera
intégré à la sortie de génération standard.

1.  Modifiez la définition de build responsable du déploiement continu.

2.  Sélectionnez l'onglet **Process**.

3.  Créez un modèle de processus en cliquant sur le bouton New... dans la zone
    Process Template Selection de l'onglet. La boîte de dialogue
    New Build Process Template se charge. Dans cette boîte de dialogue
    , assurez-vous que l'option Copy an Existing XAML est sélectionnée et accédez au modèle de processus à
    copier si vous souhaitez changer la base par défaut. Entrez
    le nom du nouveau modèle, par exemple DefaultTemplateAzure.

4.  Ouvrez le modèle de processus sélectionné pour le modifier. Vous pouvez l'ouvrir
    directement dans le concepteur de workflow ou dans l'éditeur XML
    pour modifier le XAML.

5.  Ajoutez la liste de nouveaux arguments suivante, en tant que lignes distinctes dans
    l'onglet des arguments du concepteur de workflow. Tous ces arguments doivent
    avoir direction=In et type=String. Elles sont utilisées pour passer
    les paramètres de la définition de build vers le workflow, qui est ensuite
    utilisé pour appeler le script de publication.

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![][2]

    Le code XAML correspondant ressemble à ceci :

        <Activity  _ />
          <x:Members>
            <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
            <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
            <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
            <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
            <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
            <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
            <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
            <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
            <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
            <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
            <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
            <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
            <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
            <x:Property Name="GetVersion" Type="InArgument(x:String)" />
            <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
            <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
            <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
            <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
            <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
            <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
            <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
            <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
            <x:Property Name="Environment" Type="InArgument(x:String)" />
            <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
            <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
            <x:Property Name="ServiceName" Type="InArgument(x:String)" />
          </x:Members>

          <this:Process.MSBuildArguments>

6.  Ajoutez une nouvelle séquence à la fin de Exécuter sur l'agent :

    1.  Commencez par ajouter une activité d'instruction If pour vérifier la présence d'un fichier
        de script valide. Définissez la valeur suivante pour la condition :

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  Dans le cas Then de l'instruction If, ajoutez une nouvelle activité
        Sequence. Définissez le nom d'affichage sur « Start publish »

    3.  La séquence Start publish toujours sélectionnée, ajoutez la
        liste de nouvelles variables suivante, en tant que lignes distinctes dans l'onglet des
        variables du concepteur de workflow. Toutes les variables doivent
        comporter type =String et Scope=Start publish. Elles sont
        utilisées pour passer les paramètres de la définition de build vers le
        workflow, qui est ensuite utilisé pour appeler le script de publication.

        -   SubscriptionDataFilePath, de type String

        -   PublishScriptFilePath, de type String

            ![][3]

    4.  Ajoutez une activité ConvertWorkspaceItem au début de la nouvelle partie
        Sequence. Direction=ServerToLocal, DisplayName='Convert publish
        script filename', Input=' PublishScriptLocation',
        Result='PublishScriptFilePath', Workspace='Workspace'. Cette
        activité convertit le chemin vers le script de publication des emplacements
        de serveur TFS (si applicable) en chemin d'accès vers le disque local.

    5.  Ajoutez une autre activité ConvertWorkspaceItem à la fin de la nouvelle partie
        Sequence. Direction=ServerToLocal, DisplayName='Convert
        subscription filename', Input=' SubscriptionDataFileLocation',
        Result= 'SubscriptionDataFilePath', Workspace='Workspace'.

    6.  Ajoutez une activité InvokeProcess à la fin de la nouvelle partie Sequence.
        Cette activité appelle PowerShell.exe avec les arguments transmis
        dans la définition de build.

        1.  Arguments = String.Format(" -File ""{0}"" -serviceName {1}
            -storageAccountName {2} -packageLocation ""{3}""
            -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}""
            -selectedSubscription {6} -environment ""{7}""",
            PublishScriptFilePath, ServiceName, StorageAccountName,
            PackageLocation, CloudConfigLocation,
            SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName ='Execute publish script'

        3.  FileName = "PowerShell"

        4.  OutputEncoding=
            'System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)'

    7.  Dans la zone de texte de la section Handle Standard Output
        de InvokeProcess, définissez la valeur sur « data ». Cette
        variable permet de stocker les données de sortie standard.

    8.  Ajoutez une activité WriteBuildMessage juste en dessous de la section de sortie
        standard. Définissez Importance =
        'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High'
        et Message='data'. Ceci permet que la sortie standard du
        script soit écrite dans la sortie de génération.

    9.  Dans la zone de texte de la section Handle Standard Error de
        InvokeProcess, définissez la valeur sur « data ». Cette variable
        permet de stocker les données d'erreur standard.

    10. Ajoutez une activité WriteBuildError juste en dessous de la section de sortie
        standard. Définissez Message='data'. Ceci permet d'écrire les erreurs
        standard du script dans la sortie d'erreur de génération.

    Le résultat final des activités du workflow de publication doit ressembler
    à ceci dans le concepteur :

    ![][4]

    Le résultat final des activités du workflow de publication doit ressembler à ceci
    dans le XAML :

            </TryCatch>
              <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap:VirtualizedContainerService.HintSize="1539,552">
                <If.Then>
                  <Sequence DisplayName="Start publish" sap:VirtualizedContainerService.HintSize="297,446">
                    <Sequence.Variables>
                      <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
                      <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                    </Sequence.Variables>
                    <sap:WorkflowViewStateService.ViewState>
                      <scg:Dictionary x:TypeArguments="x:String, x:Object">
                        <x:Boolean x:Key="IsExpanded">True</x:Boolean>
                      </scg:Dictionary>
                    </sap:WorkflowViewStateService.ViewState>
                    <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap:VirtualizedContainerService.HintSize="234,22" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
                    <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription data file filename" sap:VirtualizedContainerService.HintSize="234,22" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
                    <mtbwa:InvokeProcess Arguments="[String.Format(" -File ""{0}"" -serviceName {1} -storageAccountName {2} -packageLocation ""{3}"" -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}"" -selectedSubscription {6} -environment ""{7}""", PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="Execute publish script" FileName="PowerShell" sap:VirtualizedContainerService.HintSize="234,198">
                      <mtbwa:InvokeProcess.ErrorDataReceived>
                        <ActivityAction x:TypeArguments="x:String">
                          <ActivityAction.Argument>
                            <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                          </ActivityAction.Argument>
                          <mtbwa:WriteBuildError sap:VirtualizedContainerService.HintSize="200,22" Message="[data]" />
                        </ActivityAction>
                      </mtbwa:InvokeProcess.ErrorDataReceived>
                      <mtbwa:InvokeProcess.OutputDataReceived>
                        <ActivityAction x:TypeArguments="x:String">
                          <ActivityAction.Argument>
                            <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                          </ActivityAction.Argument>
                          <mtbwa:WriteBuildMessage sap:VirtualizedContainerService.HintSize="200,22" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                        </ActivityAction>
                      </mtbwa:InvokeProcess.OutputDataReceived>
                    </mtbwa:InvokeProcess>
                  </Sequence>
                </If.Then>
              </If>
            </mtbwa:AgentScope>
            <mtbwa:InvokeForReason DisplayName="Check In Gated Changes for CheckInShelveset Builds" sap:VirtualizedContainerService.HintSize="1370,146" Reason="CheckInShelveset">
              <mtbwa:CheckInGatedChanges DisplayName="Check In Gated Changes" sap:VirtualizedContainerService.HintSize="200,22" />
            </mtbwa:InvokeForReason>
          </Sequence>
        </Activity>

7.  Enregistrez le workflow DefaultTemplateAzure et archivez ce fichier.

8.  Sélectionnez le modèle de processus DefaultTemplateAzure dans votre définition
    de build. Cliquez sur Refresh ou sur le bouton New si vous ne voyez
    pas encore ce fichier dans la liste des modèles de processus.

9.  Définissez les valeurs de la propriété du paramètre dans la section Misc comme suit :

    1.  CloudConfigLocation =
        'c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg'
        *Cette valeur est dérivée de :
        ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation =
        'c:\\drops\\app.publish\\ContactManager.Azure.cspkg'
        *Cette valeur est dérivée de : ($PublishDir)($ProjectName).cspkg*

    3.  PublishScriptLocation =
        'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename'
        *Utilisez le nom du service cloud correspondant*

    5.  Environment = 'Staging'

    6.  StorageAccountName = 'mystorageaccountname'
        *Utilisez le nom du compte de stockage correspondant*

    7.  SubscriptionDataFileLocation =
        'c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![][5]

10. Enregistrez les modifications apportées à la définition de build.

11. Ajoutez une build à la file d'attente pour exécuter à la fois la génération et la publication du package. Si un
    déclencheur est défini sur Continuous Deploy, vous exécuterez ce
    comportement à chaque intégration.

### <a name="script"> </a>Modèle de script PublishCloudService.ps1

    Param(  $serviceName = "",
            $storageAccountName = "",
            $packageLocation = "",
            $cloudConfigLocation = "",
            $environment = "Staging",
            $deploymentLabel = "ContinuousDeploy to $servicename",
            $timeStampFormat = "g",
            $alwaysDeleteExistingDeployments = 1,
            $enableDeploymentUpgrade = 1,
            $selectedsubscription = "default",
            $subscriptionDataFile = ""
         )
          

    function Publish()
    {
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue 
        if ($a[0] -ne $null)
        {
            Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
        }
        #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
        if ($deployment.Name -ne $null)
        {
            switch ($alwaysDeleteExistingDeployments)
            {
                1 
                {
                    switch ($enableDeploymentUpgrade)
                    {
                        1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                        {
                            Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                            UpgradeDeployment
                        }
                        0  #Delete then create new deployment
                        {
                            Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                            DeleteDeployment
                            CreateNewDeployment
                            
                        }
                    } # switch ($enableDeploymentUpgrade)
                }
                0
                {
                    Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                    exit
                }
            } #switch ($alwaysDeleteExistingDeployments)
        } else {
                CreateNewDeployment
        }
    }

    function CreateNewDeployment()
    {
        write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

        $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName
            
        $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $completeDeploymentID = $completeDeployment.deploymentid

        write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
        Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"
        
        StartInstances
    }

    function UpgradeDeployment()
    {
        write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

        # perform Update-Deployment
        $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force
        
        $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $completeDeploymentID = $completeDeployment.deploymentid
        
        write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
        Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
    }

    function DeleteDeployment()
    {

        write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

        #WARNING - always deletes with force
        $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

        write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
        Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"
        
    }

    function StartInstances()
    {
        write-progress -id 4 -activity "Starting Instances" -status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $runstatus = $deployment.Status

        if ($runstatus -ne 'Running') 
        {
            $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
        }
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $oldStatusStr = @("") * $deployment.RoleInstanceList.Count
        
        while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
        {
            $i = 1
            foreach ($roleInstance in $deployment.RoleInstanceList)
            {
                $instanceName = $roleInstance.InstanceName
                $instanceStatus = $roleInstance.InstanceStatus

                if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
                {
                    $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
                }

                write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
                $i = $i + 1
            }

            sleep -Seconds 1

            $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        }

        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            $i = $i + 1
        }
        
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $opstat = $deployment.Status 
        
        write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
        Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
    }

    function AllInstancesRunning($roleInstanceList)
    {
        foreach ($roleInstance in $roleInstanceList)
        {
            if ($roleInstance.InstanceStatus -ne "ReadyRole")
            {
                return $false
            }
        }
        
        return $true
    }

    #configure powershell with Azure 1.7 modules
    Import-Module Azure

    #configure powershell with publishsettings for your subscription
    $pubsettings = $subscriptionDataFile
    Import-AzurePublishSettingsFile $pubsettings
    Set-AzureSubscription -CurrentStorageAccount $storageAccountName -SubscriptionName $selectedsubscription

    #set remaining environment variables for Azure cmdlets
    $subscription = Get-AzureSubscription $selectedsubscription
    $subscriptionname = $subscription.subscriptionname
    $subscriptionid = $subscription.subscriptionid
    $slot = $environment

    #main driver - publish & write progress to activity log
    Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
    Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

    Publier

    $deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
    $deploymentUrl = $deployment.Url

    Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
    Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."

## Étapes suivantes

Pour activer le débogage distant lors de l'utilisation de la remise continue, consultez [les instructions suivantes][les instructions suivantes].

  [Remise continue pour Azure avec Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso/
  [Étape 1 : configuration du serveur de builds]: #step1
  [Étape 2 : génération d'un package à l'aide des commandes MSBuild]: #step2
  [Étape 4 : publication d'un package à l'aide d'un script PowerShell]: #step4
  [Team Foundation Build Service]: http://go.microsoft.com/fwlink/p/?LinkId=239963
  [.NET Framework 4]: http://go.microsoft.com/fwlink/?LinkId=239538
  [.NET Framework 4.5]: http://go.microsoft.com/fwlink/?LinkId=245484
  [Azure Authoring Tools]: http://go.microsoft.com/fwlink/?LinkId=239600
  [bibliothèques Windows Azure]: http://go.microsoft.com/fwlink/?LinkId=257862
  [Référence de la ligne de commande MSBuild]: http://msdn.microsoft.com/fr-fr/library/ms164311(v=VS.90).aspx
  [Vue d'ensemble de Team Foundation Build]: http://go.microsoft.com/fwlink/?LinkId=238798
  [Configuration d'un ordinateur de builds]: http://go.microsoft.com/fwlink/?LinkId=238799
  [0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01.png
  [1]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [cmdlets Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=256262
  [à la fin de cet article]: #script
  [2]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
  [les instructions suivantes]: http://go.microsoft.com/fwlink/p/?LinkID=402354
