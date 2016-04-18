<properties 
    pageTitle="Azure RemoteApp : quelle est la corrélation entre la bande passante réseau et la qualité de l’expérience d’utilisation ? | Microsoft Azure"
	description="Découvrez l’impact de la bande passante réseau dans Azure RemoteApp sur la qualité de l’expérience utilisateur."
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
    ms.date="03/31/2016" 
    ms.author="elizapo" />

# Azure RemoteApp : quelle est la corrélation entre la bande passante réseau et la qualité de l’expérience d’utilisation ?

Lorsque vous examinez la [bande passante réseau globale](remoteapp-bandwidth.md) requise pour Azure RemoteApp, gardez à l’esprit les facteurs suivants ; ils font tous partie d’un système dynamique qui a un impact sur l’expérience utilisateur globale.

- **Bande passante réseau disponible et conditions actuelles du réseau** : un ensemble de paramètres (perte, latence, instabilité) sur le même réseau à un moment donné peut avoir un impact sur l’expérience de diffusion en continu de l’application, entraînant une expérience utilisateur globale de qualité inférieure. La bande passante disponible sur votre réseau est une fonction de congestion, de perte aléatoire, de latence, car tous ces paramètres affectent le mécanisme de contrôle de la congestion, qui à son tour contrôle la vitesse de transmission pour éviter les collisions. Par exemple, un réseau avec perte de données ou un réseau avec une latence élevée donnera une expérience utilisateur de mauvaise qualité, même sur un réseau avec une bande passante de 1 000 Mo. La perte et la latence varient en fonction du nombre d’utilisateurs qui se trouvent sur le même réseau et ce qu’ils font (par exemple, regarder des vidéos, télécharger ou transférer des fichiers volumineux, imprimer).
- **Scénario d’utilisation** : l’expérience dépend de ce que font les utilisateurs en tant qu’individus et en tant que groupe sur le même réseau. Par exemple, la lecture d’une diapositive nécessite la mise à jour d’une seule image. Si l’utilisateur consulte et fait défiler le contenu d’un document texte, le nombre d’images devant être mises à jour par seconde est plus élevé. La communication dans les deux sens avec le serveur dans ce scénario finira par consommer davantage de bande passante réseau. Envisagez également un exemple extrême : plusieurs utilisateurs visionnent des vidéos haute définition (résolution 4K, par exemple), organisent des téléconférences HD, jouent à des jeux vidéo 3D ou utilisent des systèmes de CAO. Tous ces éléments peuvent rendre même un réseau à bande passante réellement élevée pratiquement inutilisable.
- **Résolution d’écran et nombre d’écrans** : davantage de bande passante réseau est requise pour la mise à jour complète des écrans plus grands que pour les écrans plus petits. La technologie sous-jacente fait un assez bon travail concernant l’encodage et le transfert uniquement des parties des écrans qui ont été mises à jour, mais de temps en temps, l’écran en entier doit être mis à jour. Lorsque l’utilisateur a un écran de résolution plus élevée (par exemple, 4K), cette mise à jour nécessite plus de bande passante réseau qu’un écran avec une résolution inférieure (par exemple, 1 024 x 768 px). Cette même logique s’applique si vous utilisez plusieurs écrans pour la redirection. La bande passante doit augmenter parallèlement au nombre d’écrans.
- **Presse-papiers et redirection de périphériques** : il ne s’agit pas d’un problème très évident, mais dans de nombreux cas, si un utilisateur stocke une grande quantité de données dans le Presse-papiers, il faut un peu de temps pour que ces informations soient transférées à partir du client Bureau à distance vers le serveur. L’expérience en aval peut être affectée par l’expérience d’envoi du contenu du Presse-papiers en amont. Il en va de même pour la redirection de périphériques. Si un scanneur ou une webcam génère une grande quantité de données qui doivent être envoyées en amont vers le serveur, ou si une imprimante doit recevoir un document volumineux, ou si un périphérique de stockage local doit être disponible pour une application s’exécutant sur le cloud pour copier un fichier volumineux, les utilisateurs peuvent constater la perte d’images ou une vidéo temporairement « figée », car les données nécessaires à la redirection de périphériques augmentent les besoins en bande passante réseau. 

Lorsque vous évaluez vos besoins en bande passante réseau, veillez à prendre en compte tous ces facteurs dans le cadre d’un système.

Maintenant, revenez à l’[article principal sur la bande passante réseau](remoteapp-bandwidth.md) ou passez au test de votre [bande passante réseau](remoteapp-bandwidthtests.md).

## En savoir plus
- [Estimation de l’utilisation de la bande passante réseau Azure RemoteApp](remoteapp-bandwidth.md)

- [Azure RemoteApp : test de votre bande passante réseau avec quelques scénarios courants](remoteapp-bandwidthtests.md)

- [Bande passante réseau Azure RemoteApp : instructions générales (si vous ne pouvez pas tester votre propre bande passante)](remoteapp-bandwidthguidelines.md)

<!---HONumber=AcomDC_0406_2016-->