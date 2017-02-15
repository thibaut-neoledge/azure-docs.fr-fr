---
title: "Configurer votre environnement de développement | Microsoft Docs"
description: "Installez le runtime, le kit de développement logiciel et créez un cluster de développement local. Une fois l’installation terminée, vous serez prêt à créer des applications."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/13/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 04092b735fa77c72ffe6c492a3fc975eac2e99fd
ms.openlocfilehash: a71b77a320e9321eaa857acfcfae8822de0ac9e5


---
# <a name="prepare-your-development-environment"></a>Préparer votre environnement de développement
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 Pour générer et exécuter des [applications Azure Service Fabric][1] sur votre ordinateur de développement, installez le runtime, le Kit de développement logiciel (SDK) et les outils. Vous devez également activer l’exécution des scripts Windows PowerShell inclus dans le Kit de développement logiciel (SDK).

## <a name="prerequisites"></a>Composants requis
### <a name="supported-operating-system-versions"></a>Versions du système d’exploitation prises en charge
Les versions de système d’exploitation prises en charge pour le développement sont les suivantes :

* Windows 7
* Windows 8 et Windows 8.1
* Windows Server 2012 R2
* Windows 10

> [!NOTE]
> Windows 7 inclut uniquement Windows PowerShell 2.0 par défaut. Les applets de commande PowerShell de Service Fabric nécessitent PowerShell 3.0 ou version ultérieure. Vous pouvez [télécharger Windows PowerShell 5.0][powershell5-download] à partir du Centre de téléchargement Microsoft.
> 
> 

## <a name="install-the-runtime-sdk-and-tools"></a>Installer le runtime, le SDK et les outils
Web Platform Installer offre deux configurations pour le développement de Service Fabric.

Visual Studio 2017 (Azure Development and Management Workload doit être installé) :

* [Installer le runtime et le Kit de développement logiciel (SDK) Service Fabric (aucun outil Visual Studio)][core-sdk]

Visual Studio 2015 (requiert Visual Studio 2015 Update 2 ou une version ultérieure) :

* [Installer le runtime et le Kit de développement logiciel (SDK) Service Fabric, ainsi que les outils][full-bundle-vs2015]
* [Installer uniquement le runtime et le Kit de développement logiciel (SDK) Service Fabric (aucun outil Visual Studio)][core-sdk]

> [!WARNING]
> Les clients ont signalé des erreurs lors de l’installation par le biais de ces liens de lancement ou lors de l’utilisation de ces liens dans le navigateur Chrome. Ces problèmes liés à Web Platform Installer sont connus et sont en cours de résolution.  Pour contourner ces problèmes, essayez les opérations suivantes :
>- Lancez les liens ci-dessus dans les navigateurs Internet Explorer ou Edge, ou
>- Lancez Web Platform Installer à partir du menu Démarrer, recherchez « Service Fabric », puis installez le Kit de développement logiciel (SDK)
> 
> Veuillez nous excuser pour ce désagrément. 

Les versions actuelles sont les suivantes :
* Kit de développement logiciel (SDK) Service Fabric 2.4.145
* Runtime Service Fabric 5.4.145
* Outils Visual Studio 2015 1.4.41209

Pour obtenir la liste des versions prises en charge, consultez l’article [Service Fabric support (Options de prise en charge de Service Fabric)](service-fabric-support.md).

## <a name="enable-powershell-script-execution"></a>Activer l'exécution du script PowerShell
Service Fabric utilise des scripts Windows PowerShell pour créer un cluster de développement local et déployer des applications à partir de Visual Studio. Par défaut, Windows bloque l’exécution de ces scripts. Pour les activer, vous devez modifier votre stratégie d'exécution PowerShell. Ouvrez PowerShell en tant qu'administrateur et entrez la commande suivante :

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez fini de configurer votre environnement de développement, commencez à créer et à exécuter des applications.

* [Créer votre première application Service Fabric dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
* [Découvrez comment déployer et gérer des applications sur votre cluster local](service-fabric-get-started-with-a-local-cluster.md)
* [En savoir plus sur les modèles de programmation : acteurs fiables et services fiables](service-fabric-choose-framework.md)
* [Consulter les exemples de code Service Fabric sur GitHub](https://aka.ms/servicefabricsamples)
* [Visualiser votre cluster à l’aide de l’outil Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Suivre le parcours d’apprentissage Service Fabric pour une introduction générale à la plate-forme](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* En savoir plus sur les [options de prise en charge de Service Fabric](service-fabric-support.md)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Page de campagne Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Lien WebPI VS 2015"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Lien WebPI Dev15"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Lien WebPI du Kit de développement logiciel principal"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395



<!--HONumber=Dec16_HO2-->


