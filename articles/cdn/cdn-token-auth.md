---
title: "Sécurisation des ressources CDN Azure avec l’authentification du jeton| Microsoft Docs"
description: "Utilisation de l’authentification du jeton pour sécuriser l’accès à vos ressources CDN Azure."
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/11/2016
ms.author: mezha
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 42b182c314795b1ebf69639ec7ac5583208dc7c1
ms.lasthandoff: 03/29/2017


---


# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Sécurisation des ressources CDN Azure avec l’authentification du jeton

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

##<a name="overview"></a>Vue d'ensemble

L’authentification du jeton est un mécanisme qui vous permet d’empêcher le CDN Azure de fournir des ressources à des clients non autorisés.  Il vise généralement à empêcher le « hotlinking » de contenu, c’est-à-dire l’utilisation de vos ressources sans permission par un autre site web (souvent un forum de discussion),  ce qui peut avoir un impact sur les coûts de distribution de votre contenu. Lorsque vous activez cette fonctionnalité sur le CDN, les demandes sont authentifiées par les points de présence de périphérie du CDN avant la distribution du contenu. 

## <a name="how-it-works"></a>Fonctionnement

L’authentification du jeton vérifie que les demandes sont générées par un site de confiance en vérifiant si les demandes comportent une valeur de jeton contenant des informations codées sur le demandeur. Le contenu sera fourni au demandeur uniquement lorsque les informations codées respecteront les exigences définies. Dans le cas contraire, les demandes seront refusées. Vous pouvez configurer une exigence à l’aide de l’un ou plusieurs des paramètres ci-dessous.

- Pays : pour autoriser ou refuser les demandes provenant des pays spécifiés.  [Liste des codes de pays valides.](https://msdn.microsoft.com/library/mt761717.aspx) 
- URL : pour autoriser les demandes portant sur la ressource spécifiée ou présentant le chemin d’accès spécifié.  
- Hôte : pour autoriser ou refuser des demandes utilisant les hôtes spécifiés dans l’en-tête de la demande.
- Référent : pour autoriser ou refuser une demande provenant du référent spécifié.
- Adresse IP : pour autoriser uniquement les demandes provenant d’une adresse ou d’un sous-réseau IP spécifique.
- Protocole : pour autoriser ou bloquer les demandes basées sur le protocole utilisé pour demander le contenu.
- Date/heure d’expiration : pour assigner une date et une période afin de s’assurer que la liaison sera valide uniquement pendant une période limitée.

Consultez l’exemple de configuration détaillée de chaque paramètre.

## <a name="reference-architecture"></a>Architecture de référence

L’architecture de référence ci-dessous décrit la configuration de l’authentification du jeton sur le CDN pour une utilisation avec votre application web.

![Bouton de gestion du panneau de profil CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Logique de validation de jeton sur le point de terminaison CDN
    
Ce graphique décrit comment le CDN Azure valide la demande du client lorsque l’authentification du jeton est configurée sur le point de terminaison CDN.

![Bouton de gestion du panneau de profil CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Configuration de l’authentification du jeton

1. À partir du [Portail Azure](https://portal.azure.com), accédez à votre profil CDN, puis cliquez sur le bouton **Gérer** pour ouvrir le portail supplémentaire.

    ![Bouton de gestion du panneau de profil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)

2. Placez le pointeur sur **HTTP Large**, puis cliquez sur **Token Auth** (Authentification du jeton) dans le menu volant. Vous allez configurer la clé de chiffrement et les paramètres de chiffrement dans cet onglet.

    1. Entrez une clé de chiffrement unique sous **Clé primaire**.  Entrez un autre clé sous **Backup Key** (Clé de sauvegarde)

        ![Clé de configuration de l’authentification du jeton CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. Définissez les paramètres de chiffrement à l’aide de l’outil de chiffrement (autorisez ou refusez les demandes en fonction de l’heure d’expiration, du pays, du référent, du protocole ou de l’IP du client. Vous pouvez combiner ces exigences comme vous le souhaitez.)

        ![Bouton de gestion du panneau de profil CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

        - ec-expire : attribue un délai d’expiration spécifique à un jeton. Les demandes soumises après le délai d’expiration seront refusées. Ce paramètre utilise l’horodatage Unix (basé sur le nombre de secondes à partir de l’époque standard 01/01/1970 00:00:00 GMT. Vous pouvez utiliser les outils en ligne pour obtenir une conversion entre l’heure standard et l’heure Unix.)  Par exemple, si vous souhaitez définir l’expiration du jeton au 31/12/2016 12:00:00 GMT, utilisez l’heure Unix : 1483185600 comme indiqué ci-dessous :
    
        ![Bouton de gestion du panneau de profil CDN](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
        - ec-url-allow : vous permet d’adapter les jetons à une ressource ou un chemin d’accès particulier. Ce paramètre restreint l’accès aux demandes dont l’URL commence par un chemin d’accès relatif spécifique. Vous pouvez entrer plusieurs chemins d’accès en les séparant par une virgule. Les URL sont sensibles à la casse. Selon l’exigence, vous pouvez définir des valeurs différentes pour fournir différents niveaux d’accès. Voici quelques scénarios :
        
            Si votre URL est la suivante : http://www.mydomain.com/pictures/city/strasbourg.png. Définissez la valeur d’entrée « » et son niveau d’accès en conséquence

            1. Valeur d’entrée « / » : toutes les demandes sont autorisées
            2. Valeur d’entrée « /pictures » : toutes les demandes suivantes sont autorisées
            
                - http://www.mydomain.com/pictures.png
                - http://www.mydomain.com/pictures/city/strasbourg.png
                - http://www.mydomain.com/picturesnew/city/strasbourgh.png
            3. Valeur d’entrée « /pictures/ » : seules les demandes pour /pictures/ sont autorisées
            4. Valeur d’entrée « /pictures/city/strasbourg.png » : seules les demandes portant sur cette ressource sont autorisées
    
        ![Bouton de gestion du panneau de profil CDN](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
    
        - ec-country-allow : autorise uniquement les demandes provenant d’un ou plusieurs pays spécifiés. Les demandes provenant d’un autre pays seront refusées. Utilisez les codes de pays pour définir les paramètres. Séparez-les par une virgule. Par exemple, si vous souhaitez autoriser l’accès depuis les États-Unis et la France, entrez US, FR dans la colonne comme indiqué ci-dessous.  
        
        ![Bouton de gestion du panneau de profil CDN](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

        - ec-country-deny : refuse les demandes provenant d’un ou plusieurs pays spécifiés. Les demandes provenant d’un autre pays sont autorisées. Utilisez les codes de pays pour définir les paramètres. Séparez-les par une virgule. Par exemple, si vous souhaitez refuser l’accès depuis les États-Unis et la France, entrez US, FR dans la colonne.
    
        - ec-ref-allow : autorise uniquement les demandes provenant du référent spécifié. Un référent identifie l’URL de la page web ayant fourni le lien vers la ressource demandée. La valeur du paramètre de référent ne doit pas inclure le protocole. Vous pouvez entrer un nom d’hôte et/ou un chemin d’accès particulier sur ce nom d’hôte. Vous pouvez également ajouter plusieurs référents au sein d’un même paramètre en les séparant par une virgule. Si vous avez spécifié une valeur de référent, mais que la configuration de navigateur ne permet pas l’envoi des informations sur le référent, les demandes sont refusées par défaut. Vous pouvez affecter la valeur « Missing » ou une valeur vide dans le paramètre pour autoriser les demandes sans informations sur le référent. Vous pouvez également utiliser « *. consoto.com » pour autoriser tous les sous-domaines de consoto.com.  Par exemple, si vous souhaitez autoriser l’accès aux demandes provenant de www.consoto.com et de tous les sous-domaines sous consoto2.com, ainsi que les demandes avec un référent vide ou manquant, entrez la valeur ci-dessous :
        
        ![Bouton de gestion du panneau de profil CDN](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
        - ec-ref-deny : refuse les demandes provenant du référent spécifié. Reportez-vous aux informations détaillées et à l’exemple fournis pour le paramètre « ec-ref-allow ».
         
        - ec-proto-allow : autorise uniquement les demandes correspondant au protocole spécifié. Par exemple, http ou https.
        
        ![Bouton de gestion du panneau de profil CDN](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
            
        - ec-proto-deny : refuse les demandes correspondant au protocole spécifié. Par exemple, http ou https.
    
        - ec-clientip : limite l’accès à l’adresse IP de demandeur spécifiée. Les adresses IPV4 et IPV6 sont prises en charge. Vous pouvez spécifier un sous-réseau IP ou une adresse IP de demande unique.
            
        ![Bouton de gestion du panneau de profil CDN](./media/cdn-token-auth/cdn-token-auth-clientip.png)
        
    3. Vous pouvez tester votre jeton avec l’outil de déchiffrement.

    4. Vous pouvez également personnaliser le type de réponse qui sera renvoyé à l’utilisateur lorsque la demande est refusée. Par défaut, nous utilisons la réponse 403.

3. Cliquez maintenant sur l’onglet **Moteur de règles** sous **HTTP Large**. Cet onglet vous permet de définir les chemins d’accès pour appliquer la fonctionnalité, d’activer la fonctionnalité d’authentification du jeton et d’activer d’autres fonctionnalités d’authentification du jeton.

    - La colonne « IF » permet de définir la ressource ou le chemin d’accès pour lequel vous souhaitez appliquer l’authentification du jeton. 
    - Cliquez sur « Token Auth » (Authentification du jeton) dans la liste déroulante des fonctionnalités pour activer l’authentification du jeton.
        
    ![Bouton de gestion du panneau de profil CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Vous pouvez activer quelques fonctionnalités supplémentaires dans l’onglet **Moteur de règles**.
    
    - Token auth denial code (Code de refus d’authentification du jeton) : détermine le type de réponse à retourner à un utilisateur quand une demande est refusée. Les règles définies ici remplacent le paramètre de code de refus dans l’onglet d’authentification de jeton.
    - Token auth ignore (Ignorer la casse pour l’authentification du jeton) : détermine si la casse de l’URL utilisée pour valider le jeton est prise en compte.
    - Token auth parameter (Paramètre d’authentification du jeton) : renomme le paramètre de chaîne de demande d’authentification du jeton indiquant l’URL demandée. 
        
    ![Bouton de gestion du panneau de profil CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Vous pouvez personnaliser votre jeton, qui est une application qui génère un jeton pour les fonctionnalités d’authentification du jeton. Le code source est accessible dans [GitHub](https://github.com/VerizonDigital/ectoken).
Les langages disponibles sont notamment :
    
    - C
    - C#
    - PHP
    - Perl
    - Java
    - Python    


## <a name="azure-cdn-features-and-provider-pricing"></a>Tarification du fournisseur et des fonctionnalités du CDN Azure

Consultez la rubrique [Présentation du CDN](cdn-overview.md).

