<properties urlDisplayName="Visual Studio 14 CTP" pageTitle="Installation du Kit de développement logiciel (SDK) Azure 2.4 pour Visual Studio 14 CTP2" metaKeywords="Visual Studio, Azure SDK" description="Installation du Kit de développement logiciel (SDK) Azure 2.4 et de Visual Studio 14 CTP2" metaCanonical="" services="" documentationCenter="" title="Installing Azure SDK 2.4 for Visual Studio 14 CTP" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen" />

# Installation du Kit de développement logiciel (SDK) Azure 2.4 pour Visual Studio " 14 " CTP

Procédez comme suit pour installer le Kit de développement logiciel (SDK) Azure 2.4 pour .NET avec Visual Studio " 14 " CTP. Cette procédure permet d'installer les Kits de développement logiciel (SDK), les outils de base et les outils complexes pour le développement Azure avec Visual Studio " 14 " CTP. Cette version n'est pas conçue pour être utilisée avec d'autres versions de Visual Studio.

**Remarque** : le Kit de développement logiciel (SDK) Azure 2.4 n'est pas compatible avec Visual Studio " 14 " CTP1.

Pour installer le Kit de développement logiciel (SDK) Azure 2.4 pour .NET, procédez comme suit :

1. Installez la dernière version de [Visual Studio " 14 " CTP](http://go.microsoft.com/fwlink/p/?LinkId=400776).

2. Installez chaque composant du Kit de développement logiciel (SDK) Azure à l'aide des liens de la liste suivante, en procédant dans l'ordre. Choisissez la version x86 ou x64 pour chacun des composants suivants.

       <ul>
        <li>Outils de création Azure : <a href="http://go.microsoft.com/fwlink/p/?LinkId=400892">MicrosoftAzureAuthoringTools-x86.msi</a> ou <a href="http://go.microsoft.com/fwlink/p/?LinkId=400893">MicrosoftAzureAuthoringTools-x64.msi</a>.</li>
       <li>Émulateur de calcul Azure : <a href="http://go.microsoft.com/fwlink/p/?LinkId=400894">MicrosoftAzureEmulator-x86.exe</a> ou <a href="http://go.microsoft.com/fwlink/p/?LinkId=400895">MicrosoftAzureEmulator-x64.exe</a>.</li>
       <li>Bibliothèques clientes Azure : <a href="http://go.microsoft.com/fwlink/p/?LinkId=400896">MicrosoftAzureLibsForNet-x86.msi</a> ou <a href="http://go.microsoft.com/fwlink/p/?LinkId=400897">MicrosoftAzureLibsForNet-x64.msi</a>.</li>
       <li>Émulateur de stockage : <a href="http://go.microsoft.com/fwlink/p/?LinkId=400904">MicrosoftAzureStorageEmulator.msi</a>.                            Si vous recevez un avertissement concernant les bases de données SQL locales, installez SQL Server LocalDB 11.0 à partir <a href="http://go.microsoft.com/fwlink/p/?LinkId=400778">d'ici</a> pour x86 ou <a href="http://go.microsoft.com/fwlink/p/?LinkId=400779">d'ici</a> pour x64.</li><li> Outils Azure pour Visual Studio : <a href="http://go.microsoft.com/fwlink/p/?LinkId=400903">WindowsAzureTools.vs140.exe</a>.</li></ul>

## Problèmes connus

1. Si vous installez Visual Studio " 14 " CTP2 sur un ordinateur où Visual Studio 2013 est installé, vous ne pourrez pas démarrer de services mobiles dans Visual Studio " 14 " CTP2. Pour contourner ce problème, ajoutez une   référence aux assemblys suivants dans votre projet de services mobiles :

	* packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
	* packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2. Le débogage distant pour les sites web et les services mobiles Azure ne fonctionne pas dans Visual Studio " 14 " CTP2.

## Notes de publication

Lisez les [notes de publication](http://go.microsoft.com/fwlink/?LinkId=507517) pour le Kit de développement logiciel (SDK) Azure 2.4.
