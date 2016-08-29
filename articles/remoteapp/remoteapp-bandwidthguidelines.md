<properties 
    pageTitle="Bande passante réseau Azure RemoteApp : instructions générales | Microsoft Azure"
	description="Comprendre des instructions de base en matière de bande passante réseau pour vos applications et collections Azure RemoteApp."
	services="remoteapp"
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />
    
# Bande passante réseau Azure RemoteApp : instructions générales (si vous ne pouvez pas tester votre propre bande passante)

> [AZURE.IMPORTANT]
Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148).

Si vous n’avez pas le temps ou la possibilité d’exécuter les [tests de la bande passante réseau](remoteapp-bandwidthtests.md) pour Azure RemoteApp, voici quelques instructions relativement génériques qui peuvent vous aider à estimer la bande passante réseau par utilisateur.

Si vous avez une combinaison de ces scénarios, nous recommandons une bande passante réseau de 10 Mbits/s MINIMUM pour les applications modernes connectées à Internet dans un environnement à distance. (Bien que, comme indiqué, cela ne garantit pas une expérience utilisateur supérieure à la moyenne.)

## PowerPoint complexe, PowerPoint simple

Les performances d’Azure RemoteApp sont optimales sur un réseau local de 100 Mo. Avec un profil réseau de 10 Mbits/s, lorsque l’instabilité supérieure à 120 ms est de plus de 5 %, l’expérience utilisateur sera moyenne. À 1 Mbit/s, la différence est incontestable. Par exemple, dans un diaporama, l’utilisateur peut ne pas voir les transitions animées, car les images sont ignorées.

## Internet Explorer, PDF mixte, PDF, texte

Un profil réseau de 10 Mbits/s est similaire d’un réseau local sous bien des aspects. 1 Mbit/s offre une expérience acceptable, bien qu’une instabilité soit possible lorsqu’un utilisateur fait défiler du contenu alors que des images sont présentes à l’écran.

## Vidéo flash (YouTube)

Un réseau local de 100 Mbits/s offre la meilleure expérience, alors qu’un réseau de 10 Mbits/s est acceptable (c’est-à-dire que le réseau peut suivre la fréquence des images mais l’instabilité augmente). À 1 Mbit/s, l’instabilité est très élevée et se remarque.

## Frappe dans Word (saisie à distance dans Word)
Il s’agit d’un scénario d’utilisation à bande passante faible. À 256 Kbits/s, l’expérience est aussi bonne que sur un réseau local.

## En savoir plus
- [Estimation de l’utilisation de la bande passante réseau Azure RemoteApp](remoteapp-bandwidth.md)

- [Azure RemoteApp : quelle est la corrélation entre la bande passante réseau et la qualité de l’expérience d’utilisation ?](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp : test de votre bande passante réseau avec quelques scénarios courants](remoteapp-bandwidthtests.md)

<!---HONumber=AcomDC_0817_2016-->