
<properties 
    pageTitle="Estimation de l’utilisation de la bande passante réseau Azure RemoteApp | Microsoft Azure"
	description="Obtenir des informations sur les besoins en bande passante pour vos applications et collections Azure RemoteApp."
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

# Estimation de l’utilisation de la bande passante réseau Azure RemoteApp 

> [AZURE.IMPORTANT]
Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148).

Azure RemoteApp utilise le protocole RDP (Bureau à distance) pour communiquer entre les applications qui s’exécutent dans le cloud Azure et vos utilisateurs. Cet article fournit des instructions de base que vous pouvez utiliser pour estimer l’utilisation du réseau et évaluer potentiellement l’utilisation de la bande passante réseau pour chaque utilisateur d’Azure RemoteApp.

L’estimation de l’utilisation de la bande passante par utilisateur est très complexe et nécessite l’exécution simultanée de plusieurs applications dans des scénarios multitâche où chaque application peut avoir un impact sur les performances d’une autre, en fonction de leurs besoins en matière de bande passante réseau. Même le type de client Bureau à distance (par exemple, le client Mac et le client HTML5) peut entraîner des résultats différents sur la bande passante. Pour vous aider, nous allons répartir les scénarios d’utilisation entre plusieurs catégories courantes pour répliquer des scénarios réels. (Bien entendu, le scénario réel regroupe une combinaison de catégories et diffère par utilisateur.)

Avant d’aller plus loin, notez que nous partons du principe que le protocole RDP fournit une expérience bonne à excellente pour la plupart des scénarios d’utilisation sur des réseaux avec une latence inférieure à 120 ms et une bande passante supérieure à 5 Mo. Ceci est basé sur la capacité du protocole RDP de s’ajuster de manière dynamique en utilisant la bande passante réseau disponible et les besoins estimés en bande passante de l’application. Cet article va au-delà des « scénarios d’utilisation courants » pour s’intéresser aux cas où les scénarios commencent à faillir et l’expérience utilisateur commence à se dégrader.

À présent, consultez les articles suivants pour obtenir plus d’informations, y compris les facteurs à prendre en compte, les recommandations de base et ce que nous n’avons pas inclus dans nos estimations.

- [Quelle est la corrélation entre la bande passante réseau et la qualité de l’expérience d’utilisation ?](remoteapp-bandwidthexperience.md)
- [Test de votre bande passante réseau avec quelques scénarios courants](remoteapp-bandwidthtests.md)
- [Instructions rapides si vous n’avez pas le temps ou la possibilité d’exécuter le test](remoteapp-bandwidthguidelines.md)


## Quels sont les éléments que nous n’avons pas inclus ?

Lorsque vous examinez les tests proposés et nos recommandations globales (et il est vrai, génériques), n’oubliez pas qu’il existe plusieurs facteurs que nous n’avons pas pris en compte. Par exemple, les complications de l’expérience utilisateur dues à la nature asymétrique de la bande passante de chargement par rapport à la bande passante de téléchargement. La nature asymétrique de la plupart des réseaux Wi-Fi aura un impact supplémentaire sur les performances et la perception de l’expérience utilisateur. Pour les scénarios interactifs, le trafic en aval peut avoir une priorité inférieure au trafic en amont, ce qui peut augmenter le nombre de trames vidéos ou audio perdues, et par conséquent avoir un impact sur la perception de l’utilisateur de l’expérience de la diffusion en continu. Vous pouvez exécuter vos propres tests pour déterminer ce qui est adapté à votre cas d’utilisation spécifique et votre réseau.

Bien que nous abordions le thème de la redirection de périphériques, nous n’avons pas pris en compte l’impact de la bande passante du trafic réseau provoqué par les périphériques connectés, tels que les périphériques de stockage, les imprimantes, les scanneurs, les caméras web et d’autres périphériques USB. En général, l’effet de ces périphériques augmente de façon temporaire les besoins en bande passante et disparaît lorsque la tâche est terminée. Mais dans le cas d’une utilisation fréquente, la demande en bande passante pourrait être relativement importante.

Nous ne parlons pas non plus de la façon dont un utilisateur peut affecter d’autres utilisateurs au sein du même réseau. Par exemple, un utilisateur consommant une vidéo 4K sur un réseau 100 Mbits/s peut avoir un impact significatif sur les autres utilisateurs de ce même réseau essayant d’effectuer la même tâche. Malheureusement, il est de plus en plus difficile de déterminer l’impact de l’utilisation simultanée pour donner une recommandation commune ou complète sur les performances du système en cas d’agrégation. Tout ce que nous pouvons dire est que la technologie de protocole sous-jacente utilisera au mieux la bande passante réseau disponible, mais elle a ses limites.

<!---HONumber=AcomDC_0817_2016-->