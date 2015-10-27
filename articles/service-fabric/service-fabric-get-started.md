<properties
   pageTitle="Configurer votre environnement de développement | Microsoft Azure"
   description="Installez le runtime, le kit de développement logiciel et créez un cluster de développement local. Une fois l’installation terminée, vous serez prêt à créer des applications."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/15/2015"
   ms.author="seanmck"/>

# Préparer votre environnement de développement
 Pour générer et exécuter des [applications Service Fabric][1] sur votre ordinateur de développement, vous devez installer le runtime, le kit de développement logiciel et configurer un cluster local.

 >[AZURE.NOTE]Ces instructions sont prévues pour configurer de nouvelles machines. Si vous avez installé une version antérieure de Service Fabric, suivez les [instructions pour mettre à jour votre environnement de développement](service-fabric-update-your-development-environment.md).

## Configuration requise
### Versions de système d'exploitation prises en charge
Les versions de système d'exploitation prises en charge sont les suivantes :

- Windows 8/8.1
- Windows Server 2012 R2
- Windows 10

### Visual Studio 2015

Les outils pour Service Fabric dépendent de Visual Studio 2015, que vous pouvez trouver [ici][2].

> [AZURE.NOTE]Si vous n’exécutez pas l’une des versions de système d’exploitation prises en charge ou si vous préférez ne pas installer Visual Studio 2015 sur votre ordinateur, vous pouvez configurer une machine virtuelle Azure avec Windows Server 2012 R2 et Visual Studio 2015 préinstallés à l’aide d’une image de la galerie de machines virtuelles Azure.

## Installer le runtime, le SDK et les outils

L'installation des composants Service Fabric est effectuée par le programme d'installation de la plateforme web. Procédez comme suit pour l'installation :

1. [Téléchargez le SDK][3] à l'aide du programme d'installation de la plateforme web.

2. Cliquez sur **Installer** pour commencer le processus d'installation.

3. Lisez et acceptez le CLUF.

L'installation s'exécute automatiquement.

## Activer l'exécution du script PowerShell

Service Fabric utilise des scripts Windows PowerShell pour créer un cluster de développement local et déployer des applications à partir de Visual Studio. Par défaut, Windows bloque l'exécution de ces scripts. Pour les activer, vous devez modifier votre stratégie d'exécution PowerShell. Ouvrez PowerShell en tant qu'administrateur et entrez la commande suivante :

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Installer et démarrer un cluster local
Un cluster local est une topologie de plusieurs ordinateurs que vous utilisez par la suite en production sur un seul ordinateur de développement. Pour configurer le cluster local, procédez comme suit :


1. Fermez toutes les autres fenêtres PowerShell et démarrez-en une nouvelle en tant qu'administrateur.

2. Accédez au répertoire d’installation du cluster.

    ```powershell
    cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"
    ```

3. Exécuter

    ```powershell
    .\DevClusterSetup.ps1
    ```

Quelques instants après, une sortie s'affiche indiquant les informations de nœuds et confirmant que le cluster a été correctement créé. Dans certains cas, des alertes peuvent se produire pendant le démarrage du service hôte de Service Fabric et des services d'affectation de noms. Ce sont des alertes normales, suivies momentanément par certaines informations de base sur le cluster.

> [AZURE.NOTE]Votre cluster local utilise exactement le même runtime que s'il s'exécutait dans Azure. Il n'est ni simulé ni émulé. La seule différence est que tous vos nœuds s'exécutent sur un seul ordinateur au lieu d'être répartis sur plusieurs ordinateurs, comme c'est le cas dans Azure.

## Valider la configuration de votre cluster

Vous pouvez vérifier que votre cluster a été correctement créé à l'aide de l'outil Service Fabric Explorer fourni avec le SDK.

1. Démarrez Service Fabric Explorer en exécutant

    ```powershell
    . "$env:ProgramW6432\Microsoft SDKs\Service Fabric\Tools\ServiceFabricExplorer\ServiceFabricExplorer.exe"
    ```

2. Développez le nœud Onebox/Local Cluster en haut à gauche.

3. Assurez-vous que les vues d'application et de nœud s'affichent en vert.

Si aucun élément n'est vert ou qu'un message d'erreur s'affiche, attendez quelques instants et cliquez sur le bouton Actualiser. Si vous rencontrez encore des problèmes, consultez les [étapes de dépannage de configuration](service-fabric-troubleshoot-local-cluster-setup.md).

## Étapes suivantes
À présent que votre environnement de développement est configuré, vous pouvez commencer à créer et exécuter des applications.

- [En savoir plus sur les modèles de programmation : acteurs fiables et services fiables](service-fabric-choose-framework.md)
- [Prise en main de l'API des services fiables](service-fabric-reliable-services-quick-start.md)
- [Prise en main de l'API des acteurs fiables](service-fabric-reliable-actors-get-started.md)
- [Consulter les exemples de Service Fabric sur GitHub](https://github.com/azure/servicefabric-samples)
- [Visualiser votre cluster à l'aide de l'outil Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Page de campagne Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[3]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "Lien WebPI"

<!---HONumber=Oct15_HO4-->