
<properties 
    pageTitle="Informations sur la taille des réseaux virtuels dans RemoteApp"
    description="Découvrez le nombre d’adresses IP requises pour l’exécution de RemoteApp avec un réseau virtuel" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/29/2015" 
    ms.author="elizapo" />



# Informations sur la taille des réseaux virtuels dans RemoteApp

Lorsque vous utilisez RemoteApp avec un réseau virtuel \(VNET\), RemoteApp utilise des adresses IP au sein du sous-réseau. En fonction de l’échelle de votre service RemoteApp, vous devez vous assurer que votre sous-réseau comprend suffisamment d’adresses IP pour les machines virtuelles RemoteApp. Bien que ces indications sur la taille ne soient pas parfaites, compte tenu de la manière dont RemoteApp monte ou descend dynamiquement les machines virtuelles au sein d’une collection, celles-ci vous aideront à évaluer votre plage de sous-réseau. Ceci est particulièrement important car, une fois qu’un service RemoteApp est placé dans un réseau virtuel, vous ne pouvez plus augmenter la taille du sous-réseau sans supprimer RemoteApp.

Pour chaque collection RemoteApp que vous souhaitez exécuter à capacité maximale, vous devez disposer de 100 adresses IP disponibles. Par exemple, si vous possédez une collection RemoteApp dans le plan Standard et que vous souhaitez atteindre le nombre maximal de 500 utilisateurs, vous devez disposer de 100 adresses IP pour cette collection. De même, vous devez disposer de 100 adresses IP pour une collection RemoteApp dans le plan de base, qui comprend 800 utilisateurs. Si vous pensez que le nombre d’utilisateurs sera inférieur au nombre maximal, vous pouvez réduire le nombre d’adresses IP requises par collection. La taille minimale requise du sous-réseau est de 30 adresses IP \(/ 27\).

<!--HONumber=52-->
