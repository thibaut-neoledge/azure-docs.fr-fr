<properties
   pageTitle="Mettre à jour votre environnement de développement Service Fabric | Microsoft Azure"
   description="Mettez à jour votre environnement de développement Service Fabric pour utiliser le runtime, le SDK et les outils les plus récents."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/30/2015"
   ms.author="seanmck"/>

# Mise à jour de votre environnement de développement Service Fabric

 Service Fabric fournit régulièrement de nouvelles versions du runtime, du SDK et des outils à utiliser dans un développement local. En maintenant votre environnement de développement local à jour avec ces versions, vous êtes sûr de toujours avoir accès aux fonctionnalités, correctifs de bogues et améliorations des performances les plus récents lorsque vous créez et testez vos applications localement.

## Nettoyage de votre cluster local

 Pour le moment, Service Fabric ne prend pas en charge la mise à niveau du runtime Service Fabric alors qu'un cluster local est en cours d'exécution. Pour une mise à niveau sans souci, il est important de nettoyer tout d'abord votre cluster local.

 >[AZURE.NOTE]Le nettoyage de votre cluster local supprime toutes les applications déployées et leurs données.

 Vous pouvez nettoyer votre cluster local comme suit :


 1. Fermez toutes les autres fenêtres PowerShell et démarrez-en une nouvelle en tant qu'administrateur.

 2. Accédez au répertoire d'installation du cluster avec `cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"`.

 3. Exécutez `.\CleanCluster.ps1`.


## Installation du runtime, du SDK et des outils.

 Une fois que vous avez nettoyé avec succès votre cluster existant, vous pouvez poursuivre la mise à niveau comme suit :


 1. Démarrez Web Platform Installer pour la [mise à jour vers la nouvelle version][1].

 2. Une fois celle-ci terminée, démarrez une nouvelle fenêtre PowerShell en tant qu'administrateur et accédez au répertoire d'installation du cluster avec `cd "$ENV:ProgramFiles\Microsoft SDKs\ServiceFabric\ClusterSetup"`.

 3. Exécutez `.\DevClusterSetup.ps1` pour configurer votre cluster local.

Et voilà ! Vous pouvez maintenant démarrer Visual Studio et poursuivre la création d'applications Service Fabric.

>[AZURE.NOTE]La structure de projet par défaut a changé dans cette version. Vous devriez être capable d'ouvrir et d'exécuter des projets existants dans Visual Studio. Toutefois, si vous rencontrez des problèmes liés à la création, au déploiement ou au débogage de vos applications, vous devriez envisager de créer un nouveau projet et de migrer votre code.

 [1]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "Lien WebPI"

<!---HONumber=Nov15_HO2-->