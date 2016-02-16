<properties
   pageTitle="Configurer votre environnement de développement | Microsoft Azure"
   description="Installez le runtime, le kit de développement logiciel et créez un cluster de développement local. Une fois l’installation terminée, vous serez prêt à créer des applications."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/09/2016"
   ms.author="seanmck"/>

# Préparer votre environnement de développement
 Pour générer et exécuter des [applications Azure Service Fabric][1] sur votre ordinateur de développement, vous devez installer le runtime, le kit de développement logiciel et les outils. Vous devez également activer l’exécution des scripts Windows PowerShell inclus dans le Kit de développement logiciel (SDK).

## Composants requis
### Versions du système d’exploitation prises en charge
Les versions de système d'exploitation prises en charge sont les suivantes :

- Windows 8 et Windows 8.1
- Windows Server 2012 R2
- Windows 10

### Visual Studio 2015

Les outils de Service Fabric dépendent de Visual Studio 2015. Vous pouvez le trouver sur le [site web Visual Studio][2].

> [AZURE.NOTE] Si vous n’exécutez pas l’une des versions de système d’exploitation prises en charge ou si vous préférez ne pas installer Visual Studio 2015 sur votre ordinateur, vous pouvez configurer une machine virtuelle Azure avec Windows Server 2012 R2 et Visual Studio 2015 préinstallés. Vous pouvez également procéder en utilisant une image à partir de la galerie de machine virtuelle Azure.

## Installer le runtime, le SDK et les outils

L’installation des composants Service Fabric est effectuée par le programme d’installation de la plateforme web. Procédez comme suit pour l'installation :

1. [Téléchargez le kit de développement logiciel][3] à l’aide du programme d’installation de la plateforme web.

2. Cliquez sur **Installer** pour lancer le processus d’installation.

3. Lisez et acceptez le CLUF.

L'installation s'exécute automatiquement.

## Activer l'exécution du script PowerShell

Service Fabric utilise des scripts Windows PowerShell pour créer un cluster de développement local et déployer des applications à partir de Visual Studio. Par défaut, Windows bloque l'exécution de ces scripts. Pour les activer, vous devez modifier votre stratégie d'exécution PowerShell. Ouvrez PowerShell en tant qu'administrateur et entrez la commande suivante :

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Étapes suivantes
À présent que votre environnement de développement est configuré, vous pouvez commencer à créer et exécuter des applications.

- [Créer votre première application Service Fabric dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Découvrez comment déployer et gérer des applications sur votre cluster local](service-fabric-get-started-with-a-local-cluster.md)
- [En savoir plus sur les modèles de programmation : acteurs fiables et services fiables](service-fabric-choose-framework.md)
- [Consulter les exemples de code Service Fabric sur GitHub](https://aka.ms/servicefabricsamples)
- [Visualiser votre cluster à l’aide de l’outil Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
- [Suivre le parcours d’apprentissage Service Fabric pour une introduction générale à la plate-forme](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Page de campagne Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[3]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "Lien WebPI"

<!---HONumber=AcomDC_0211_2016-->