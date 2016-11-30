---
title: "Passerelle des services Bureau à distance et serveur Multi-Factor Authentication avec RADIUS"
description: "Il s&quot;agit de la page d&quot;authentification multifacteur Azure qui facilite le déploiement de la passerelle Bureau à distance (RD) et le serveur Azure Multi-Factor Authentication à l’aide de RADIUS."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: f2354ac4-a3a7-48e5-a86d-84a9e5682b42
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ee868c5ba1a8429a733633edbc7efaa74e512135


---
# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Passerelle des services Bureau à distance et serveur Multi-Factor Authentication avec RADIUS
Dans de nombreux cas, la passerelle des services Bureau à distance utilise le serveur NPS local pour authentifier les utilisateurs. Ce document explique comment acheminer la demande RADIUS hors de la passerelle Bureau à distance (via le serveur NPS local) vers le serveur Multi-Factor Authentication.

Le serveur Multi-Factor Authentication doit être installé sur un serveur distinct, qui enverra ensuite par proxy la demande RADIUS au serveur NPS sur le serveur de la passerelle Bureau à distance. Après avoir validé le nom d'utilisateur et le mot de passe, NPS renvoie une réponse au serveur Multi-Factor Authentication qui effectue le second facteur d'authentification avant de retourner un résultat à la passerelle.

## <a name="configure-the-rd-gateway"></a>Configuration de la passerelle RD
La passerelle RD doit être configurée pour envoyer l'authentification RADIUS à un serveur Multi-Factor Authentication. Une fois la passerelle RD  installée, configurée et opérationnelle, accédez aux propriétés de passerelle Bureau à distance. Cliquez sur l'onglet Magasin des stratégies d’autorisation des connexions aux services Bureau à distance et modifiez-le pour utiliser un serveur central exécutant NPS au lieu du serveur local exécutant NPS. Ajoutez un ou plusieurs serveurs Azure Multi-Factor Authentication comme serveurs RADIUS et spécifiez un secret partagé pour chaque serveur.

## <a name="configure-nps"></a>Configuration NPS
La passerelle Bureau à distance utilise NPS pour envoyer la demande RADIUS à Azure Multi-Factor Authentication. Le délai d’expiration doit être modifié pour éviter que la passerelle Bureau à distance n'expire avant la fin de l'authentification multifacteur. Utilisez la procédure suivante pour configurer NPS.

1. Dans NPS, développez le menu Clients et serveurs RADIUS dans la colonne de gauche, puis cliquez sur Groupes de serveurs RADIUS distants. Accédez aux propriétés du GROUPE DE SERVEURS DE PASSERELLE TS. Modifiez les serveurs RADIUS affichés et accédez à l'onglet Équilibrage de la charge. Modifiez le « nombre de secondes sans réponse avant que la requête est considérée comme supprimée » et le « nombre de secondes entre les demandes lorsque le serveur est identifié comme non disponible » de 30 à 60 secondes. Cliquez sur l'onglet Authentification/Compte et assurez-vous que les ports RADIUS spécifiés correspondent aux ports sur lesquels le serveur Multi-Factor Authentication écoutera.
2. NPS doit également être configuré pour recevoir des authentifications RADIUS à partir du serveur Azure Multi-Factor Authentication. Dans le menu de gauche, cliquez sur Clients RADIUS. Ajoutez le serveur Azure Multi-Factor Authentication en tant que client RADIUS. Choisissez un nom convivial et spécifiez un secret partagé.
3. Développez la section Stratégies dans le volet de navigation gauche, puis cliquez sur Stratégies de demande de connexion. Elle doit contenir une stratégie de demande de connexion appelée STRATÉGIE D'AUTORISATION DE PASSERELLE TS créée lors de la configuration de la passerelle Bureau à distance. Cette stratégie transfère les demandes RADIUS au serveur Azure Multi-Factor Authentication.
4. Copiez cette stratégie pour en créer une. Dans la nouvelle stratégie, ajoutez une condition qui met en correspondance le nom convivial du client et le nom convivial défini à l'étape 2 ci-dessus pour le client RADIUS du serveur Azure Multi-Factor Authentication. Choisissez un ordinateur local comme fournisseur d'authentification. Cette stratégie garantit que lorsqu'une demande RADIUS est reçue du serveur Azure Multi-Factor Authentication, l'authentification se produit localement au lieu de renvoyer une demande RADIUS au serveur Azure Multi-Factor Authentication, ce qui entraînerait une condition de boucle. Pour éviter la condition de boucle, cette nouvelle stratégie doit être placée AU-DESSUS de la stratégie d'origine de transfert vers le serveur Azure Multi-Factor Authentication.

## <a name="configure-azure-multi-factor-authentication"></a>Configuration d’Azure Multi-Factor Authentication
- - -
Le serveur Azure Multi-Factor Authentication est configuré en tant que proxy RADIUS entre la passerelle Bureau à distance et le serveur NPS.  Il devrait être installé sur un serveur appartenant à un domaine, distinct du serveur de la passerelle Bureau à distance. Utilisez la procédure suivante pour configurer le serveur Azure Multi-Factor Authentication.

1. Ouvrez le serveur Azure Multi-Factor Authentication, puis cliquez sur l'icône de l'authentification RADIUS. Cochez la case Activer l'authentification RADIUS.
2. Sous l'onglet Clients, vérifiez que les ports correspondent à ce qui est configuré dans NPS, puis cliquez sur le bouton Ajouter... . Ajoutez l'adresse IP du serveur de la passerelle RD, le nom de l'application (facultatif) et un secret partagé. Le secret partagé doit être identique sur le serveur Azure Multi-Factor Authentication et sur la passerelle RD.
3. Cliquez sur l'onglet Cible puis sur le bouton radio des serveurs RADIUS.
4. Cliquez sur le bouton Ajouter… . Entrez l'adresse IP, le secret partagé et les ports du serveur NPS. À moins d'utiliser un NPS central, le client RADIUS et la cible RADIUS seront identiques. Le secret partagé doit correspondre à celui configuré dans la section du client RADIUS du serveur NPS.

![Authentification RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)




<!--HONumber=Nov16_HO2-->


