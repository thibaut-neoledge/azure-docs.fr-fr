<properties linkid="dotnet-visual-studio-2014" urlDisplayName="Visual Studio 14 CTP2" pageTitle="Installing the Azure SDK 2.4 for Visual Studio 14 CTP2" metaKeywords="Visual Studio, Azure SDK" description="Install Azure SDK 2.4 and Visual Studio 14 CTP2" metaCanonical="" services="" documentationCenter="" title="Installing Azure SDK 2.4 for Visual Studio 14 CTP2" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen"></tags>

## Installation du Kit de développement logiciel (SDK) Azure 2.4 pour Visual Studio « 14 » CTP

Procédez comme suit pour installer le Kit de développement logiciel (SDK) Azure 2.4 pour .NET avec Visual Studio « 14 » CTP. Cette procédure permet d'installer les Kits de développement logiciel (SDK), les outils de base et les outils complexes pour le développement Azure avec Visual Studio « 14 » CTP. Cette version n'est pas conçue pour être utilisée avec d'autres versions de Visual Studio.

**Remarque** : le Kit de développement logiciel (SDK) Azure 2.4 n'est pas compatible avec Visual Studio « 14 » CTP1.

Pour installer le Kit de développement logiciel (SDK) Azure 2.4 pour .NET, procédez comme suit :

1.  Installez la dernière version de [Visual Studio « 14 » CTP][Visual Studio « 14 » CTP].

2.  Installez chaque composant du Kit de développement logiciel (SDK) Azure à l'aide des liens de la liste suivante en procédant dans l'ordre. Choisissez la version x86 ou x64 pour chacun des composants suivants.

    -   Outils de création Azure : [WindowsAzureAuthoringTools-x86.msi][WindowsAzureAuthoringTools-x86.msi] ou [WindowsAzureAuthoringTools-x64.msi][WindowsAzureAuthoringTools-x64.msi].
    

    -   Émulateur de calcul Azure : [WindowsAzureEmulator-x86.exe][WindowsAzureEmulator-x86.exe] ou [WindowsAzureEmulator-x64.exe][WindowsAzureEmulator-x64.exe].
    

    -   Bibliothèques clientes Azure : [WindowsAzureLibsForNet-x86.msi][WindowsAzureLibsForNet-x86.msi] ou [WindowsAzureLibsForNet-x64.msi][WindowsAzureLibsForNet-x64.msi].


    -   Émulateur de stockage : [WindowsAzureStorageEmulator.msi][WindowsAzureStorageEmulator.msi]. Si vous recevez un avertissement concernant les bases de données SQL locales, installez SQL Server LocalDB 11.0 à partir [d'ici][d'ici] pour x86 ou [d'ici][1] pour x64.


    -   Outils Azure pour Visual Studio : [WindowsAzureTools.vs140.exe][WindowsAzureTools.vs140.exe].

    </p>

## Problèmes connus

1.  Si vous installez Visual Studio « 14 » CTP2 sur un ordinateur où Visual Studio 2013 est installé, vous ne pourrez pas démarrer de services mobiles dans Visual Studio « 14 » CTP2. Pour contourner ce problème, ajoutez une référence aux assemblys suivants dans votre projet de services mobiles :

    -   packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
    -   packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2.  Le débogage distant pour les sites web et les services mobiles Azure ne fonctionne pas dans Visual Studio « 14 » CTP2.

## Notes de publication

Lisez les [notes de publication][notes de publication] pour le Kit de développement logiciel (SDK) Azure 2.4.

  [Visual Studio « 14 » CTP]: http://go.microsoft.com/fwlink/p/?LinkId=400776
  [WindowsAzureAuthoringTools-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400892
  [WindowsAzureAuthoringTools-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400893
  [WindowsAzureEmulator-x86.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400894
  [WindowsAzureEmulator-x64.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400895
  [WindowsAzureLibsForNet-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400896
  [WindowsAzureLibsForNet-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400897
  [WindowsAzureStorageEmulator.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400904
  [d'ici]: http://go.microsoft.com/fwlink/p/?LinkId=400778
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=400779
  [WindowsAzureTools.vs140.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400903
  [notes de publication]: http://go.microsoft.com/fwlink/?LinkId=507517
