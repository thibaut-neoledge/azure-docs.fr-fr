---
title: "Publier le Bureau à distance avec le proxy d’application Azure AD | Microsoft Docs"
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
ms.date: 04/21/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 9724ad2e460837157c7677d2c91493cebc8f7012
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017


---

# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publier le Bureau à distance avec le proxy d’application Azure AD

Cet article explique comment déployer les Services Bureau à distance (RDS) avec le proxy d’application de manière à ce que les utilisateurs distants puissent toujours être productifs. 

Le public concerné par cet article est le suivant :
- Clients actuels du proxy d’application Azure AD qui souhaitent proposer plus d’applications à leurs utilisateurs finaux en publiant des applications locales via les Services Bureau à distance. 
- Clients actuels des Services Bureau à distance qui souhaitent réduire la surface d’attaque de leur déploiement à l’aide du proxy d’application Azure AD. Ce scénario propose un ensemble limité de contrôles de vérification en deux étapes et d’accès conditionnel à RDS.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Comment le proxy d’application s’intègre dans le déploiement RDS standard

Un déploiement RDS standard inclut divers services du rôle Bureau à distance s’exécutant sur Windows Server. [L’architecture des Services Bureau à distance](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) offre plusieurs options de déploiement. La principale différence entre le [déploiement RDS avec le proxy d’application Azure AD](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (illustré dans le diagramme suivant) et les autres options de déploiement est que le scénario du proxy d’application comporte une connexion sortante permanente sur le serveur exécutant le service de connecteur. Les autres déploiements comportent des connexions entrantes ouvertes via un équilibreur de charge. 

![Le proxy d’application se situe entre la VM RDS et l’Internet public](./media/application-proxy-publish-remote-desktop/rds-with-app-proxy.png)

Dans un déploiement RDS, le rôle Site Web Bureau à distance et le rôle Passerelle Bureau à distance s’exécutent sur des ordinateurs connectés à Internet. Ces points de terminaison sont exposés pour les raisons suivantes :
- Le rôle Site Web Bureau à distance fournit à l’utilisateur un point de terminaison public pour se connecter et afficher les applications locales et les postes de travail auxquels il peut se connecter. Lors de la sélection d’une ressource, une connexion RDP est créée à l’aide de l’application native sur le système d’exploitation.
- Le rôle Passerelle Bureau à distance apparaît lorsque l’utilisateur lance la connexion RDP. Ce rôle gère le trafic RDP chiffré provenant d’Internet et le convertit sur le serveur local auquel l’utilisateur est connecté. Dans ce scénario, le trafic reçu par la Passerelle Bureau à distance provient du proxy d’application Azure AD.

>[!TIP]
>Si vous n’avez pas déployé RDS au préalable, ou si vous souhaitez en savoir plus avant de commencer, découvrez comment [déployer RDS de manière transparente avec Azure Resource Manager et Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Configuration requise

Les points de terminaison du rôle Site Web Bureau à distance et du rôle Passerelle Bureau à distance doivent se trouver sur le même ordinateur et avoir une racine commune. Le rôle Site Web Bureau à distance et le rôle Passerelle Bureau à distance sont publiés sous la forme d’une seule application afin de vous proposer une expérience d’authentification unique pour les deux applications. 

Vous devez déjà avoir [déployé RDS](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure) et [activé le proxy d’application](active-directory-application-proxy-enable.md). 

Vos utilisateurs finaux peuvent uniquement accéder à ce scénario via les ordinateurs de bureau Windows 7 et Windows 10 qui se connectent via la page Site Web Bureau à distance. Ce scénario n’est pas pris en charge sur d’autres systèmes d’exploitation, y compris ceux incluant des applications Bureau à distance Microsoft.

Vos utilisateurs finaux doivent utiliser Internet Explorer et activer le module complémentaire ActiveX de RDS lors de la connexion à leurs ressources. 

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Déploiement du scénario associant RDS et proxy d’application

Après avoir configuré RDS et le proxy d’application Azure AD pour votre environnement, suivez les étapes permettant de combiner les deux solutions. Ces étapes expliquent comment publier les deux points de terminaison RDS orientés Web (Site Web Bureau à distance et Passerelle Bureau à distance) en tant qu’applications, puis diriger le trafic de votre RDS pour qu’il traverse le proxy d’application.

### <a name="publish-the-rd-host-endpoint"></a>Publication du point de terminaison hôte Bureau à distance

1. [Publiez une nouvelle application de proxy d’application](application-proxy-publish-azure-portal.md) avec les valeurs suivantes :
   - URL interne : https://\<rdhost\>.com /, où \<rdhost\> est la racine commune partagée par Site Web Bureau à distance et Passerelle Bureau à distance. 
   - URL externe : ce champ est automatiquement renseigné en fonction du nom de l’application, mais vous pouvez le modifier. Vos utilisateurs accéderont à cet URL en accédant à RDS. 
   - Méthode de pré-authentification : Azure Active Directory
   - Traduire des URL dans les en-têtes : Non
2. Affectez des utilisateurs à l’application Bureau à distance publiée. Assurez-vous également qu’ils ont tous accès à RDS.
3. Conservez la méthode d’authentification unique de l’application **Authentification unique Azure AD désactivée**. Les utilisateurs sont invités à s’authentifier une fois sur Azure AD et une fois sur Site Web Bureau à distance, mais profitent de l’authentification unique pour Passerelle Bureau à distance. 
4. Accédez à **Azure Active Directory** > **Inscriptions des applications** > *Votre application* > **Paramètres**. 
5. Sélectionnez **Propriétés** et mettez à jour le champ **URL de la page d’accueil** pour pointer vers votre point de terminaison Site Web Bureau à distance (par ex., https://\<rdhost\>.com/RDWeb).

### <a name="direct-rds-traffic-to-application-proxy"></a>Trafic RDS direct vers le proxy d’application

Connectez-vous au déploiement RDS en tant qu’administrateur et modifiez le nom du serveur Passerelle Bureau à distance pour le déploiement. Ainsi vous garantissez que les connexions traversent le proxy d’application Azure AD.

1. Connectez-vous au serveur RDS exécutant le rôle Service Broker pour les connexions Bureau à distance.
2. Lancez le **Gestionnaire de serveur**.
3. Sélectionnez **Services Bureau à distance** dans le volet de gauche.
4. Sélectionnez **Vue d’ensemble**.
5. Dans la section Vue d’ensemble du déploiement, sélectionnez le menu déroulant et choisissez **Modifier les propriétés de déploiement**.
6. Dans l’onglet Passerelle Bureau à distance, modifiez le champ **Nom du serveur** en entrant l’URL externe définie pour le point de terminaison hôte Bureau à distance dans le proxy d’application. 
7. Changez le champ **Méthode de connexion** en **Authentification par mot de passe**.

  ![Écran Propriétés de déploiement sur RDS](./media/application-proxy-publish-remote-desktop/rds-deployment-properties.png)

8. Pour chaque collection, exécutez la commande suivante. Remplacez *\<yourcollectionname\>* et *\<proxyfrontendurl\>* par vos propres informations. Cette commande active l’authentification unique entre Site Web Bureau à distance et Passerelle Bureau à distance, et optimise les performances :

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s: <proxyfrontendurl> `n require pre-authentication:i:1"
   ```

Maintenant que vous avez configuré le Bureau à distance, le proxy d’application Azure AD a repris le dessus comme composant accessible sur Internet de RDS. Vous pouvez supprimer les autres points de terminaison accessibles sur Internet de vos ordinateurs Site Web Bureau à distance et Passerelle Bureau à distance. 

## <a name="test-the-scenario"></a>Test du scénario

Testez le scénario avec Internet Explorer sur un ordinateur Windows 7 ou 10.

1. Accédez à l’URL externe que vous configurez ou recherchez votre application dans le [panneau MyApps](https://myapps.microsoft.com).
2. Vous êtes invité à vous authentifier sur Azure Active Directory. Utilisez un compte de test que vous avez attribué à l’application.
3. Vous êtes invité à vous authentifier sur Site Web Bureau à distance. 
4. Une fois authentifié sur RDS, vous pouvez sélectionner l’application ou le bureau de votre choix et commencer à travailler. 

## <a name="next-steps"></a>Étapes suivantes

[Activer l’accès distant pour SharePoint avec le proxy d’application Azure AD](application-proxy-enable-remote-access-sharepoint.md)  
[Considérations de sécurité pour l’accès aux applications à distance à l’aide du proxy d’application Azure AD](application-proxy-security-considerations.md)

