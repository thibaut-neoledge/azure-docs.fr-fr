---
title: "Configurer le protocole LDAPS (LDAP sécurisé) dans les services de domaine Azure AD | Microsoft Docs"
description: "Configurer le protocole LDAPS (LDAP sécurisé) pour un domaine géré par les services de domaine Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 9d563c45-9578-410d-96c8-355af64feae8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 3aafe209aad7383cd0610d147b5fdba673023c93
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


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-classic-azure-portal"></a>Tâche 3 : Activer LDAP sécurisé pour le domaine managé à l’aide du portail Azure Classic
Exécutez les étapes de configuration suivantes pour activer le protocole LDAP sécurisé :

1. Accédez au **[portail Azure Classic](https://manage.windowsazure.com)**.
2. Sélectionnez le nœud **Active Directory** dans le volet gauche.
3. Sélectionnez l’annuaire Azure AD (également appelé « client ») pour lequel vous avez activé les services de domaine Azure Active Directory.

    ![Sélectionner un annuaire Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. Cliquez sur l'onglet **Configurer** .

    ![Configurer l’onglet de l’annuaire](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. Faites défiler la page jusqu’à la section relative aux **services de domaine**. L’option **LDAP sécurisé (LDAPS)** doit s’afficher comme illustré dans la capture d’écran ci-dessous :

    ![Section de configuration des services de domaine](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)
6. Cliquez sur le bouton **Configurer le certificat...** pour ouvrir la boîte de dialogue **Configurer le certificat pour LDAP sécurisé**.

    ![Configurer le certificat pour LDAP sécurisé](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)
7. Cliquez sur l’icône du dossier située après **Fichier PFX avec certificat** pour spécifier le fichier PFX contenant le certificat que vous souhaitez utiliser pour l’accès LDAP sécurisé au domaine managé. Indiquez également le mot de passe spécifié lors de l’exportation du certificat vers le fichier PFX. Puis, cliquez sur le bouton Terminé, au bas de la page.

    ![Spécifier le mot de passe et le fichier PFX pour LDAP sécurisé](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)
8. La section **Services de domaine** de l’onglet **Configurer** doit être grisée ; elle reste à l’état **En attente...** pendant quelques minutes. Pendant cette période, l’exactitude du certificat LDAP sécurisé est vérifiée et le protocole LDAP sécurisé est configuré pour votre domaine géré.

    ![LDAP sécurisé - État En attente](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

   > [!NOTE]
   > L’activation du protocole LDAP sécurisé pour votre domaine géré dure 10 à 15 minutes. Si le certificat LDAP sécurisé fourni ne correspond pas aux critères requis, le protocole LDAP sécurisé n’est pas activé pour votre annuaire et une erreur s’affiche. Par exemple, le nom de domaine est incorrect, le certificat a expiré ou arrivera bientôt à expiration.
   >
   >

9. Lorsque le protocole LDAP sécurisé est activé pour votre domaine géré, le message **En attente...** doit disparaître. Le Thumbprint du certificat doit être affiché.

    ![LDAP sécurisé - Activé](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>

## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>Tâche 4 : Activer l’accès LDAP sécurisé via Internet
**Tâche facultative** : ignorez cette étape de configuration si vous n’envisagez pas d’accéder au domaine géré via le protocole LDAP sécurisé sur Internet.

Avant de commencer cette tâche, vérifiez que vous avez effectué les étapes décrites dans la [Tâche 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-classic-azure-portal).

1. Vous devez voir apparaître une option **ACTIVER L’ACCÈS LDAP SÉCURISÉ SUR INTERNET** dans la section **Services de domaine** de la page **Configurer**. Cette option est définie sur **NON** par défaut, car l’accès Internet au domaine géré via LDAP sécurisé est désactivé par défaut.

    ![LDAP sécurisé - Activé](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)
2. Affectez à l’option **ACTIVER L’ACCÈS LDAP SÉCURISÉ SUR INTERNET** la valeur **OUI**. Cliquez sur le bouton **ENREGISTRER** situé sur le panneau inférieur.
    ![LDAP sécurisé - Activé](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)
3. La section **Services de domaine** de l’onglet **Configurer** doit être grisée ; elle reste à l’état **En attente...** pendant quelques minutes. Après un laps de temps, l’accès Internet à votre domaine géré via LDAP sécurisé est activé.

    ![LDAP sécurisé - État En attente](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

   > [!NOTE]
   > L’activation de l’accès Internet via le protocole LDAP sécurisé pour votre domaine géré dure 10 minutes environ.
   >
   >
4. Lorsque l’accès LDAP sécurisé à votre domaine géré via Internet est activé, le message **En attente...** doit disparaître. Vous devez voir l’adresse IP externe qui peut être utilisée pour accéder à votre annuaire par l’intermédiaire du protocole LDAP sécurisé dans le champ **ADRESSE IP EXTERNE POUR L’ACCÈS LDAPS**.

    ![LDAP sécurisé - Activé](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tâche 5 : Configurer DNS pour accéder au domaine managé à partir d’Internet
**Tâche facultative** : ignorez cette étape de configuration si vous n’envisagez pas d’accéder au domaine géré via le protocole LDAP sécurisé sur Internet.

Avant de commencer cette tâche, vérifiez que vous avez effectué les étapes décrites dans la [Tâche 4](#task-4---enable-secure-ldap-access-over-the-internet).

Une fois l’accès LDAP sécurisé via Internet activé pour le domaine géré, vous devez mettre à jour DNS, afin que les ordinateurs clients puissent détecter ce domaine. À la fin de la tâche 4, une adresse IP externe est affichée sur l’onglet **Configurer** de la page **ADRESSE IP EXTERNE POUR L’ACCÈS LDAPS**.

Configurez votre fournisseur DNS externe afin que le nom DNS du domaine géré (par exemple, 'ldaps.contoso100.com') pointe vers cette adresse IP externe. Dans notre exemple, nous devons créer l’entrée DNS suivante :

    ldaps.contoso100.com  -> 52.165.38.113

Et voilà, vous êtes maintenant prêt à vous connecter au domaine géré à l’aide du protocole LDAP sécurisé sur Internet.

> [!WARNING]
> N’oubliez pas que les ordinateurs clients doivent approuver l’émetteur du certificat LDAP sécurisé afin d’être en mesure de se connecter au domaine géré à l’aide du protocole LDAP sécurisé. Si vous utilisez une autorité de certification d’entreprise ou une autorité de certification approuvée publiquement, vous n’avez rien à faire, car les ordinateurs clients approuvent ces émetteurs de certificats. Si vous utilisez un certificat auto-signé, vous devez installer la partie publique du certificat auto-signé dans le magasin de certificats de confiance sur l’ordinateur client.
>
>


## <a name="lock-down-ldaps-access-to-your-managed-domain-over-the-internet"></a>Verrouiller l’accès LDAPS à votre domaine managé via internet
> [!NOTE]
> **Tâche facultative** : si vous n’avez pas activé l’accès LDAPS au domaine managé via internet, ignorez cette étape de configuration.
>
>

Avant de commencer cette tâche, vérifiez que vous avez effectué les étapes décrites dans la [Tâche 4](#task-4---enable-secure-ldap-access-over-the-internet).

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

