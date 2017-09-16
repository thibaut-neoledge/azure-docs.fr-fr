---
title: "Accès conditionnel Azure Active Directory pour la connectivité de réseau privé virtuel (préversion) | Microsoft Docs"
description: "Découvrez le fonctionnement de l’accès conditionnel Azure Active Directory pour la connectivité de réseau privé virtuel. "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: 540d8974ee2c02f80bccf28764b4d0d243e98d85
ms.contentlocale: fr-fr
ms.lasthandoff: 09/02/2017

---
# <a name="azure-active-directory-conditional-access-for-virtual-private-network-connectivity-preview"></a>Accès conditionnel Azure Active Directory pour la connectivité de réseau privé virtuel (préversion)

Avec l’[accès conditionnel Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), vous pouvez préciser les méthodes d’accès des utilisateurs autorisés aux ressources. L’accès conditionnel Azure AD pour la connectivité de réseau privé virtuel (VPN) vous permet d’utiliser l’accès conditionnel pour protéger vos connexions VPN.


Pour configurer l’accès conditionnel Azure AD pour la connectivité VPN, vous devez effectuer les étapes suivantes : 

1.  Configurer votre serveur VPN
2.  Configurer votre client VPN 
3.  Configurer votre stratégie d’accès conditionnel
4.  Vérification


## <a name="before-you-begin"></a>Avant de commencer

Cette rubrique part du principe que vous connaissez les sujets suivants :

- [Accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal.md)
- [VPN et accès conditionnel](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

Vous souhaiterez peut-être aussi jeter un coup d’œil à l’article [Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile) (Amélioration de l’accès à distance dans Windows 10 avec un profil VPN automatique) pour découvrir comment Microsoft a implémenté cette fonctionnalité.   


## <a name="prerequisites"></a>Prérequis

Pour configurer l’accès conditionnel Azure Active Directory pour la connectivité de réseau privé virtuel, vous devez disposer d’un serveur VPN configuré. 



## <a name="step-1---configure-your-vpn-server"></a>Étape 1 : configurer votre serveur VPN 

L’objectif de cette étape consiste à configurer des certificats racines pour l’authentification VPN auprès d’Azure AD. Pour configurer l’accès conditionnel pour la connectivité de réseau privé virtuel, vous devez :

1. Créer un certificat VPN dans le portail Azure
2. Télécharger le certificat VPN
2. Déployer le certificat sur votre serveur VPN

Le certificat VPN est l’émetteur utilisé par Azure AD pour signer les certificats émis pour les clients Windows 10 lors de l’authentification auprès d’Azure AD pour la connectivité VPN. Imaginez que le jeton demandé par le client Windows 10 est un certificat qui est ensuite présenté à l’application, qui dans le cas présent est le serveur VPN.

![Accès conditionnel](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

Dans le portail Azure, vous pouvez créer deux certificats pour gérer les transitions quand un certificat est sur le point d’expirer. Quand vous créez un certificat, vous pouvez choisir s’il s’agit du certificat principal. Le certificat principal est celui qui est utilisé lors de l’authentification pour signer le certificat pour la connexion.


**Pour créer un certificat VPN**

1. Connectez-vous à votre [portail Azure](https://portal.azure.com) en tant qu’administrateur général.

2. Dans la barre de navigation gauche, cliquez sur **Azure Active Directory**. 

    ![Connectivité VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. Dans la page **Azure Active Directory**, dans la section **Gérer**, cliquez sur **Accès conditionnel**.

    ![Connectivité VPN](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. Dans la page **Accès conditionnel**, dans la section **Gérer**, cliquez sur **Connectivité VPN (préversion)**.

    ![Connectivité VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. Dans la page **Connectivité VPN**, cliquez sur **Nouveau certificat**.

    ![Connectivité VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. Dans la page **Nouveau**, effectuez les étapes suivantes :

    ![Connectivité VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. Pour **Durée**, sélectionnez **1 an**.

    b. Pour **Principal**, sélectionnez **Oui**.

    c. Cliquez sur **Créer**.

7. Dans la page de connectivité VPN, cliquez sur **Télécharger le certificat**.


À ce stade, vous êtes prêt à déployer votre nouveau certificat sur votre serveur VPN. Sur votre serveur VPN, vous devez ajouter le certificat téléchargé en tant qu’*autorité de certification racine de confiance pour l’authentification VPN*.

Pour les déploiements de Windows basés sur RRAS, sur votre serveur NPS, vous devez ajouter le certificat racine dans le magasin *Enterprise NTauth* en exécutant les commandes suivantes :

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2---configure-your-vpn-client"></a>Étape 2 : configurer votre client VPN 

Lors de cette étape, vous devez configurer votre profil de connectivité cliente VPN comme indiqué dans [VPN et accès conditionnel](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access).


## <a name="step-3---configure-your-conditional-access-policy"></a>Étape 3 : configurer votre stratégie d’accès conditionnel

Cette section explique comment configurer votre stratégie d’accès conditionnel pour la connectivité VPN.

**Pour configurer la stratégie d’accès conditionnel** 

1. Dans la page **Accès conditionnel**, dans la barre d’outils en haut, cliquez sur **Ajouter**.

    ![Accès conditionnel](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. Dans la page **Nouveau**, dans la zone de texte **Nom**, tapez un nom pour votre stratégie, par exemple **Stratégie VPN**.

    ![Accès conditionnel](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. Dans la section **Affectation**, cliquez sur **Utilisateurs et groupes**.

    ![Accès conditionnel](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. Dans la page **Utilisateurs et groupes**, effectuez les étapes suivantes :

    ![Accès conditionnel](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. Cliquez sur **Sélectionner des utilisateurs et des groupes**.

    b. Cliquez sur **Sélectionner**.

    c. Dans la page **Sélectionner**, sélectionnez votre utilisateur de test, puis cliquez sur **Sélectionner**.

    d. Dans la page **Utilisateurs et groupes**, cliquez sur **Terminé**.

7. Dans la page **Nouveau**, effectuez les étapes suivantes.

    ![Accès conditionnel](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. Dans la section **Affectations**, cliquez sur **Applications cloud**.

    b. Dans la page **Applications cloud**, cliquez sur **Sélectionner les applications**, puis sur **Sélectionner**.

    c. Dans la page **Sélectionner**, dans la zone de texte **Applications**, tapez **vpn**.

    d. Sélectionnez **Serveur VPN**.

    e. Cliquez sur **Sélectionner**.


13. Dans la page **Nouveau**, pour ouvrir la page **Octroyer**, cliquez sur **Octroyer** dans la section **Contrôles**.

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. Dans la page **Octroyer**, effectuez les étapes suivantes :

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Sélectionnez **Exiger une authentification multifacteur**.

    b. Cliquez sur **Sélectionner**.

15. Dans la page **Nouveau**, sous **Activer la stratégie**, cliquez sur **Activé**.

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. Dans la page **Nouveau**, cliquez sur **Créer**.



## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment Microsoft a implémenté cette fonctionnalité, consultez [Enhancing remote access in Windows 10 with an automatic VPN profile (Amélioration de l’accès à distance dans Windows 10 avec un profil VPN automatique)](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile).    


