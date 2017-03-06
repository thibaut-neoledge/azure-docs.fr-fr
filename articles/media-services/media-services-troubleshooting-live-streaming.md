---
title: "Guide de dépannage de la vidéo en flux continu | Microsoft Docs"
description: "Cette rubrique fournit des suggestions sur la façon de résoudre les problèmes de vidéo en flux continu."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 3a7f6c1d-ce57-4fa4-a7a6-edb526b3ffbf
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 0c77ee0f612c1cbef551a129a22cf3f125e6f29d
ms.openlocfilehash: 69fa0a7802ca39f86ada0af47b7a99e56436f973
ms.lasthandoff: 02/11/2017


---
# <a name="troubleshooting-guide-for-live-streaming"></a>Guide de dépannage de la vidéo en flux continu
Cette rubrique fournit des suggestions sur la façon de résoudre certains problèmes de vidéo en flux continu.

## <a name="issues-related-to-on-premises-encoders"></a>Problèmes liés aux encodeurs locaux
Cette section fournit des suggestions sur la façon de résoudre les problèmes liés aux encodeurs locaux qui sont configurés pour envoyer un flux à débit binaire unique à des canaux AMS activés pour l’encodage live.

### <a name="problem-would-like-to-see-logs"></a>Problème : vous aimeriez voir les journaux
* **Problème potentiel**: impossible de trouver des journaux de l’encodeur qui pourraient aider à déboguer des problèmes.
  
  * **Telestream Wirecast** : les journaux se trouvent en général sous C:\Users\{nom_utilisateur}\AppData\Roaming\Wirecast\ 
  * **Elemental Live** : vous pouvez trouver des liens vers les journaux sur le portail de gestion. Cliquez sur **Statistiques**, puis **Journaux**. Dans la page **Fichiers journaux**, vous trouvez une liste des journaux pour tous les éléments LiveEvent. Sélectionnez celui qui correspond à votre session active. 
  * **Flash Media Live Encoder** : vous pouvez trouver le **Répertoire des journaux...** en accédant à l’onglet **Journal d’encodage**.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problème : il n’existe aucune option pour générer un flux progressif
* **Problème potentiel**: l’encodeur utilisé n’effectue pas de désentrelacement automatique. 
  
    **Étapes de dépannage**: recherchez une option de désentrelacement dans l’interface de l’encodeur. Une fois le désentrelacement activé, revérifiez les paramètres de sortie progressive. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problème : vous avez essayé plusieurs paramètres de sortie d’encodeur et la connexion échoue encore.
* **Problème potentiel**: le canal d’encodage Azure n’a pas été réinitialisé correctement. 
  
    **Étapes de dépannage** : vérifiez que l’encodeur ne transmet plus les données à AMS, arrêtez puis réinitialisez le canal. Une fois le canal redémarré, essayez de connecter votre encodeur avec les nouveaux paramètres. Si cela ne résout toujours pas le problème, essayez de créer un canal. Parfois, les canaux peuvent être endommagés après plusieurs tentatives infructueuses.  
* **Problème potentiel**: la taille de GOP ou les paramètres d’image clé ne sont pas optimaux. 
  
    **Étapes de dépannage**: la taille de GOP ou l’intervalle d’image clé recommandé(e) est de deux secondes. Certains encodeurs calculent ce paramètre en nombre d’images, tandis que d’autres utilisent des secondes. Par exemple : lors de la sortie de 30 i/s, la taille de GOP serait de 60 images, ce qui équivaut à deux secondes.  
* **Problème potentiel**: des ports fermés bloquent le flux de données. 
  
    **Étapes de dépannage**: lors de la diffusion en flux continu via RTMP, vérifiez les paramètres de pare-feu et/ou de proxy pour confirmer que les ports sortants 1935 et 1936 sont ouverts. Lorsque vous utilisez la diffusion en flux continu RTP, vérifiez que le port sortant 2010 est ouvert. 

### <a name="problem-when-configuring-the-encoder-to-stream-with-the-rtp-protocol-there-is-no-place-to-enter-a-host-name"></a>Problème : lors de la configuration de l’encodeur pour la diffusion avec le protocole RTP, il n’y a aucun emplacement où entrer un nom d’hôte.
* **Problème potentiel**: de nombreux encodeurs RTP n’autorisent pas les noms d’hôtes et une adresse IP doit être acquise.  
  
    **Étapes de dépannage**: pour trouver l’adresse IP, ouvrez une invite de commandes sur n’importe quel ordinateur. Pour ce faire, dans Windows, ouvrez le lanceur Exécuter (touche Windows + R) et tapez « cmd ».  
  
    Une fois l’invite de commandes ouverte, tapez « Ping [nom_hôte_AMS] ». 
  
    Vous pouvez obtenir le nom d’hôte en omettant le numéro de port de l’URL de réception Azure, comme illustré dans l’exemple suivant : 
  
    rtp://test2-amstest009.rtp.channel.mediaservices.windows.net:2010/ 
  
    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

> [!NOTE]
> Si, après avoir suivi la procédure de dépannage, vous ne pouvez toujours pas diffuser en continu avec succès, envoyez un ticket de support en utilisant le portail Azure.
> 
> 

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


