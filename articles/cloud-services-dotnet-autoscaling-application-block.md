<properties 
	pageTitle="Utilisation du bloc applicatif de mise à l'échelle automatique (.NET) - Azure" 
	description="Découvrez comment utiliser l'application de mise à l'échelle automatique pour Azure. Les exemples de code sont écrits en C# et utilisent l'API .NET." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="squillace" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/14/2014" 
	ms.author="rasquill"/>







# Utilisation du bloc applicatif de mise à l'échelle automatique

Ce guide décrit le déroulement des scénarios courants dans le cadre de l'utilisation du
bloc applicatif de mise à l'échelle automatique de [Microsoft Enterprise Library 5.0
Integration Pack for Azure][]. Les exemples sont écrits en code C\#
et utilisent l'API .NET. Les scénarios traités incluent l'**hébergement du
bloc**, **l'utilisation de règles de contrainte** et **l'utilisation de règles réactives**. Pour
plus d'informations sur le bloc applicatif de mise à l'échelle automatique, consultez la section [Étapes suivantes][].

## Sommaire

[Présentation du bloc applicatif de mise à l'échelle automatique][]   
 [Concepts][]   
 [Collecte de données de compteurs de performances à partir de votre application Azure cible][]   
 [Configuration d'une application hôte pour le bloc applicatif de mise à l'échelle automatique][]   
 [Procédure : instanciation et exécution de la classe Autoscaler][] [Procédure : définition d'un modèle de service][]   
 [Procédure : définition de règles de mise à l'échelle automatique][]   
 [Procédure : configuration du bloc applicatif de mise à l'échelle automatique][]   
 [Étapes suivantes][]

## <a id="WhatIs"> </a>Présentation du bloc applicatif de mise à l'échelle automatique

Le bloc applicatif de mise à l'échelle automatique vous permet de mettre automatiquement à l'échelle votre
application Windows Azure en fonction de règles que vous définissez spécifiquement pour cette
application. Vous pouvez utiliser ces règles pour
préserver le rendement de votre application Azure face à l'évolution de sa
charge de travail, tout en contrôlant les coûts liés
à l'hébergement de votre application dans Azure. Outre la mise à l'échelle par
augmentation ou diminution du nombre d'instances de rôle dans votre
application, le bloc vous permet également d'utiliser d'autres actions de mise à l'échelle
telles que la limitation de certaines fonctionnalités dans votre application ou
l'utilisation d'actions personnalisées.

Vous pouvez choisir d'héberger le bloc dans un rôle Azure ou dans une
application locale.

Le bloc applicatif de mise à l'échelle automatique fait partie intégrante de [Microsoft Enterprise Library 5.0 Integration Pack for Azure][].

## <a id="Concepts"> </a>Concepts

Dans le schéma suivant, le trait vert représente le nombre
d'instances exécutées d'un rôle Azure sur deux jours. Le nombre
d'instances change automatiquement dans le temps sous l'effet d'un
ensemble de règles de mise à l'échelle automatique.

![diagram of sample autoscaling](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling01.png)

Le bloc utilise deux types de règles pour définir le comportement de mise à l'échelle automatique de
votre application :

-   **Règles de contrainte :** celles-ci permettent de définir les limites supérieure et inférieure du nombre
    d'instances. Par exemple, si tous les matins entre 8h00 et 10h00,
    vous souhaitez un minimum de quatre instances et un maximum de six instances,
    vous utilisez une **règle de contrainte**. Dans le schéma, les traits rouge et bleu
    représentent des règles de contrainte. Par exemple, au point **A** du
    schéma, le nombre minimal d'instances de rôle passe de deux à
    quatre pour s'adapter à l'augmentation prévue de la
    charge de travail de l'application à cette heure-là. Au point **B** du schéma,
    le nombre d'instances de rôle est limité à cinq
    dans le but de contrôler les coûts d'exécution de l'application.

-   **Règles réactives :** pour adapter le nombre d'instances de rôle
    à des évolutions imprévisibles de la demande, vous devez utiliser des **règles
    réactives**. Au point **C** du schéma, le bloc abaisse automatiquement
    le nombre d'instances de rôle de quatre à trois en
    réponse à une diminution de la charge de travail. Au point **D**, le bloc
    détecte une augmentation de la charge de travail et fait automatiquement passer le
    nombre d'instances de rôle en cours d'exécution de trois à quatre.

Le bloc stocke ses paramètres de configuration dans deux magasins :

-   **Magasin de règles :** le magasin de règles renferme la configuration de votre entreprise, c'est-à-dire
    la liste de toutes les règles de mise à l'échelle automatique que vous avez définies pour votre
    application Windows Azure. En règle générale, ce magasin est un fichier XML
    situé à un emplacement auquel le bloc applicatif de mise à l'échelle automatique peut accéder en lecture.
    Il peut s'agir, par exemple, d'un stockage d'objets blob Azure ou d'un fichier local.

-   **Magasin d'informations de service :** ce magasin stocke
    votre configuration opérationnelle, à savoir, le modèle de service de votre
    application Windows Azure. Il contient toutes les
    informations sur votre application Azure (telles que les noms de rôles
    et les détails de compte de stockage) dont a besoin le bloc pour pouvoir
    collecter les points de données de l'application Azure cible et
    effectuer des opérations de mise à l'échelle.

## <a id="PerfCounter"> </a>Collecte de données de compteurs de performances à partir de votre application Azure cible

Les règles réactives peuvent utiliser les données de compteurs de performances des rôles dans le cadre de
la définition des règles. Par exemple, une règle réactive peut surveiller l'utilisation
processeur d'un rôle Azure pour déterminer si le bloc
doit lancer une opération de mise à l'échelle. Le bloc lit les données de compteurs de
performances de la table de diagnostics Azure nommée
**WADPerformanceCountersTable** dans le stockage Azure.

Par défaut, Azure n'écrit pas les données de compteurs de performances dans la
table de diagnostics dans Azure Storage. Par conséquent, vous
devez modifier les rôles à partir desquels vous devez collecter les données
de compteurs de performances, pour enregistrer ces données. Pour plus d'informations sur l'activation
des compteurs de performances dans votre application, consultez la page [Utilisation de compteurs de performances dans Azure][].

## <a id="CreateHost"> </a>Configuration d'une application hôte pour le bloc applicatif de mise à l'échelle automatique

Vous pouvez héberger le bloc applicatif de mise à l'échelle automatique dans un rôle
Azure ou dans une application locale. Il est généralement
hébergé dans une application distincte de l'application
cible que vous souhaitez mettre automatiquement à l'échelle. Cette section fournit des
indications sur la configuration de votre application hôte.

### Obtention du package NuGet de bloc applicatif de mise à l'échelle automatique

Pour pouvoir utiliser le bloc applicatif de mise à l'échelle automatique dans votre projet Visual
Studio, vous devez obtenir les fichiers binaires
associés et les ajouter à votre projet. L'extension
Visual Studio NuGet facilite l'installation et la mise à jour des bibliothèques
et des outils de Visual Studio et Visual Web Developer. Le package NuGet
de bloc applicatif de mise à l'échelle automatique est le moyen le plus simple de se procurer les 
API associées au bloc. Pour plus d'informations sur NuGet **sur**
l'installation et l'utilisation de l'extension Visual Studio **NuGet**, consultez le site Web [NuGet][]
.

Une fois que le Gestionnaire de package NuGet est installé, pour installer le package NuGet de mise à l'échelle automatique
dans votre application, procédez comme suit :

1.  Ouvrez la fenêtre **NuGet Package Manager Console**. Dans le menu **Tools**,
    sélectionnez **Library Package Manager**, puis **Package
    Console Manager**.

2.  Entrez la commande suivante dans la fenêtre de la console du gestionnaire du package
    NuGet :

        PM> Install-Package EnterpriseLibrary.WindowsAzure.Autoscaling

L'installation du package NuGet a pour effet de mettre à jour votre projet avec l'ensemble des
assemblys et des références nécessaires à l'utilisation du bloc applicatif
de mise à l'échelle automatique. Votre projet comprend dès lors les fichiers de schéma XML pour
les définitions de règle de mise à l'échelle et les informations de service de mise à l'échelle.
Il inclut également un fichier lisezmoi (readme) qui contient des informations importantes
sur le bloc applicatif de mise à l'échelle automatique :

![files configured by autoscaling NuGet package](./media/cloud-services-dotnet-autoscaling-application-block/auotscaling02.png)


### Définition de l'infrastructure cible .NET Framework 4

Votre projet doit donc viser .NET Framework 4. Pour modifier ou vérifier
l'infrastructure cible :

1.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez
    **Propriétés**.

2.  Dans l'onglet **Application** de la fenêtre Propriétés, assurez-vous que la version cible définie de l'infrastructure est bien **.NET Framework 4**.

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling03.png)


### Ajout de références à l'espace de noms

Ajoutez les déclarations d'espace de noms suivantes au début de chaque fichier C\#
dans lequel vous souhaitez accéder au bloc applicatif de mise à l'échelle automatique par
programme :

    using Microsoft.Practices.EnterpriseLibrary.Common.Configuration;
    using Microsoft.Practices.EnterpriseLibrary.WindowsAzure.Autoscaling;

## <a id="Instantiate"> </a>Procédure : instanciation et exécution de la classe Autoscaler

Utilisez la méthode **IServiceLocator.GetInstance** pour instancier la classe
Autoscaler, puis appelez la méthode **Autoscaler.Start** pour exécuter
**Autoscaler**.

    Autoscaler scaler =
        EnterpriseLibraryContainer.Current.GetInstance<Autoscaler>();
    scaler.Start();

## <a id="DefineServiceModel"> </a>Procédure : Définition d'un modèle de service

En règle générale, les modèles de service (description de l'environnement Windows
Azure avec notamment des informations sur les abonnements, les services hébergés,
les rôles et les comptes de stockage) sont stockés dans un fichier XML. Vous pouvez trouver une
copie du schéma de ce fichier XML dans le fichier
**AutoscalingServiceModel.xsd** de votre projet. Dans Visual Studio,
ce schéma propose Intellisense et assure la validation lors de la modification
d'un fichier XML de modèle de service.

Créez dans votre projet un fichier XML nommé **services.xml**.

Dans Visual Studio, vous devez vous assurer que le fichier de modèle de service est copié
dans le dossier de sortie. Pour ce faire :

1.  Cliquez avec le bouton droit sur le fichier, puis sélectionnez **Propriétés**.

2.  Dans le volet Propriétés, définissez la valeur de **Copier dans le répertoire de sortie**
    sur **Toujours copier**.

    ![Set Copy to Output Directory value](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling04.png)


	L'exemple de code suivant montre un exemple de modèle de service dans un fichier **services.xml** :

    <?xml version="1.0" encoding="utf-8" ?>
    <serviceModel xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/serviceModel">
      <subscriptions>
        <subscription name="[subscriptionname]"
                      certificateThumbprint="[managementcertificatethumbprint]"
                      subscriptionId="[subscriptionid]"
                      certificateStoreLocation="CurrentUser"
                      certificateStoreName="My">
          <services>
            <service dnsPrefix="[hostedservicednsprefix]" slot="Staging">
              <roles>
                <role alias="AutoscalingApplicationRole"
                      roleName="[targetrolename]"
                      wadStorageAccountName="targetstorage"/>
              </roles>
            </service>
          </services>
          <storageAccounts>
            <storageAccount alias="targetstorage"
              connectionString="DefaultEndpointsProtocol=https;AccountName=[storageaccountname];AccountKey=[storageaccountkey]">
            </storageAccount>
          </storageAccounts>
        </subscription>
      </subscriptions>
    </serviceModel>

Vous devez remplacer les valeurs entre crochets par des valeurs propres à
votre environnement et à votre application cible. Pour trouver la plupart de ces valeurs,
vous devez vous connecter au [portail de gestion Azure][].

Connectez-vous au portail de gestion.

-   **[subscriptionname] :** choisissez un nom convivial pour faire référence à
    l'abonnement Azure qui contient l'application dans laquelle
    vous souhaitez utiliser la mise à l'échelle automatique.

-   **[subscriptionId] :** ID unique de
    l'abonnement Azure qui contient l'application dans laquelle
    vous souhaitez utiliser la mise à l'échelle automatique.

    1.  Dans le portail de gestion Azure, cliquez sur
        **Cloud Services**.

    2.  Dans la liste Cloud Services, cliquez sur le service qui héberge
        l'application dans laquelle vous souhaitez utiliser la mise à l'échelle automatique. Le
        volet Aperçu rapide à droite affiche
        **l'ID d'abonnement**.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling05.png)

  
	-   **[hostedservicednsprefix] :** préfixe DNS du service hébergé dans lequel vous souhaitez utiliser la mise à l'échelle automatique.

    1.  Dans le portail de gestion Azure, cliquez sur
        **Cloud Services**.

    2.  Dans la liste Cloud Services, repérez le service qui héberge
        l'application dans laquelle vous souhaitez utiliser la mise à l'échelle automatique. Le nom du
        cloud serviceClick est le **préfixe DNS**.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling06.png)
 
	-   **[targetrolename] :** nom du rôle correspondant à la cible de vos règles de mise à l'échelle automatique.

    1.  Dans le portail de gestion Azure, cliquez sur
        **Cloud Services**.

    2.  Dans la liste Cloud Services, cliquez sur le service qui héberge
        l'application dans laquelle vous souhaitez utiliser la mise à l'échelle automatique, puis cliquez sur
        **Instances**. La colonne **Role* affiche le nom de votre rôle
        cible.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling07.png)


	-   **[storageaccountname]** et**[storageaccountkey] :**nom du compte de stockage Azure que vous utilisez pour votre application Azure cible.

    1.  Dans le portail de gestion Azure, cliquez sur
        **Stockage**.

    2.  Dans la liste Comptes de stockage, sélectionnez  le compte de stockage que vous utilisez. La colonne
        **Nom** colonne afficher le **nom**.

    3.  Cliquez sur le bouton **Gérer les clés** au bas de l'écran
        pour obtenir la clé d'accès primaire.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling08.png)
  
 
	-   **[managementcertificatethumbprint]:** **empreinte** du certificat de gestion que le bloc utilisera pour sécuriser les demandes de mise à l'échelle à destination de l'application cible.

    1.  Dans le portail de gestion Azure, cliquez sur
        **Paramètres**.

    2.  La colonne **Empreinte** affiche l'**empreinte**.

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling09.png)
 

Pour plus d'informations sur le contenu du fichier de modèle de service, consultez la page
[Stockage de vos données d'informations de service][].

## <a id="DefineAutoscalingRules"> </a>Procédure : Définition de règles de mise à l'échelle automatique

En principe, les règles de mise à l'échelle automatique qui contrôlent le nombre
d'instances de rôle dans une application cible sont stockées dans un fichier XML. Vous pouvez trouver une
copie du schéma de ce fichier XML dans le fichier **AutoscalingRules.xsd**
de votre projet. Dans Visual Studio, ce schéma propose
Intellisense et assure la validation lors de la modification d'un fichier XML.

Créez dans votre projet un fichier XML nommé **rules.xml**.

Dans Visual Studio, vous devez vous assurer que le fichier de règles est copié dans le
dossier de sortie. Pour ce faire :

1.  Cliquez avec le bouton droit sur le fichier, puis sélectionnez **Propriétés**.

2.  Dans le volet Propriétés, définissez la valeur de **Copier dans le répertoire de sortie**
    sur **Toujours copier**.

L'exemple de code suivant montre un exemple de règle définie dans un fichier **rules.xml**
:

    <?xml version="1.0" encoding="utf-8" ?>
    <rules xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/rules">
      <constraintRules>
        <rule name="default" enabled="true" rank="1" description="The default constraint rule">
          <actions>
            <range min="2" max="6" target="AutoscalingApplicationRole"/>
          </actions>
        </rule>
      </constraintRules>
      <reactiveRules>
        <rule name="ScaleUpOnHighUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <any>
               <greaterOrEqual operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </any>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="1"/>
          </actions>
        </rule>
        <rule name="ScaleDownOnLowUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <all>
              <less operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </all>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="-1"/>
          </actions>
        </rule>
      </reactiveRules>
      <operands>
        <performanceCounter alias="WebRoleA_CPU_Avg_5m"
          performanceCounterName="\Processor(_Total)\% Processor Time"
          source ="AutoscalingApplicationRole"
          timespan="00:05:00" aggregate="Average"/>
      </operands>
    </rules>

Dans cet exemple, les règles de mise à l'échelle automatique sont au nombre de trois : une **règle
de contrainte** et deux **règles réactives**. Elles opèrent sur une cible nommée
**AutoscalingApplicationRole** qui est l'alias d'un rôle défini dans
le **modèle de service** :

-   La règle de contrainte est toujours active et définit le nombre minimal
    d'instances de rôle sur deux et le nombre maximal d'instances de rôle sur
    six.

-   Les deux règles réactives utilisent un **opérande** appelé
    **WebRoleA\_CPU\_Avg\_5m** qui calcule l'utilisation UC moyenne
    au cours des cinq dernières minutes pour un rôle Azure nommé
    **AutoscalingApplicationRole.** Ce rôle est défini dans le
    **modèle de service**.

-   La règle réactive nommée **ScaleUpOnHighUtilization** incrémente le
    nombre d'instances du rôle cible d'une unité si l'utilisation UC moyenne
    au cours des cinq dernières minutes a été supérieure
    ou égale à 60 %.

-   La règle réactive nommée **ScaleDownOnLowUtilization** décrémente le
    nombre d'instances du rôle cible d'une unité si l'utilisation UC moyenne
    au cours des cinq dernières minutes a été inférieure à 60 %.

## <a id="Configure"> </a>Procédure : Configuration du bloc applicatif de mise à l'échelle automatique

Après avoir défini le modèle de service et les règles de mise à l'échelle automatique, vous
devez configurer le bloc applicatif de mise à l'échelle automatique pour qu'il les utilise. Ces
informations de configuration opérationnelle sont stockées dans le fichier de
configuration de l'application.

Par défaut, le bloc applicatif de mise à l'échelle automatique considère que
les règles de mise à l'échelle automatique et le modèle de service sont stockés dans des objets blob Azure. Dans cet
exemple, vous allez configurer le bloc pour qu'il les charge à partir du système de fichiers
local.

### Configuration du bloc applicatif de mise à l'échelle automatique dans l'application hôte

1.  Cliquez avec le bouton droit sur le fichier **App.config** dans l'Explorateur de solutions, puis
    cliquez sur **Modifier le fichier de configuration**.

2.  Dans le menu **Blocs**, cliquez sur **Ajouter des paramètres de mise à l'échelle automatique** :  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling10.png)
  
3.  Développez **Paramètres de mise à l'échelle automatique**, cliquez sur les points de suspension (...)
    en regard de **Compte de stockage du magasin de points de données**, ajoutez le **nom de
    compte** et la **clé de compte** du compte de stockage Azure
    dans lequel le bloc doit stocker les points de données qu'il collecte (consultez la section
    [Procédure : définition d'un modèle de service][] si vous ne savez pas exactement où
    se trouvent ces valeurs), puis cliquez sur **OK** :  

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling11.png)

4.  Développez la section **Paramètres de mise à l'échelle automatique** pour afficher les sections **Magasin
    de règles** et **Magasin d'informations de service**. Par défaut, elles
    sont configurées pour utiliser le stockage d'objets blob Azure :  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling12.png)


5.  Cliquez sur le signe plus (+) en regard de **Magasin de règles**, pointez sur **Définir
    le magasin de règles**, cliquez sur **Utiliser le magasin de règles de fichier local**, puis
    sur **Oui**.

6.  Dans la zone **Nom de fichier**, tapez **rules.xml**. Il s'agit du nom du
    fichier qui contient vos règles de mise à l'échelle automatique :  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling13.png)


7.  Cliquez sur le signe plus (+) en regard de **Magasin d'informations de service**, pointez
    sur **Définir le magasin d'informations de service**, cliquez sur **Utiliser le magasin
    d'informations de service de fichier local**, puis sur **Oui**.

8.  Dans la zone **Nom de fichier**, tapez **services.xml**. Il s'agit du nom du
    fichier qui contient vos règles de mise à l'échelle automatique :  
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling14.png)


9.  Dans la fenêtre Configuration de la bibliothèque d'entreprise, dans le menu **Fichier**,
    cliquez sur **Enregistrer** pour enregistrer les modifications de votre configuration. Ensuite, dans la
    fenêtre Configuration de la bibliothèque d'entreprise, dans le menu **Fichier**, cliquez sur
    **Quitter**.

Pour obtenir des informations détaillées sur les actions effectuées par le bloc applicatif
de mise à l'échelle automatique, vous devez capturer les messages de journal
qu'il écrit. Par exemple, si vous hébergez le bloc dans une application
console, vous pouvez afficher les messages de journal dans la fenêtre Sortie de
Visual Studio. La section suivante montre comment configurer ce
comportement.

### Configuration de la journalisation dans l'application hôte du bloc applicatif de mise à l'échelle automatique

1.  Dans Visual Studio, double-cliquez sur le fichier **App.config** dans
    l'Explorateur de solutions pour l'ouvrir dans l'éditeur. Ajoutez ensuite la section
    **system.diagnostics** comme indiqué dans l'exemple suivant :

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
          ...
          <system.diagnostics>
            <sources>
              <sourcename="Autoscaling General" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
              <sourcename="Autoscaling Updates" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
            </sources>
            <switches>
              <addname="SourceSwitch" value="Verbose, Information, Warning, Error, Critical" />
            </switches>
          </system.diagnostics>
        </configuration>

2.  Enregistrez vos modifications.

Vous pouvez à présent exécuter votre application console hôte de bloc applicatif de mise à l'échelle automatique
et observer la façon dont les règles de mise à l'échelle automatique fonctionnent avec votre application
Windows Azure cible. Lorsque vous exécutez l'application console hôte,
des messages analogues au suivant doivent s'afficher dans la fenêtre Sortie de
Visual Studio. Ces messages de journal vous aident à comprendre le comportement du
bloc. Par exemple, ils indiquent les règles qui sont mises en correspondance par
le bloc, ainsi que les actions que celui-ci effectue.

    Autoscaling General Verbose: 1002 : Rule match.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "MatchingRules":[{"RuleName":"default","RuleDescription":"The default constraint rule",
    "Targets":["AutoscalingApplicationRole"]},{"RuleName":"ScaleUp","RuleDescription":"Scale up the web role",
    "Targets":[]},{"RuleName":"ScaleDown","RuleDescription":"Scale up the web role","Targets":[]}]}

    Autoscaling Updates Verbose: 3001 : The current deployment configuration for a hosted service is about to be checked
    to determine if a change is required (for role scaling or changes to settings).
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{}}

    Autoscaling Updates Verbose: 3003 : Role scaling requests for hosted service about to be submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},
    "ScaleRequests":{"AutoscalingApplicationRole":
    {"Min":2,"Max":6,"AbsoluteDelta":0,"RelativeDelta":0,"MatchingRules":"default"}},
    "SettingChangeRequests":{},"InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{}}

    Autoscaling Updates Information: 3002 : Role configuration changes for deployment were submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{},
    "InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{},"RequestID":"f8ca3ada07c24559b1cb075534f02d44"}

## <a id="NextSteps"> </a>Étapes suivantes

Maintenant que vous avez appris les principes de base de l'utilisation du bloc applicatif de mise à l'échelle automatique,
suivez ces liens pour savoir comment implémenter des scénarios de mise à l'échelle
plus complexes :

-   [Hébergement du bloc applicatif de mise à l'échelle automatique dans un rôle de travail][]
-   [Implémentation d'un comportement de limitation][]
-   [Présentation des classements et de la conciliation des règles][]
-   [Développement et modification du bloc applicatif de mise à l'échelle automatique][]
-   [Utilisation du stabilisateur d'optimisation pour empêcher une oscillation des hautes fréquences et optimiser les coûts][]
-   [Utilisation des notifications et de la mise à l'échelle manuelle][]
-   [Définition de groupes d'échelle][]
-   [Utilisation de WASABiCmdlets pour manipuler le bloc via Windows PowerShell][]
-   [Guide du développeur Enterprise Library 5.0 Integration Pack for Azure][]
-   [Réduction des coûts d'hébergement Azure avec Sage via la mise à l'échelle automatique][]
-   [Réduction des coûts d'hébergement et de l'impact environnemental de TechNet et MSDN grâce à la mise à l'échelle automatique Azure][]

  [Microsoft Enterprise Library 5.0 Integration Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=235134
  [Étapes suivantes]: #NextSteps
  [Présentation du bloc applicatif de mise à l'échelle automatique]: #WhatIs
  [Concepts]: #Concepts
  [Collecte de données de compteurs de performances à partir de votre application Azure cible]: #PerfCounter
  [Configuration d'une application hôte pour le bloc applicatif de mise à l'échelle automatique]: #CreateHost
  [Procédure : instanciation et exécution de la classe Autoscaler]: #Instantiate
  [Procédure : Définition d'un modèle de service]: #DefineServiceModel
  [Procédure : Définition de règles de mise à l'échelle automatique]: #DefineAutoscalingRules
  [Procédure : Configuration du bloc applicatif de mise à l'échelle automatique]: #Configure
  [Utilisation de compteurs de performances dans Azure]: http://azure.microsoft.com/develop/net/common-tasks/performance-profiling/
  [NuGet]: http://nuget.org/
  [Portail de gestion Azure]: http://manage.windowsazure.com
  [Stockage de vos données d'informations de service]: http://msdn.microsoft.com/library/hh680878(PandP.50).aspx		
  [Hébergement du bloc applicatif de mise à l'échelle automatique dans un rôle de travail]: http://msdn.microsoft.com/library/hh680914(PandP.50).aspx
  [Implémentation d'un comportement de limitation]: http://msdn.microsoft.com/library/hh680896(PandP.50).aspx
  [Présentation des classements et de la conciliation des règles]: http://msdn.microsoft.com/library/hh680923(PandP.50).aspx
  [Développement et modification du bloc applicatif de mise à l'échelle automatique]: http://msdn.microsoft.com/library/hh680889(PandP.50).aspx
  [Utilisation du stabilisateur d'optimisation pour empêcher une oscillation des hautes fréquences et optimiser les coûts]: http://msdn.microsoft.com/library/hh680951(PandP.50).aspx
  [Utilisation des notifications et de la mise à l'échelle manuelle]: http://msdn.microsoft.com/library/hh680885(PandP.50).aspx
  [Définition de groupes d'échelle]: http://msdn.microsoft.com/library/hh680902(PandP.50).aspx
  [Utilisation de WASABiCmdlets pour manipuler le bloc via Windows PowerShell]: http://msdn.microsoft.com/library/hh680938(PandP.50).aspx
  [Guide du développeur Enterprise Library 5.0 Integration Pack for Azure]: http://msdn.microsoft.com/library/hh680949(PandP.50).aspx
  [Réduction des coûts d'hébergement Azure avec Sage via la mise à l'échelle automatique]: http://msdn.microsoft.com/library/jj838716(PandP.50).aspx
  [Réduction des coûts d'hébergement et de l'impact environnemental de TechNet et MSDN grâce à la mise à l'échelle automatique Azure]: http://msdn.microsoft.com/library/jj838718(PandP.50).aspx

<!--HONumber=45--> 
