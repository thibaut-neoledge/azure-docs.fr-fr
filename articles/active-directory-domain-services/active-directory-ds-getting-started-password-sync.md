---
title: "Azure Active Directory Domain Services : activer la synchronisation de mot de passe | Microsoft Docs"
description: Prise en main des services de domaine Azure Active Directory
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 51e4665b6372859021237144a0bf7f6763a2638b
ms.lasthandoff: 04/14/2017


---
# <a name="enable-password-synchronization-with-azure-active-directory-domain-services"></a>Activer la synchronisation de mot de passe pour Azure Active Directory Domain Services
Dans les tâches précédentes, vous avez activé Azure Active Directory Domain Services (Azure AD DS) pour votre locataire Azure Active Directory (Azure AD). Dans la tâche suivante, vous allez activer les hachages d’informations d’identification, qui sont requis pour que l’authentification NTLM (NT LAN Manager) et Kerberos se synchronise avec Azure AD DS. Une fois la synchronisation des informations d’identification configurée, les utilisateurs peuvent se connecter au domaine managé à l’aide de leurs informations d’identification d’entreprise.

Les étapes sont différentes selon que votre organisation dispose d’un locataire Azure AD dans le cloud uniquement ou est configurée de manière à se synchroniser avec votre annuaire local à l’aide d’Azure AD Connect.

> [!div class="op_single_selector"]
> * [Client Azure AD dans le cloud uniquement](active-directory-ds-getting-started-password-sync.md)
> * [Client Azure AD synchronisé](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

## <a name="task-5-enable-password-synchronization-with-azure-active-directory-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Tâche 5 : activer la synchronisation de mot de passe avec Azure AD DS pour un locataire Azure AD dans le cloud uniquement
Pour authentifier les utilisateurs sur le domaine managé, Azure AD DS exige que les hachages d’informations d’identification présentent un format adapté à l’authentification NTLM et Kerberos. À moins que vous n’activiez Azure AD DS pour votre locataire, Azure AD ne génère pas et ne stocke pas les hachages d’informations d’identification dans le format requis pour l’authentification NTLM ou Kerberos. Pour des raisons évidentes de sécurité, Azure AD ne stocke pas non plus d’informations d’identification dans un format en texte clair. Par conséquent, Azure AD n’a pas la capacité de générer ces hachages d’informations d’identification NTLM ou Kerberos en fonction des informations d’identification existantes des utilisateurs.

> [!NOTE]
> Si votre organisation inclut un locataire Azure AD dans le cloud uniquement, les utilisateurs ayant besoin d’utiliser Azure AD DS doivent modifier leur mot de passe.
>
>

Le processus de modification du mot de passe entraîne la génération, dans Azure AD, des hachages d’informations d’identification requis par Azure AD DS pour l’authentification Kerberos et NTLM. Vous pouvez faire expirer les mots de passe de tous les utilisateurs du locataire qui doivent recourir à Azure AD DS, ou demander à ces utilisateurs de modifier leur mot de passe.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Activer la génération du hachage des informations d’identification NTLM et Kerberos pour un client Azure AD dans le cloud uniquement
Voici les instructions que vous devez fournir aux utilisateurs pour qu’ils puissent modifier leur mot de passe :

1. Accédez à la [page du volet d’accès Azure AD](http://myapps.microsoft.com) de votre organisation.
2. Dans la fenêtre du volet d’accès, sélectionnez l’onglet **Profil**.
3. Cliquez sur la mosaïque **Modifier le mot de passe**.

    ![Mosaïque Modifier le mot de passe du volet d’accès Azure AD](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > Si l’option **Modifier le mot de passe** ne s’affiche pas sur la fenêtre du volet d’accès, vérifiez que votre organisation a configuré la [gestion des mots de passe dans Azure AD](../active-directory/active-directory-passwords-getting-started.md).
   >
   >
4. Sur la page **Modifier le mot de passe**, saisissez votre ancien mot de passe, puis tapez un nouveau mot de passe et confirmez-le.

    ![Créer un réseau virtuel pour les services de domaine Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Cliquez sur **Envoyer**.

Quelques minutes après cette modification, le nouveau mot de passe est utilisable Azure AD DS. Après 20 minutes environ (en général), vous pouvez vous connecter aux ordinateurs joints au domaine managé à l’aide du nouveau mot de passe.

## <a name="next-steps"></a>Étapes suivantes
* [Comment mettre à jour votre mot de passe](../active-directory/active-directory-passwords-update-your-own-password.md#reset-my-password)
* [Prise en main de la gestion de mot de passe dans Azure AD](../active-directory/active-directory-passwords-getting-started.md)
* [Activer la synchronisation de mot de passe dans Azure Active Directory Domain Services pour un locataire Azure AD synchronisé](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Administrer un domaine managé par Azure Active Directory Domain Services ](active-directory-ds-admin-guide-administer-domain.md)
* [Joindre une machine virtuelle Windows à un domaine managé par Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Joindre une machine virtuelle Red Hat Enterprise Linux à un domaine managé par Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

