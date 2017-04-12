---
title: "Publier le bureau à distance avec le proxy d’application Azure Active Directory | Microsoft Docs"
description: "Couvre les bases sur les connecteurs de proxy d’application Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: fd0ecc62fd3cfc860423acd02108648e99f44753
ms.lasthandoff: 04/03/2017


---

# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publier le Bureau à distance avec le proxy d’application Azure AD

Cet article explique comment rendre les déploiements des services Bureau à distance Windows accessibles aux utilisateurs distants. Ces déploiements peuvent résider localement ou sur des réseaux privés, comme les déploiements IaaS.

> [!NOTE]
> Le proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou De base d’Azure Active Directory (Azure AD). Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

Le trafic du protocole RDP (Remote Desktop Protocol) peut être publié via le proxy d’application Azure AD en tant qu’application de proxy direct. Cette solution résout le problème de connectivité et fournit une protection de sécurité de base, avec notamment la mise en mémoire tampon réseau, un frontal Internet renforcé et une protection de déni de service distribué (DDoS).

## <a name="remote-desktop-deployment"></a>Déploiement de bureau à distance

Dans le déploiement du Bureau à distance, la passerelle des services Bureau à distance est publiée afin de pouvoir convertir l’appel de procédure distante (RPC) sur HTTPS en trafic RDP sur UDP (User Datagram Protocol).

Vous pouvez configurer les clients afin d’utiliser des clients Bureau à distance, comme MSTSC.exe, pour accéder au proxy d’application Azure AD. Ce faisant, vous créez une connexion HTTPS à la passerelle des services Bureau à distance à l’aide de ses connecteurs. En conséquence, la passerelle n’est pas directement exposée à Internet, et toutes les requêtes HTTPS sont d’abord interrompues dans le cloud.

La topologie est présentée dans le diagramme suivant :

 ![Diagramme Azure AD Services Local](./media/application-proxy-publish-remote-desktop/remote-desktop-topology.png)

## <a name="configure-the-remote-desktop-gateway-url"></a>Configurer l’URL de la passerelle Bureau à distance

Si des utilisateurs configurent l’URL de la passerelle des services Bureau à distance et déclenchent le trafic RDP, comme ils le font généralement, ils peuvent accéder aux fichiers et à d’autres méthodes.

Vous pouvez effectuer la publication en utilisant le nom de domaine fourni par le proxy d’application (msappproxy.net) ou un nom de domaine personnalisé configuré sur Azure AD (par exemple rdg.contoso.com).

Si les appareils clients et le fichier RDP sont déjà configurés avec une URL de passerelle des services Bureau à distance, vous pouvez choisir d’utiliser le même nom de domaine et par conséquent éviter la modification. Dans ce cas, le certificat qui couvre ce domaine doit être fourni au proxy d’application, et sa liste de révocation de certificats doit être accessible via Internet.

Si aucune URL de passerelle des services Bureau à distance n’est configurée, les utilisateurs ou administrateurs peuvent la spécifier dans les clients Bureau à distance (MSTSC), à l’aide de la boîte de dialogue Connexion Bureau à distance, comme illustré ci-dessous.

 ![Boîte de dialogue Connexion Bureau à distance](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-advanced.png)

La boîte de dialogue **Paramètres de connexion** s’affiche lorsque vous cliquez sur **Paramètres** dans l’onglet **Avancé**.

 ![Fenêtre Paramètres de connexion dans la boîte de dialogue Connexion Bureau à distance](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-settings.png)

## <a name="remote-desktop-web-access"></a>Accès web au Bureau à distance

Si votre organisation utilise le portail d’accès web au Bureau à distance (RDWA), vous pouvez également publier à l’aide du proxy d’application Azure AD. Vous pouvez publier sur ce portail avec l’authentification préalable et l’authentification unique (SSO).

La topologie du scénario RDWA est présentée dans le diagramme suivant :

 ![Diagramme du scénario RDWA](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal1.png)

Dans le cas précédent, les utilisateurs sont authentifiés sur Azure AD avant d’accéder à RDWA. S’ils ont déjà été authentifiés sur Azure AD (par exemple, s’ils utilisent Office 365), ils n’ont pas à s’authentifier à nouveau pour RDWA.

Lorsque les utilisateurs démarrent la session RDP, ils doivent s’authentifier à nouveau sur le canal RDP. Cela se produit, car l’authentification unique à partir de RDWA pour la passerelle des services Bureau à distance repose sur le stockage des informations d’identification de l’utilisateur sur le client à l’aide d’ActiveX. Ce processus est déclenché à partir de l’authentification par formulaire RDWA. Si l’authentification RDWA utilise Kerberos, aucune authentification par formulaire n’est présentée, et l’authentification unique RDWA sur RDP ne fonctionne donc pas.

Si RDWA a besoin de l’authentification unique pour le trafic RDP ou si l’authentification par formulaire RDWA a été fortement personnalisée, vous pouvez publier RDWA sans pré-authentification.

La topologie de ce scénario est présentée dans le diagramme suivant :

 ![Diagramme du scénario RDWA](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal2.png)

Dans le cas précédent, les utilisateurs doivent s’authentifier auprès de RDWA à l’aide de l’authentification par formulaire, mais ils n’ont pas à s’authentifier via RDP.

>[!NOTE]
>Dans les deux cas précédents, aucune pré-authentification n’est requise sur le trafic RDP. Les utilisateurs peuvent donc y accéder sans passer d’abord par RDWA.

## <a name="next-steps"></a>Étapes suivantes

[Activer l’accès distant pour SharePoint avec le proxy d’application Azure AD](application-proxy-enable-remote-access-sharepoint.md)  
[Activer le proxy d’application dans le portail Azure](active-directory-application-proxy-enable.md)

