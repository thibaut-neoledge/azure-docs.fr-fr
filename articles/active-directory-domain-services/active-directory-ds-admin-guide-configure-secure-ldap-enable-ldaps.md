---
title: "Configurer le protocole LDAPS (LDAP sécurisé) dans les services de domaine Azure AD | Microsoft Docs"
description: "Configurer le protocole LDAPS (LDAP sécurisé) pour un domaine géré par les services de domaine Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 3b19f078b0d6dc3e02d951014056406fd1b099a8
ms.contentlocale: fr-fr
ms.lasthandoff: 08/16/2017

---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurer le protocole LDAPS (LDAP sécurisé) pour un domaine managé Azure AD Domain Services

## <a name="before-you-begin"></a>Avant de commencer
Vérifiez que vous avez accompli la [Tâche 2 : Exporter le certificat du protocole LDAP sécurisé vers un fichier .PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).

Choisissez entre utiliser le portail Azure en préversion ou le portail Azure Classic pour effectuer cette tâche.
> [!div class="op_single_selector"]
> * **Portail Azure (préversion)** : [Activer LDAP sécurisé à l’aide du portail Azure](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
> * **Portail Azure Classic** : [Activer LDAP sécurisé à l’aide du portail Azure Classic](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps-classic.md)
>
>


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview"></a>Tâche 3 : Activer LDAP sécurisé pour le domaine managé à l’aide du portail Azure (préversion)
Exécutez les étapes de configuration suivantes pour activer le protocole LDAP sécurisé :

1. Accédez au **[portail Azure](https://portal.azure.com)**.

2. Recherchez « Domain Services » dans la zone de recherche **Rechercher des ressources**. Sélectionnez **Azure AD Domain Services** dans les résultats de la recherche. Le panneau **Azure AD Domain Services** répertorie votre domaine managé.

    ![Rechercher le domaine managé en cours d’approvisionnement](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Pour plus d’informations sur le domaine, cliquez sur le nom du domaine managé (par exemple, « contoso100.com »).

    ![Domain Services - État d’approvisionnement](./media/getting-started/domain-services-provisioning-state.png)

3. Cliquez sur **LDAP sécurisé** dans le volet de navigation.

    ![Domain Services - Panneau LDAP sécurisé](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Par défaut, l’accès LDAP sécurisé à votre domaine managé est désactivé. Basculez **LDAP sécurisé** sur **Activer**.

    ![Activer LDAP sécurisé](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Par défaut, l’accès LDAP sécurisé à votre domaine managé via Internet est désactivé. Si vous le souhaitez, basculez **Autorisez l’accès LDAP sécurisé sur Internet** sur **Activer**. 

6. Cliquez sur l’icône de dossier suivant le fichier **.PFX avec certificat LDAP sécurisé**. Spécifiez le chemin d’accès au fichier PFX avec le certificat pour l’accès LDAP sécurisé au domaine managé.

7. Spécifiez le **mot de passe pour déchiffrer le fichier .PFX**. Fournissez le mot de passe que vous avez utilisé lors de l’exportation du certificat vers le fichier PFX.

8. Lorsque vous avez terminé, cliquez sur le bouton **Enregistrer**.

9. Vous voyez une notification vous informant que le LDAP sécurisé est en cours de configuration pour le domaine managé. Tant que cette opération n’est pas terminée, vous ne pouvez pas modifier d’autres paramètres du domaine.

    ![Configuration de LDAP sécurisé pour le domaine managé](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> L’activation du protocole LDAP sécurisé pour votre domaine géré dure 10 à 15 minutes. Si le certificat LDAP sécurisé fourni ne correspond pas aux critères requis, le protocole LDAP sécurisé n’est pas activé pour votre annuaire et une erreur s’affiche. Par exemple, le nom de domaine est incorrect, le certificat a expiré ou arrivera bientôt à expiration. Dans ce cas, réessayez avec un certificat valide.
>
>

<br>

## <a name="task-4---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tâche 4 : Configurer DNS pour accéder au domaine managé à partir d’Internet
> [!NOTE]
> **Tâche facultative** : ignorez cette étape de configuration si vous n’envisagez pas d’accéder au domaine géré via le protocole LDAP sécurisé sur Internet.
>
>

Avant de commencer cette tâche, vérifiez que vous avez effectué les étapes décrites dans la [Tâche 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

Une fois l’accès LDAP sécurisé via Internet activé pour le domaine géré, vous devez mettre à jour DNS, afin que les ordinateurs clients puissent détecter ce domaine. À la fin de la tâche 3, une adresse IP externe s’affiche sur le panneau **Configurer** dans **Adresse IP externe pour l’accès LDAPS**.

Configurez votre fournisseur DNS externe afin que le nom DNS du domaine géré (par exemple, 'ldaps.contoso100.com') pointe vers cette adresse IP externe. Dans notre exemple, nous devons créer l’entrée DNS suivante :

    ldaps.contoso100.com  -> 52.165.38.113

Et voilà, vous êtes maintenant prêt à vous connecter au domaine géré à l’aide du protocole LDAP sécurisé sur Internet.

> [!WARNING]
> N’oubliez pas que les ordinateurs clients doivent approuver l’émetteur du certificat LDAP sécurisé afin d’être en mesure de se connecter au domaine géré à l’aide du protocole LDAP sécurisé. Si vous utilisez une autorité de certification approuvée publiquement, vous n’avez rien à faire, car les ordinateurs clients approuvent ces émetteurs de certificats. Si vous utilisez un certificat auto-signé, installez la partie publique du certificat auto-signé dans le magasin de certificats de confiance sur l’ordinateur client.
>
>


## <a name="task-5---lock-down-ldaps-access-to-your-managed-domain-over-the-internet"></a>Tâche 5 : Verrouiller l’accès LDAPS à votre domaine managé via internet
> [!NOTE]
> **Tâche facultative** : si vous n’avez pas activé l’accès LDAPS au domaine managé via internet, ignorez cette étape de configuration.
>
>

Avant de commencer cette tâche, vérifiez que vous avez effectué les étapes décrites dans la [Tâche 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

L’exposition de votre domaine managé pour l’accès LDAPS via Internet constitue une menace pour la sécurité. Le domaine managé est accessible à partir d’Internet sur le port utilisé pour le LDAP sécurisé (port 636). Par conséquent, vous pouvez choisir de restreindre l’accès au domaine managé à des adresses IP connues spécifiques. Pour améliorer la sécurité, créez un groupe de sécurité réseau (NSG) et associez-le au réseau virtuel dans lequel est activé Azure AD Domain Services.

Le tableau suivant illustre un exemple de groupe de sécurité réseau que vous pouvez configurer pour verrouiller l’accès LDAP sécurisé via Internet. Le groupe de sécurité réseau contient un ensemble de règles qui autorisent l’accès LDAPS entrant sur le port TCP 636 uniquement à partir d’un ensemble spécifique d’adresses IP. La règle « DenyAll » par défaut s’applique à tout autre trafic entrant en provenance d’internet. La règle de groupe de sécurité réseau pour autoriser l’accès LDAPS via Internet à partir d’adresses IP spécifiées prend le pas sur la règle DenyAll NSG.

![Exemple de groupe de sécurité réseau pour l’accès LDAP sécurisé via internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Plus d’informations** - [Groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

<br>

## <a name="related-content"></a>Contenu connexe
* [Services de domaine Azure AD : guide de prise en main](active-directory-ds-getting-started.md)
* [Administrer un domaine géré par les services de domaine Azure Active Directory](active-directory-ds-admin-guide-administer-domain.md)
* [Gérer la stratégie de groupe sur un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-administer-group-policy.md)
* [Groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md)
* [Créer des groupes de sécurité réseau à l’aide du portail Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

