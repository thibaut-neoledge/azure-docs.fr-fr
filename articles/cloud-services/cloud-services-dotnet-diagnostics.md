<properties 
	pageTitle="Utilisation des diagnostics (.NET) | Microsoft Azure" 
	description="Découvrez comment utiliser les données de diagnostic dans Azure pour le débogage, la mesure des performances, la surveillance, l'analyse du trafic, etc." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="rboucher" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="robb"/>



# Activation de Diagnostics dans les services cloud et les machines virtuelles Azure

Azure Diagnostics 1.2 et 1.3 vous permettent de collecter des données de diagnostic à partir d'un rôle de travail, d'un rôle Web ou d'une machine virtuelle exécuté dans Azure. Ce guide décrit comment utiliser Azure Diagnostics 1.2 et 1.3. Pour d'autres instructions détaillées sur la création d'une stratégie de journalisation et de suivi, et sur l'utilisation des diagnostics et des autres techniques pour résoudre les problèmes, consultez la page [Meilleures pratiques de dépannage pour développer des applications Azure][].

## Vue d'ensemble

Azure Diagnostics 1.2 et 1.3 sont des extensions vous permettant de collecter des données télémétriques de diagnostic à partir d'un rôle de travail, d'un rôle Web ou d'une machine virtuelle exécuté dans Azure. Les données télémétriques sont stockées dans un compte de stockage Azure et peuvent servir au débogage et à la résolution des problèmes, à mesurer les performances, à suivre l’utilisation des ressources, à analyser le trafic et à prévoir et analyser la capacité.

Azure Diagnostics 1.2 est utilisé avec les Kits de développement logiciel (SDK) Azure pour .NET 2.4 et versions antérieures. Azure Diagnostics 1.3 est utilisé avec les Kits de développement logiciel (SDK) Azure pour .NET 2.5 et versions postérieures.

Si vous avez utilisé Diagnostics version 1.0 par le passé, il existe trois différences notables avec Diagnostics 1.2 et 1.3 :

1.	Diagnostics 1.2 et 1.3 peuvent être déployés sur des machines virtuelles, ainsi que sur des services cloud.
2.	Diagnostics 1.0 fait partie du kit de développement logiciel Azure. Il est déployé où vous déployez votre service cloud. Diagnostics 1.2 et 1.3 sont des extensions et sont déployés séparément du déploiement de votre service cloud.
3.	Diagnostics 1.2 et 1.3 permettent de collecter des événements ETW et .NET EventSource.

Pour une comparaison plus détaillée, consultez [Comparaison des versions d’Azure Diagnostics][] à la fin de cet article.

Azure Diagnostics peut collecter les types de données télémétriques suivants :

Source de données|Description
---|---
Journaux IIS|Informations au sujet des sites Web IIS.
Journaux d'infrastructure Azure Diagnostic|Informations au sujet de Diagnostics lui-même.
Journaux d'échecs de requête IIS|Informations au sujet des échecs de requête à un site ou à une application IIS.
Journaux d'événements Windows|Informations envoyées au système de journalisation des événements Windows.
Compteurs de performances|Compteur du système d'exploitation et compteurs de performances personnalisés.
Vidages sur incident|Informations au sujet de l'état du processus en cas d'incident d'application.
Journaux d'erreurs personnalisés|Journaux créés par votre application ou votre service.
NET EventSource |Événements générés par votre code à l'aide de la <a href="http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">classe EventSource</a> .NET
ETW basé sur les manifestes|Événements ETW générés par n'importe quel processus.

## Activation de Diagnostics dans un rôle de travail

Cette procédure pas à pas décrit comment mettre en oeuvre un rôle de travail Azure qui émet des données télémétriques à l'aide de la classe EventSource .NET. Azure Diagnostics est utilisé pour collecter des données télémétriques et les stocker dans un compte de stockage Azure. Lors de la création d'un rôle de travail, Visual Studio active automatiquement Diagnostics 1.0 dans le cadre de la solution dans les Kits de développement logiciel (SDK) pour .NET 2.4 et versions antérieures. Les instructions suivantes décrivent le processus de création d'un rôle de travail, de désactivation de Diagnostics 1.0 de la solution, et de déploiement de Diagnostics 1.2 ou 1.3 sur votre rôle de travail.

### Conditions préalables
Cet article part du principe que vous disposez d'un abonnement Azure et que vous utilisez Visual Studio 2013 avec le kit de développement logiciel (SDK) Azure. Si vous n'avez pas d'abonnement Azure, vous pouvez vous inscrire pour bénéficier d'une [évaluation gratuite][]. Assurez-vous d'avoir [installé et configuré Azure PowerShell version 0.8.7 ou ultérieure][].

### Étape 1 : création d’un rôle de travail
1.	Lancez **Visual Studio 2013**.
2.	Créez un projet **Service cloud Azure** à partir du modèle **Cloud** qui cible .NET Framework 4.5. Nommez le projet « WadExample » et cliquez sur OK.
3.	Sélectionnez **Rôle de travail** et cliquez sur Ok. Le projet est créé. 
4.	Dans **Explorateur de solutions**, double-cliquez sur le fichier de propriétés **WorkerRole1**.
5.	Dans l’onglet **Configuration**, décochez **Activer Diagnostics** pour désactiver Diagnostics 1.0 (Kit de développement Azure 2.4 et versions antérieures).
6.	Générez votre solution pour vérifier que vous n'avez pas d'erreurs.

### Étape 2 : instrumentalisation de votre code
Remplacez le contenu de WorkerRole.cs par le code suivant : La classe SampleEventSourceWriter, héritée de la [classe EventSource][], implémente quatre méthodes de journalisation : **SendEnums**, **MessageMethod**, **SetOther** et **HighFreq**. Le premier paramètre de la méthode **WriteEvent** définit l'ID de l'événement respectif. La méthode Run implémente une boucle infinie qui appelle chacune des méthodes de journalisation implémentées dans la classe **SampleEventSourceWriter** toutes les 10 secondes.

	using Microsoft.WindowsAzure.ServiceRuntime;
	using System;
	using System.Diagnostics;
	using System.Diagnostics.Tracing;
	using System.Net;
	using System.Threading;

	namespace WorkerRole1
	{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections 
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
	}


### Étape 3 : déploiement de votre rôle de travail
1.	Déployez votre rôle de travail vers Azure à partir de Visual Studio en sélectionnant le projet **WadExample** dans l’Explorateur de solutions, puis **Publier** à partir du menu **Build**.
2.	Choisissez votre abonnement.
3.	Dans la boîte de dialogue **Paramètres de publication Microsoft Azure**, sélectionnez **Créer**.
4.	Dans la boîte de dialogue **Créer un service de cloud computing et Compte de stockage**, saisissez un **Nom** (par exemple « WadExample »), puis sélectionnez une région ou un groupe d’affinités.
5.	Définissez l'**Environnement** sur **Intermédiaire**.
6.	Modifiez d'autres **Paramètres** le cas échéant, puis cliquez sur **Publier**.
7.	Une fois que le déploiement a été réalisé, vérifiez dans le portail Azure que votre service cloud est en cours d'**Exécution**.

### Étape 4 : création de votre fichier de configuration Diagnostics et installation de l’extension
1.	Téléchargez la définition de schéma de fichier de configuration publique en exécutant la commande PowerShell suivante :
2.	
		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	Ajoutez un fichier XML à votre projet **WorkerRole1** en cliquant avec le bouton droit sur le projet **WorkerRole1**, puis sélectionnez **Ajouter** -> **Nouvel élément…** -> **Visual C# items** -> **Données** -> **Fichier XML**. Nommez le fichier « WadExample.xml ».

	![CloudServices\_diag\_add\_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)

3.	Associez le fichier WadConfig.xsd avec le fichier de configuration. Assurez-vous que la fenêtre de l'éditeur WadExample.xml est la fenêtre active. Appuyez sur **F4** pour ouvrir la fenêtre **Propriétés**. Cliquez sur la propriété **Schémas** dans la fenêtre **Propriétés**. Cliquez sur **…** dans la propriété **Schémas**. Cliquez sur le bouton **Ajouter…** et naviguez jusqu’à l’emplacement où vous avez enregistré le fichier XSD, puis sélectionnez le fichier WadConfig.xsd. Cliquez sur **OK**.
4.	Remplacez le contenu du fichier de configuration WadExample.xml par le XML suivant, puis enregistrez le fichier. Ce fichier de configuration définit deux compteurs de performances à collecter : un pour l'utilisation du processeur et l'autre pour l'utilisation de la mémoire. Ensuite, la configuration définit les quatre événements correspondant aux méthodes de la classe SampleEventSourceWriter.

```
		<?xml version="1.0" encoding="utf-8"?>
		<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  			<WadCfg>
    			<DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      			<PerformanceCounters scheduledTransferPeriod="PT1M">
        			<PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        			<PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      				</PerformanceCounters>
      				<EtwProviders>
        				<EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          					<Event id="1" eventDestination="EnumsTable"/>
          					<Event id="2" eventDestination="MessageTable"/>
          					<Event id="3" eventDestination="SetOtherTable"/>
          					<Event id="4" eventDestination="HighFreqTable"/>
          					<DefaultEvents eventDestination="DefaultTable" />
        				</EtwEventSourceProviderConfiguration>
      				</EtwProviders>
    			</DiagnosticMonitorConfiguration>
  			</WadCfg>
		</PublicConfig>
```

### Étape 5 : installation de Diagnostics sur votre rôle de travail
Les applets de commande PowerShell pour la gestion de Diagnostics sur un rôle Web ou de travail sont : Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension et Remove-AzureServiceDiagnosticsExtension.

1.	Ouvrez Azure PowerShell.
2.	Exécutez le script pour installer Diagnostics sur votre rôle de travail (remplacez *StorageAccountKey* par la clé du compte de stockage de votre compte de stockage wadexample) :

		$storage_name = "wadexample"
		$key = "<StorageAccountKey>"
		$config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
		$service_name="wadexample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
		Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1


### Étape 6 : examen de vos données télémétriques
Dans l'**Explorateur de serveurs** de Visual Studio, naviguez jusqu'au compte de stockage wadexample. Une fois que le service cloud a été exécuté pendant environ 5 minutes, vous devriez voir les tables **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** et **WADSetOtherTable**. Double-cliquez sur l’une des tables pour afficher les données télémétriques qui ont été collectées. ![CloudServices\_diag\_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## Activation de Diagnostics dans une machine virtuelle

Cette procédure pas à pas décrit comment installer Diagnostics à distance dans une machine virtuelle Azure à partir d'un ordinateur de développement. Vous apprendrez également à implémenter une application qui s'exécute sur cette machine virtuelle Azure et qui émet des données télémétriques à l'aide de la classe [EventSource Class][] .NET. Azure Diagnostics est utilisé pour collecter des données télémétriques et les stocker dans un compte de stockage Azure.

### Conditions préalables
Cette procédure pas à pas part du principe que vous disposez d'un abonnement Azure et que vous utilisez Visual Studio 2013 avec le kit de développement logiciel (SDK) Azure. Si vous n'avez pas d'abonnement Azure, vous pouvez vous inscrire pour bénéficier d'une [évaluation gratuite][]. Assurez-vous d'avoir [installé et configuré Azure PowerShell version 0.8.7 ou ultérieure][].

### Étape 1 : création d’une machine virtuelle
1.	Sur votre ordinateur de développement, lancez Visual Studio 2013.
2.	Dans l’**Explorateur de serveurs** de Visual Studio, développez **Azure**, cliquez avec le bouton droit sur **Machines virtuelles**, puis sélectionnez **Créer une machine virtuelle**.
3.	Sélectionnez votre abonnement Azure dans la boîte de dialogue **Choisissez un abonnement**, puis cliquez sur **Suivant**.
4.	Sélectionnez **Windows Server 2012 R2 Datacenter, novembre 2014** dans la boîte de dialogue **Sélectionner une image de machine virtuelle**, puis cliquez sur **Suivant**.
5.	Dans **Paramètres de base de la machine virtuelle**, définissez le nom de la machine virtuelle sur « wadexample ». Définissez votre nom d'utilisateur et votre mot de passe administrateur, puis cliquez sur **Suivant**.
6.	Dans la boîte de dialogue **Paramètres de service cloud**, créez un service cloud appelé « wadexampleVM ». Créez un compte de stockage appelé « wadexample », puis cliquez sur **Suivant**.
7.	Cliquez sur **Create**.

### Étape 2 : création d’une application
1.	Sur votre ordinateur de développement, lancez Visual Studio 2013.
2.	Créez une nouvelle application console Visual C# qui cible .NET Framework 4.5. Nommez le projet « WadExampleVM ». ![CloudServices\_diag\_new\_project](./media/cloud-services-dotnet-diagnostics/NewProject.png)
3.	Remplacez le contenu de Program.cs par le code suivant. La classe **SampleEventSourceWriter** implémente quatre méthodes de journalisation : **SendEnums**, **MessageMethod**, **SetOther** et **HighFreq**. Le premier paramètre de la méthode WriteEvent définit l'ID de l'événement respectif. La méthode Run implémente une boucle infinie qui appelle chacune des méthodes de journalisation implémentées dans la classe **SampleEventSourceWriter** toutes les 10 secondes.

		using System;
		using System.Diagnostics;
		using System.Diagnostics.Tracing;
		using System.Threading;

		namespace WadExampleVM
		{
    	sealed class SampleEventSourceWriter : EventSource
    	{
        	public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        	public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        	public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        	public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        	public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    	}

    	enum MyColor
    	{
        	Red,
        	Blue,
        	Green
    	}

    	[Flags]
    	enum MyFlags
    	{
        	Flag1 = 1,
        	Flag2 = 2,
        	Flag3 = 4
    	}

    	class Program
    	{
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");
            
            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }

        }
    	}
		}


4.	Enregistrez le fichier, puis sélectionnez **Générer la solution** à partir du menu **Générer** pour générer votre code.


### Étape 3 : déploiement de votre application
1.	Cliquez avec le bouton droit sur le projet **WadExampleVM** dans l’**Explorateur de solutions**, puis choisissez **Ouvrir un dossier dans l’Explorateur de fichiers**.
2.	Naviguez vers le dossier *bin\\Debug* et copiez tous les fichiers (WadExampleVM.\*)
3.	Dans l'**Explorateur de serveurs**, cliquez avec le bouton droit sur la machine virtuelle, puis sélectionnez **Se connecter à l'aide du Bureau à distance**.
4.	Une fois connecté à la machine virtuelle, créez un dossier nommé WadExampleVM, puis collez vos fichiers d'application dans le dossier.
5.	Lancez l'application WadExampleVM.exe. Une fenêtre de console vide doit apparaître.

### Étape 4 : création de votre configuration Diagnostics et installation de l’extension
1.	Téléchargez la définition de schéma de fichier de configuration publique sur votre ordinateur de développement en exécutant la commande PowerShell suivante :

		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	Ouvrez un nouveau fichier XML dans Visual Studio, soit dans un projet qui est déjà ouvert, soit dans une instance de Visual Studio ne comportant aucun projet ouvert. Dans Visual Studio, sélectionnez **Ajouter** -> **Nouvel élément…** -> **Éléments Visual C#** -> **Données** -> **Fichier XML**. Nommez le fichier « WadExample.xml ».
3.	Associez le fichier WadConfig.xsd avec le fichier de configuration. Assurez-vous que la fenêtre de l'éditeur WadExample.xml est la fenêtre active. Appuyez sur **F4** pour ouvrir la fenêtre **Propriétés**. Cliquez sur la propriété **Schémas** dans la fenêtre **Propriétés**. Cliquez sur **…** dans la propriété **Schémas**. Cliquez sur le bouton **Ajouter…** et naviguez jusqu’à l’emplacement où vous avez enregistré le fichier XSD, puis sélectionnez le fichier WadConfig.xsd. Cliquez sur **OK**.
4.	Remplacez le contenu du fichier de configuration WadExample.xml par le XML suivant, puis enregistrez le fichier. Ce fichier de configuration définit deux compteurs de performances à collecter : un pour l'utilisation du processeur et l'autre pour l'utilisation de la mémoire. Ensuite, la configuration définit les quatre événements correspondant aux méthodes de la classe SampleEventSourceWriter.

```
		<?xml version="1.0" encoding="utf-8"?>
		<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  			<WadCfg>
    			<DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      			<PerformanceCounters scheduledTransferPeriod="PT1M">
        			<PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        			<PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      				</PerformanceCounters>
      				<EtwProviders>
        				<EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          					<Event id="1" eventDestination="EnumsTable"/>
          					<Event id="2" eventDestination="MessageTable"/>
          					<Event id="3" eventDestination="SetOtherTable"/>
          					<Event id="4" eventDestination="HighFreqTable"/>
          					<DefaultEvents eventDestination="DefaultTable" />
        				</EtwEventSourceProviderConfiguration>
      				</EtwProviders>
    			</DiagnosticMonitorConfiguration>
  			</WadCfg>
		</PublicConfig>
```

### Étape 5 : installation à distance de Diagnostics sur votre machine virtuelle Azure
Les applets de commande PowerShell pour la gestion de Diagnostics sur une machine virtuelle sont : Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension et Remove-AzureVMDiagnosticsExtension.

1.	Sur votre ordinateur de développement, ouvrez Azure PowerShell.
2.	Exécutez le script pour installer Diagnostics à distance machine virtuelle (remplacez *StorageAccountKey* par la clé du compte de stockage de votre compte de stockage wadexamplevm) :

		$storage_name = "wadexamplevm"
		$key = "<StorageAccountKey>"
		$config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
		$service_name="wadexamplevm"
		$vm_name="WadExample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
		$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
		$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
		$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


### Étape 6 : examen de vos données télémétriques
Dans l'**Explorateur de serveurs** de Visual Studio, naviguez jusqu'au compte de stockage wadexample. Une fois que la machine virtuelle a été exécutée pendant environ 5 minutes, vous devriez voir les tables **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** et **WADSetOtherTable**. Double-cliquez sur l’une des tables pour afficher les données télémétriques qui ont été collectées. ![CloudServices\_diag\_wadexamplevm\_tables](./media/cloud-services-dotnet-diagnostics/WadExampleVMTables.png)

## Schéma du fichier de configuration

Le fichier de configuration Diagnostics définit les valeurs qui sont utilisées pour initialiser les paramètres de configuration de diagnostic lorsque le moniteur de diagnostics démarre. Consultez le [schéma de configuration d’Azure Diagnostics 1.2][] pour un exemple de fichier de configuration et une documentation détaillée sur son schéma.

## Résolution des problèmes

### Azure Diagnostics ne démarre pas
Diagnostics comprend deux composants : un plug-in d’agent invité et l’agent de surveillance. Les fichiers journaux du plug-in d'agent invité sont situés dans le fichier :

*%SystemDrive%\\ WindowsAzure\\Logs\\Plugins\\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>*\\CommandExecution.log

Les codes d'erreur suivants sont renvoyés par le plug-in :

Code de sortie|Description
---|---
0|Vous avez réussi !
\-1|Erreur générique.
\-2|Impossible de charger le fichier rcf.<p>Il s'agit d'une erreur interne qui ne peut se produire que si le lanceur du plug-in d'agent invité est appelé manuellement et de manière incorrecte sur la machine virtuelle.
\-3|Impossible de charger le fichier de configuration Diagnostics. <p><p>Solution : cette erreur se produit quand un fichier de configuration ne réussit pas la validation de schéma. La solution consiste à fournir un fichier de configuration qui est conforme au schéma.
\-4|Une autre instance de l'agent de surveillance Diagnostics utilise déjà le répertoire de ressources local. <p><p>Solution : indiquez une autre valeur pour **LocalResourceDirectory**.
\-6|Le lanceur du plug-in d'agent invité a tenté de démarrer Diagnostics avec une ligne de commande non valide.<p><p>Il s'agit d'une erreur interne qui ne peut se produire que si le lanceur du plug-in d'agent invité est appelé manuellement, de manière incorrecte, sur la machine virtuelle.
\-10|Le plug-in Diagnostics s'est terminé avec une exception non prise en charge.
\-11|L'agent invité n'a pas pu créer le processus responsable du lancement et de la surveillance de l'agent de surveillance.<p><p>Solution : vérifiez que les ressources système disponibles sont suffisantes pour lancer de nouveaux processus.<p>
\-101|Arguments non valides lors de l’appel du plug-in Diagnostics. <p><p>Il s'agit d'une erreur interne qui ne peut se produire que si le lanceur du plug-in d'agent invité est appelé manuellement et de manière incorrecte sur la machine virtuelle.
\-102|Le processus du plug-in ne peut pas démarrer tout seul.<p><p>Solution : vérifiez que les ressources système disponibles sont suffisantes pour lancer de nouveaux processus.
\-103|Le processus du plug-in n'arrive pas à s'initialiser. Plus précisément, il n’est pas en mesure de créer l’objet de journalisation. <p><p>Solution : vérifiez que les ressources système disponibles sont suffisantes pour lancer de nouveaux processus.
\-104|Impossible de charger le fichier rcf fourni par l’agent invité. <p><p>Il s'agit d'une erreur interne qui ne peut se produire que si le lanceur du plug-in d'agent invité est appelé manuellement et de manière incorrecte sur la machine virtuelle.
\-105|Le plug-in Diagnostics ne parvient pas à ouvrir le fichier de configuration Diagnostics. <p><p>Il s'agit d'une erreur interne qui ne peut se produire que si le plug-in Diagnostics est appelé manuellement et de manière incorrecte sur la machine virtuelle.
\-106|Impossible de lire le fichier de configuration Diagnostics. <p><p>Solution : cette erreur se produit quand un fichier de configuration ne réussit pas la validation de schéma. La solution consiste alors à fournir un fichier de configuration qui est conforme au schéma. Le XML qui est fourni à l'extension Diagnostics est présent dans le dossier *%SystemDrive%\\WindowsAzure\\Config* sur la machine virtuelle. Ouvrez le fichier XML approprié et recherchez **Microsoft.Azure.Diagnostics**, puis le champ **xmlCfg**. Les données sont encodées en base64. Vous devrez donc [les décoder](http://www.bing.com/search?q=base64+decoder) pour afficher le XML qui a été chargé par Diagnostics.<p>
\-107|La transmission du répertoire de ressources à l’agent de surveillance n’est pas valide. <p><p>Il s'agit d'une erreur interne qui ne peut se produire que si l'agent de surveillance est appelé manuellement et de manière incorrecte sur la machine virtuelle.</p>
\-108 |Impossible de convertir le fichier de configuration Diagnostics dans le fichier de configuration de l'agent de surveillance.<p><p>Il s'agit d'une erreur interne qui doit se produire uniquement si le plug-in Diagnostics est appelé manuellement avec un fichier de configuration non valide.
\-110|Erreur de configuration générale de Diagnostics.<p><p>Il s'agit d'une erreur interne qui ne peut se produire que si le plug-in Diagnostics est appelé manuellement avec un fichier de configuration non valide.
\-111|Impossible de démarrer l’agent de surveillance.<p><p>Solution : vérifiez que les ressources système disponibles sont suffisantes pour lancer de nouveaux processus.
\-112|Erreur générale


### Les données de Diagnostics ne sont pas journalisées dans le stockage
La principale cause de données d'événement manquantes est une mauvaise définition des informations du compte de stockage.

Solution : corrigez votre fichier de configuration Diagnostics et réinstallez Diagnostics. Avant que les données d'événement ne soient téléchargées sur votre compte de stockage, elles sont stockées dans le dossier. Voir ci-dessus pour plus informations sur **LocalResourceDirectory**.

Si ce dossier ne contient aucun fichier, l'agent de surveillance ne peut être lancé. Cette erreur est généralement causée par un fichier de configuration non valide et a dû être signalée dans CommandExecution.log. Si l'agent de surveillance collecte avec succès des données d'événement, vous verrez les fichiers .tsf pour chaque événement défini dans votre fichier de configuration.

L'agent de surveillance consigne toutes les erreurs qu'il rencontre dans le fichier MaEventTable.tsf. Pour inspecter le contenu de ce fichier, exécutez la commande suivante :

		%SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.[IaaS | PaaS]Diagnostics\1.3.0.0\Monitor\x64\table2csv maeventtable.tsf

L'outil génère un fichier appelé maeventtable.csv, qui présente des journaux que vous pouvez inspecter pour déterminer les défaillances.


## Forum Aux Questions (FAQ)
Vous trouverez ci-dessous des réponses à certaines questions fréquemment posées :

**Q.** Comment faire pour mettre à niveau ma solution Visual Studio d'Azure Diagnostics 1.0 à Azure Diagnostics 1.1 ?

**A.** La mise à niveau de votre solution Visual Studio de Diagnostics 1.0 vers Diagnostics 1.1 (ou ultérieure) est un processus manuel : - Désactivez Diagnostics dans votre solution Visual Studio pour empêcher Diagnostics 1.0 d’être déployé avec votre rôle. - Si votre code utilise l’écouteur de suivi, vous devez modifier votre code pour utiliser .NET EventSource. Diagnostics 1.1 et versions ultérieures ne prennent pas en charge l’écouteur de suivi. - Modifiez votre processus de déploiement pour installer l’extension Diagnostics 1.1.

**Q.** Si j’ai déjà installé l’extension Diagnostics 1.1 sur mon rôle ou ma machine virtuelle, comment faire pour mettre à niveau vers Diagnostics 1.2 ou 1.3 ?

**A.** Si vous avez spécifié « –Version “1.*” » lorsque vous avez installé Diagnostics 1.1, la prochaine fois que votre rôle ou votre machine virtuelle redémarrera, il sera automatiquement mis à jour vers la version la plus récente correspondant à l'expression régulière « 1.* ». Si vous avez spécifié « –Version “1.1” » lorsque vous avez installé Diagnostics 1.1, vous pouvez mettre à jour vers une nouvelle version en exécutant à nouveau la cmdlet Set- et en spécifiant la version que vous souhaitez installer.

**Q.** Comment les tables sont-elles nommées ?

**A.** Les tables sont nommées en fonction des éléments suivants :

		if (String.IsNullOrEmpty(eventDestination)) {
		    if (e == "DefaultEvents")
		        tableName = "WADDefault" + MD5(provider);
		    else
		        tableName = "WADEvent" + MD5(provider) + eventId;
		}
		else
		    tableName = "WAD" + eventDestination;

Voici un exemple :

		<EtwEventSourceProviderConfiguration provider=”prov1”>
		  <Event id=”1” />
		  <Event id=”2” eventDestination=”dest1” />
		  <DefaultEvents />
		</EtwEventSourceProviderConfiguration>
		<EtwEventSourceProviderConfiguration provider=”prov2”>
		  <DefaultEvents eventDestination=”dest2” />
		</EtwEventSourceProviderConfiguration>

Cela générera 4 tables :

Événement|Nom de la table
---|---
provider=”prov1” &lt;Event id=”1” /&gt;|WADEvent+MD5(“prov1”)+”1”
provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt;|WADdest1
provider=”prov1” &lt;DefaultEvents /&gt;|WADDefault+MD5(“prov1”)
provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt;|WADdest2

## Comparaison des versions d'Azure Diagnostics

Le tableau suivant compare les fonctionnalités prises en charge par Azure Diagnostics version 1.0 et versions 1.1/1.2/1.3 :

Types de rôle pris en charge|Diagnostics 1.0|Diagnostics 1.1/1.2/1.3
---|---
Rôle Web|Oui|Oui
Rôle de travail|Oui|Oui
IaaS|Non|Oui

Configuration et déploiement|Diagnostics 1.0|Diagnostics 1.1/1.2/1.3
---|---|---
Intégration avec Visual Studio - Intégré dans l'expérience de développement web/travail Azure.|Oui|Non
Scripts PowerShell - Scripts pour gérer l'installation et la configuration de Diagnostics sur le rôle.|Oui|Oui

Source de données|Collecte par défaut|Format|Description|Diagnostics 1.0|Diagnostics 1.1/1.2|Diagnostics 1.3
---|---|---|---|---|---|---
Journaux System.Diagnostics.Trace|Oui|Table|Journalise les messages de suivi envoyés par votre code à l'écouteur de suivi (un écouteur de suivi doit être ajouté dans le fichier web.config ou app.config). Les données du journal seront transférées à la table de stockage WADLogsTable en fonction de l'intervalle de transfert scheduledTransferPeriod.|Oui|Non (utilisent EventSource)|Oui
Journaux IIS|Oui|Blob|Journalise les informations au sujet des sites IIS. Les données du journal seront transférées au conteneur que vous spécifiez en fonction de l'intervalle de transfert scheduledTransferPeriod.|Oui|Oui|Oui
Journaux d'infrastructure Azure Diagnostic|Oui|Table|Journalise les informations au sujet de l'infrastructure de diagnostic, du module RemoteAccess et du module RemoteForwarder. Les données du journal seront transférées à la table de stockage ADDiagnosticInfrastructureLogsTable en fonction de l'intervalle de transfert scheduledTransferPeriodtransfer.|Oui|Oui|Oui
Journaux d'échecs de requête IIS|Non|Blob|Journalise les informations au sujet des échecs de requête à un site ou à une application IIS. Vous devez également l'activer en paramétrant les options de suivi de system.WebServer dans Web.config. Les données du journal seront transférées au conteneur que vous spécifiez en fonction de l'intervalle de transfert scheduledTransferPeriod.|Oui|Oui|Oui
Journaux d'événements Windows|Non|Table|Journalise les informations concernant les performances du système d'exploitation, de l'application ou du pilote. Les compteurs de performances doivent être spécifiés de manière explicite. Lorsque ceux-ci sont ajoutés, les données des compteurs de performances seront transférées à la table de stockage WADPerformanceCountersTable en fonction de l'intervalle de transfert scheduledTransferPeriod.|Oui|Oui|Oui
Compteurs de performances|Non|Table|Journalise les informations concernant les performances du système d'exploitation, de l'application ou du pilote. Les compteurs de performances doivent être spécifiés de manière explicite. Lorsque ceux-ci sont ajoutés, les données des compteurs de performances seront transférées à la table de stockage WADPerformanceCountersTable en fonction de l'intervalle de transfert scheduledTransferPeriod.|Oui|Oui|Oui
Vidages sur incident|Non|Blob|Journalise les informations au sujet de l'état du système d'exploitation en cas d'échec système. Les mini vidages sur incident sont collectés localement. Les vidages complets peuvent être activés. Les données du journal seront transférées au conteneur que vous spécifiez en fonction de l'intervalle de transfert scheduledTransferPeriod. Comme ASP.NET gère la plupart des exceptions, cette fonction est généralement utile uniquement pour un rôle de travail ou une machine virtuelle.|Oui|Oui|Oui
Journaux d'erreurs personnalisés|Non|Blob|En utilisant des ressources de stockage locales, les données personnalisées peuvent être journalisées et transférées immédiatement au conteneur que vous spécifiez.|Oui|Oui|Oui
EventSource|Non|Table|Journalise les événements générés par votre code à l'aide de la classe EventSource .NET.|Non|Oui|Oui
ETW basé sur les manifestes|Non|Table|Événements ETW générés par n'importe quel processus.|Non|Oui|Oui


## Ressources supplémentaires

- [Meilleures pratiques de dépannage pour développer des applications Azure][]
- [Collecte des données de journalisation avec les diagnostics Azure][]
- [Débogage d’une application Azure][]
- [Configuration de Diagnostics pour Azure Cloud Services et Azure Virtual Machines][]

  

[Overview]: #overview
[How to Enable Diagnostics in a Worker Role]: #worker-role
[How to Enable Diagnostics in a Virtual Machine]: #virtual-machine
[Sample Configuration File and Schema]: #configuration-file-schema
[Troubleshooting]: #troubleshooting
[Frequently Asked Questions]: #faq
[Comparaison des versions d’Azure Diagnostics]: #comparing
[Additional Resources]: #additional
[EventSource Class]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx
[classe EventSource]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx
  
[Configuration de Diagnostics pour Azure Cloud Services et Azure Virtual Machines]: http://msdn.microsoft.com/library/windowsazure/dn186185.aspx
[Débogage d’une application Azure]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx
[Collecte des données de journalisation avec les diagnostics Azure]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Meilleures pratiques de dépannage pour développer des applications Azure]: http://msdn.microsoft.com/library/windowsazure/hh771389.aspx
[évaluation gratuite]: http://azure.microsoft.com/pricing/free-trial/
[installé et configuré Azure PowerShell version 0.8.7 ou ultérieure]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
[schéma de configuration d’Azure Diagnostics 1.2]: http://msdn.microsoft.com/library/azure/dn782207.aspx
[Set-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/library/dn495270.aspx
[Get-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/library/dn495145.aspx
[Remove-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/library/dn495168.aspx
 

<!---HONumber=August15_HO7-->