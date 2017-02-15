---
title: Utilisation des diagnostics Azure dans les Machines Virtuelles | Microsoft Docs
description: "Utilisation des diagnostics Azure pour rassembler des données à partir d’Azure Virtual Machines pour le débogage, la mesure des performances, la surveillance, l’analyse du trafic, etc."
services: virtual-machines
documentationcenter: .net
author: rboucher
manager: jwhit
editor: 
ms.assetid: dfaabc7a-23e7-4af0-8369-f504d2915b3d
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 4480366f620ccd8397aa4264ab7f0cc1c5b37c7d


---
# <a name="enabling-diagnostics-in-azure-virtual-machines"></a>Activation des diagnostics dans Azure Virtual Machines
Consultez la page [Présentation des diagnostics Azure](azure-diagnostics.md) pour obtenir des informations sur les diagnostics Azure.

## <a name="how-to-enable-diagnostics-in-a-virtual-machine"></a>Activation de Diagnostics dans une machine virtuelle
Cette procédure pas à pas décrit comment installer Diagnostics à distance dans une machine virtuelle Azure à partir d'un ordinateur de développement. Vous apprendrez également à implémenter une application qui s’exécute sur cette machine virtuelle Azure et qui émet des données télémétriques à l’aide de la classe [EventSource Class][EventSource Class].NET. Azure Diagnostics est utilisé pour collecter des données télémétriques et les stocker dans un compte de stockage Azure.

### <a name="pre-requisites"></a>Conditions préalables
Cette procédure pas à pas part du principe que vous disposez d'un abonnement Azure et que vous utilisez Visual Studio 2013 avec le kit de développement logiciel (SDK) Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez vous inscrire pour bénéficier d’une [version d’évaluation gratuite][Free Trial]. Assurez-vous d’avoir [installé et configuré Azure PowerShell version 0.8.7 ou ultérieure][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-virtual-machine"></a>Étape 1 : création d’une machine virtuelle
1. Sur votre ordinateur de développement, lancez Visual Studio 2013.
2. Dans **l’Explorateur de serveurs** de Visual Studio, développez **Azure**, cliquez avec le bouton droit sur **Machines virtuelles**, puis sélectionnez **Créer une machine virtuelle**.
3. Sélectionnez votre abonnement Azure dans la boîte de dialogue **Choisir un abonnement** et cliquez sur **Suivant**.
4. Sélectionnez **Windows Server 2012 R2 Datacenter, novembre 2014** dans la boîte de dialogue **Sélectionner une image de machine virtuelle**, puis cliquez sur **Suivant**.
5. Dans **Paramètres de base de la machine virtuelle**, définissez le nom de la machine virtuelle sur « wadexample ». Définissez votre nom d'utilisateur et votre mot de passe administrateur, puis cliquez sur **Suivant**.
6. Dans la boîte de dialogue **Paramètres de service cloud** , créez un service cloud appelé « wadexampleVM ». Créez un compte de stockage appelé « wadexample », puis cliquez sur **Suivant**.
7. Cliquez sur **Create**.

### <a name="step-2-create-your-application"></a>Étape 2 : création d’une application
1. Sur votre ordinateur de développement, lancez Visual Studio 2013.
2. Créez une nouvelle application console Visual C# qui cible .NET Framework 4.5. Nommez le projet « WadExampleVM ».
   ![CloudServices_diag_new_project](./media/virtual-machines-dotnet-diagnostics/NewProject.png)
3. Remplacez le contenu de Program.cs par le code suivant. La classe **SampleEventSourceWriter** implémente quatre méthodes de journalisation : **SendEnums**, **MessageMethod**, **SetOther** et **HighFreq**. Le premier paramètre de la méthode WriteEvent définit l'ID de l'événement respectif. La méthode Run implémente une boucle infinie qui appelle chacune des méthodes de journalisation implémentées dans la classe **SampleEventSourceWriter** toutes les 10 secondes.
   
     using System;   using System.Diagnostics;   using System.Diagnostics.Tracing;   using System.Threading;
   
     namespace WadExampleVM   {   sealed class SampleEventSourceWriter : EventSource   {
   
         public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
         public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
         public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
         public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
         public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }
   
     }
   
     enum MyColor   {
   
         Red,
         Blue,
         Green
     }
   
     [Flags]   enum MyFlags   {
   
         Flag1 = 1,
         Flag2 = 2,
         Flag3 = 4
     }
   
     class Program   {   static void Main(string[] args)   {
   
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
   
     }   }   }
4. Enregistrez le fichier, puis sélectionnez **Générer la solution** à partir du menu **Générer** pour générer votre code.

### <a name="step-3-deploy-your-application"></a>Étape 3 : déploiement de votre application
1. Cliquez avec le bouton droit sur le projet **WadExampleVM** dans **l’Explorateur de solutions**, puis choisissez **Ouvrir un dossier dans l’Explorateur de fichiers**.
2. Naviguez vers le dossier *bin\Debug* et copiez tous les fichiers (WadExampleVM.*)
3. Dans **l’Explorateur de serveurs**, cliquez avec le bouton droit sur la machine virtuelle, puis sélectionnez **Se connecter à l’aide du Bureau à distance**.
4. Une fois connecté à la machine virtuelle, créez un dossier nommé WadExampleVM, puis collez vos fichiers d'application dans le dossier.
5. Lancez l'application WadExampleVM.exe. Une fenêtre de console vide doit apparaître.

### <a name="step-4-create-your-diagnostics-configuration-and-install-the-extension"></a>Étape 4 : création de votre configuration Diagnostics et installation de l’extension
1. Téléchargez la définition de schéma de fichier de configuration publique sur votre ordinateur de développement en exécutant la commande PowerShell suivante :
   
     (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
2. Ouvrez un nouveau fichier XML dans Visual Studio, soit dans un projet qui est déjà ouvert, soit dans une instance de Visual Studio ne comportant aucun projet ouvert. Dans Visual Studio, sélectionnez **Ajouter** -> **Nouvel élément…** -> **Éléments Visual C#** -> **Données** -> **Fichier XML**. Nommez le fichier « WadExample.xml ».
3. Associez le fichier WadConfig.xsd avec le fichier de configuration. Assurez-vous que la fenêtre de l'éditeur WadExample.xml est la fenêtre active. Appuyez sur **F4** pour ouvrir la fenêtre **Propriétés**. Cliquez sur la propriété **Schémas** dans la fenêtre **Propriétés**. Cliquez sur **…** in the **Schémas** . Cliquez sur **Ajouter…** et naviguez jusqu’à l’emplacement où vous avez enregistré le fichier XSD, puis sélectionnez le fichier WadConfig.xsd. Cliquez sur **OK**.
4. Remplacez le contenu du fichier de configuration WadExample.xml par le XML suivant, puis enregistrez le fichier. Ce fichier de configuration définit deux compteurs de performances à collecter : un pour l'utilisation du processeur et l'autre pour l'utilisation de la mémoire. Ensuite, la configuration définit les quatre événements correspondant aux méthodes de la classe SampleEventSourceWriter.

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

### <a name="step-5-remotely-install-diagnostics-on-your-azure-virtual-machine"></a>Étape 5 : installation à distance de Diagnostics sur votre machine virtuelle Azure
Les applets de commande PowerShell pour la gestion de Diagnostics sur une machine virtuelle sont : Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension et Remove-AzureVMDiagnosticsExtension.

1. Sur votre ordinateur de développement, ouvrez Azure PowerShell.
2. Exécutez le script pour installer Diagnostics à distance machine virtuelle (remplacez *StorageAccountKey* par la clé du compte de stockage de votre compte de stockage wadexamplevm) :
   
     $storage_name = "wadexamplevm"   $key = "<StorageAccountKey>"   $config_path="C:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"   $service_name="wadexamplevm"   $vm_name="WadExample"   $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key   $VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name   $VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext   $VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM

### <a name="step-6-look-at-your-telemetry-data"></a>Étape 6 : examen de vos données télémétriques
Dans l' **Explorateur de serveurs** de Visual Studio, naviguez jusqu'au compte de stockage wadexample. Après environ cinq minutes d’exécution de la machine virtuelle, vous devriez voir les tables **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** et **WADSetOtherTable**. Double-cliquez sur l'une des tables pour afficher les données télémétriques qui ont été collectées.

![CloudServices_diag_wadexamplevm_tables](./media/virtual-machines-dotnet-diagnostics/WadExampleVMTables.png)

## <a name="configuration-file-schema"></a>Schéma du fichier de configuration
Le fichier de configuration des diagnostics définit les valeurs qui sont utilisées pour initialiser les paramètres de configuration de diagnostic lorsque l’agent de diagnostics démarre. Consultez la [dernière référence de schéma](https://msdn.microsoft.com/library/azure/mt634524.aspx) pour obtenir les valeurs valides et des exemples.

## <a name="troubleshooting"></a>Résolution de problèmes
Pour plus d'informations, voir [Dépannage des diagnostics Azure](azure-diagnostics-troubleshooting.md) .

## <a name="next-steps"></a>Étapes suivantes
[Consultez la liste des articles sur les diagnostics Azure relatifs aux machines virtuelles](azure-diagnostics.md#virtual-machines-using-azure-diagnostics) pour modifier les données que vous collectez, résoudre des problèmes ou pour en savoir plus sur les diagnostics en général.

[EventSource Class]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: http://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/



<!--HONumber=Feb17_HO2-->


