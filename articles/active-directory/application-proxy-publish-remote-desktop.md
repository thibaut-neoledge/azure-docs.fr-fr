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
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 40ddf7d6648561eab855b41e76a5ca509c6ca4ef
ms.openlocfilehash: 05130d728c579e0b778bdc8ec49e01f2223c2bba


---

# <a name="publish-remote-desktop-with-azure-active-directory-application-proxy"></a>Publier le bureau à distance avec le proxy d’application Azure Active Directory

Cet article explique comment rendre accessibles les déploiements de bureau à distance pour les utilisateurs distants. De tels déploiements de bureau à distance peuvent résider localement ou sur des réseaux privés, comme les déploiements IaaS. 

> [!NOTE]
> Le proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou De base d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).
> 
 

Le trafic du protocole de bureau à distance peut être publié via le proxy d’application en tant qu’application de proxy direct. Cette solution résout le problème de connectivité et fournit une protection de sécurité de base, avec notamment la mise en mémoire tampon réseau, un frontal Internet renforcé et une protection DDoS. 

##<a name="remote-desktop-deployment"></a>Déploiement de bureau à distance

Dans le déploiement de bureau à distance, la passerelle des services Bureau à distance est publiée afin d’être en mesure de convertir le trafic RPC sur HTTPS en trafic RDP sur UDP.

Vous pouvez configurer vos clients afin d’utiliser des clients de bureau à distance, comme MSTSC.exe, pour accéder au proxy d’application Azure AD. Cela vous permet de créer une nouvelle connexion HTTPS pour la passerelle Bureau à distance avec ses connecteurs. Ce faisant, la passerelle Bureau à distance ne sera pas exposée directement à Internet, toutes les requêtes HTTPS seront d’abord interrompues dans le cloud. 

Le diagramme ci-dessous illustre cette topologie.

 ![AzureAD Services Local](./media/application-proxy-publish-remote-desktop/remote-desktop-topology.png)

## <a name="configure-the-remote-desktop-gateway-url"></a>Configurer l’URL de la passerelle Bureau à distance

Tant que vos utilisateurs configurent l’URL de passerelle Bureau à distance, lorsqu’ils déclenchent le trafic RDP comme ils le font généralement, ils seront en mesure d’accéder aux fichiers et autres méthodes.

La publication peut être accomplie en utilisant soit le nom de domaine fourni par le proxy d’application (msappproxy.net) soit un nom de domaine personnalisé configuré sur Azure AD, par exemple rdg.contoso.com. 

Si vos appareils clients et votre fichier RDP sont déjà configurés avec une URL de passerelle Bureau à distance, vous pouvez choisir d’utiliser le même nom de domaine et par conséquent éviter la modification. Dans ce cas, le certificat qui couvre ce domaine doit être fourni au proxy d’application, et ses CRL doivent être accessibles via Internet.

Si aucune URL de passerelle Bureau à distance n’est configurée, les utilisateurs ou les administrateurs peuvent la spécifier dans les clients Bureau à distance (MSTSC), à l’aide de la zone Connexion Bureau à distance, comme illustré ci-dessous.

 ![AzureAD Services Local](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-advanced.png)

La zone Paramètres de connexion s’affiche lorsque vous cliquez sur **Paramètres** dans l’onglet **Avancé**.

 ![AzureAD Services Local](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-settings.png)

## <a name="remote-desktop-web-access"></a>Accès web au Bureau à distance

Si votre organisation utilise le portail d’accès web au Bureau à distance (RDWA), vous pouvez également publier à l’aide du proxy d’application Azure AD. Vous pouvez publier sur ce portail avec l’authentification préalable et l’authentification unique (SSO).

Le diagramme ci-dessous illustre cette topologie pour ce scénario.

 ![AzureAD Services Local](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal1.png)

Dans ce cas, les utilisateurs seront authentifiés sur Azure AD avant d’accéder à RDWA. S’ils ont déjà été authentifiés sur Azure AD (par exemple, s’ils utilisent Office 365), ils n’ont pas à s’authentifier à nouveau pour RDWA.

Lorsque vos utilisateurs démarrent la session RDP, ils doivent s’authentifier à nouveau sur le canal RDP. Cela se produit car l’authentification unique à partir de RDWA pour la passerelle Bureau à distance repose sur le stockage des informations d’identification de l’utilisateur final sur le client à l’aide d’ActiveX. Ce processus est déclenché à partir de l’authentification par formulaire RDWA. Lorsque l’authentification RDWA utilise Kerbros, aucune authentification par formulaire n’est présentée, et par conséquent l’authentification unique RDWA sur RDP ne fonctionnera pas.

Si RDWA a besoin de l’authentification unique pour le trafic RDP ou que l’authentification par formulaire RDWA a été fortement personnalisée, il est possible de publier RDWA sans la pré-authentification.

Le diagramme ci-dessous illustre cette topologie pour ce scénario.

 ![AzureAD Services Local](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal2.png)

Dans le cas ci-dessus, vos utilisateurs doivent s’authentifier auprès de RDWA à l’aide de l’authentification par formulaire, mais ils n’auront pas à s’authentifier via RDP. 

Il est important de noter dans les deux cas, il n’existe aucune authentification préalable requise pour le trafic RDP. Par conséquent, les utilisateurs peuvent y accéder sans passer d’abord par RDWA.

##<a name="next-steps"></a>Étapes suivantes

[Activer l’accès distant pour SharePoint avec le proxy d’application Azure AD](application-proxy-enable-remote-access-sharepoint.md)<br>
[Activer le proxy d’application dans le Portail Azure](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)



<!--HONumber=Feb17_HO1-->


