---
title: "Didacticiel : configuration de Google Apps pour l’approvisionnement automatique d’utilisateurs dans Azure | Documents Microsoft"
description: "Découvrez comment approvisionner et déprovisionner automatiquement des comptes utilisateur d’Azure AD vers Google Apps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: b061f0ddad70be4a5ca48d48d1a737d6af8afa8d
ms.contentlocale: fr-fr
ms.lasthandoff: 07/14/2017

---
# <a name="tutorial-configuring-google-apps-for-automatic-user-provisioning"></a>Didacticiel : configuration de Google Apps pour approvisionner automatiquement des utilisateurs

L’objectif de ce didacticiel est de vous indiquer les étapes à suivre dans Google Apps et Azure AD pour approvisionner et déprovisionner automatiquement des comptes utilisateur d’Azure AD vers Google Apps.

## <a name="prerequisites"></a>Composants requis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   Un client Azure Active Directory.
*   Vous devez avoir un client valide pour Google Apps pour Travailler ou Google Apps pour l’Éducation. Vous pouvez utiliser un compte d’essai gratuit pour chaque service.
*   Un compte utilisateur dans Google Apps avec les autorisations d’administrateur d’équipe.

## <a name="assigning-users-to-google-apps"></a>Attribution d’utilisateurs à Google Apps

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique de comptes utilisateur, seuls les utilisateurs et les groupes qui ont été « attribués » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et/ou groupes dans Azure AD représentent les utilisateurs qui ont besoin d’accéder à votre application Google Apps. Une fois choisi, vous pouvez attribuer ces utilisateurs à votre application Google Apps en suivant les instructions fournies ici : [Attribuer un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

> [!IMPORTANT]
>*   Nous vous recommandons de n’attribuer qu’un seul utilisateur Azure AD à Google Apps pour tester la configuration de l’approvisionnement. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.
>*   Lorsque vous attribuez un utilisateur à Google Apps, vous devez sélectionner le rôle Utilisateur ou Groupe dans la boîte de dialogue d’attribution. Le rôle « Accès par défaut » ne fonctionne pas pour l’approvisionnement.

## <a name="enable-automated-user-provisioning"></a>Activer l’approvisionnement automatique des utilisateurs

Cette section vous guide afin de connecter votre instance Azure AD à votre compte utilisateur Google Apps qui fournit l’API et configurer le service d’approvisionnement afin de créer, mettre à jour et désactiver des comptes utilisateur attribués dans Google Apps, en fonction des attributions d’utilisateur et de groupe dans Azure AD.

>[!Tip]
>Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Google Apps, en suivant les instructions disponibles dans le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="configure-automatic-user-account-provisioning"></a>Configurer l’approvisionnement automatique d’un compte utilisateur

> [!NOTE]
> Il existe une autre option pour automatiser l’approvisionnement des utilisateurs avec Google Apps : il s’agit d’utiliser [Google Apps Active Sync (GADS)](https://support.google.com/a/answer/106368?hl=en) , qui approvisionne vos identités Active Directory en local pour Google Apps. En revanche, la solution proposée dans ce didacticiel approvisionne vos utilisateurs Azure Active Directory (cloud) et vos groupes à extension messagerie pour Google Apps. 

1. Connectez-vous à la [Console d'administration de Google Apps](http://admin.google.com/) avec votre compte d'administrateur, puis cliquez sur **Sécurité**. Si le lien ne s'affiche pas, il est peut-être masqué par le menu **Autres contrôles** situé en bas de l'écran.
   
    ![Cliquez sur Sécurité.][10]

2. Sur la page **Sécurité**, cliquez sur **Référence de l’API**.
   
    ![Cliquez sur Référence d’API.][15]

3. Sélectionnez **Activer l'accès à l'API**.
   
    ![Cliquez sur Référence d’API.][16]

    > [!IMPORTANT]
    > Le nom d'utilisateur Azure Active Directory de chaque utilisateur que vous souhaitez approvisionner pour Google Apps *doit* être associé à un domaine personnalisé. Par exemple, les noms d'utilisateur qui ressemblent à bob@contoso.onmicrosoft.com ne sont pas acceptés par Google Apps, tandis que ceux ressemblant à bob@contoso.com sont acceptés. Vous pouvez modifier le domaine d’un utilisateur existant en modifiant ses propriétés dans Azure AD. Vous trouverez ci-dessous des instructions sur la définition d’un domaine personnalisé pour Azure Active Directory et Google Apps.
      
4. Si vous n’avez pas encore ajouté un nom de domaine personnalisé pour Azure Active Directory, suivez la procédure ci-dessous :
  
    a. Dans le volet de navigation gauche du [portail Azure](https://portal.azure.com), cliquez sur **Active Directory**. Dans la liste Annuaire, sélectionnez votre annuaire. 

    b. Cliquez sur **nom des domaines** dans le volet de navigation gauche, puis cliquez sur **Ajouter**.
     
     ![domaine](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_1.png)

     ![ajout de domaine](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_2.png)

    c. Saisissez votre nom de domaine dans le champ **Nom de domaine** . Ce nom de domaine doit être identique à celui que vous souhaitez utiliser pour Google Apps. Lorsque vous êtes prêt, cliquez sur le bouton **Ajouter un domaine** .
     
     ![nom de domaine](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_3.png)

    d. Cliquez sur **Suivant** pour accéder à la page de vérification. Pour vérifier que vous possédez ce domaine, vous devez modifier les enregistrements DNS du domaine en fonction des valeurs fournies dans cette page. Vous pouvez choisir d'effectuer la vérification en utilisant des **enregistrements MX** ou des **enregistrements TXT**, selon l’option de **Type d'enregistrement** sélectionné. Pour obtenir des instructions plus complètes sur la vérification de nom de domaine avec Azure AD, consultez [Ajout de votre propre domaine à Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
     ![domaine](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_4.png)

    e. Répétez la procédure précédente pour tous les domaines que vous souhaitez ajouter à votre répertoire.

5. Maintenant que vous avez vérifié tous vos domaines avec Azure AD, vous devez maintenant les vérifier à nouveau avec Google Apps. Pour chaque domaine qui n’est pas déjà inscrit auprès de Google Apps, procédez comme suit :
   
    a. Dans la [Console d'administration de Google Apps](http://admin.google.com/), cliquez sur **Domaines**.
     
     ![Cliquer sur Domaines][20]

    b. Cliquez sur **Ajouter un domaine ou un alias de domaine**.
     
     ![Ajouter un nouveau domaine][21]

    c. Sélectionnez **Ajouter un autre domaine**, puis tapez le nom du domaine que vous souhaitez ajouter.
     
     ![Entrer votre nom de domaine][22]

    d. Cliquez sur **Continuer et vérifier la propriété du domaine**. Puis suivez les étapes pour vérifier que vous possédez le nom de domaine. Pour obtenir des instructions complètes sur la vérification de votre domaine avec Google Apps, consultez. [Vérifiez que le propriétaire de votre site avec Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Répétez la procédure précédente pour tous les domaines supplémentaires que vous souhaitez ajouter à Google Apps.
     
     > [!WARNING]
     > Si vous modifiez le domaine principal pour votre client Google Apps tout en ayant déjà configuré l’authentification unique avec Azure AD, vous devez répéter l’étape 3 sous [Étape 2 : activation de l’authentification unique](#step-two-enable-single-sign-on).
       
6. Dans la [Console d'administration de Google Apps](http://admin.google.com/), cliquez sur **Rôles d'administrateur**.
   
     ![Cliquer sur Google Apps][26]

7. Déterminez le compte d’administrateur à utiliser pour gérer l’approvisionnement de l’utilisateur. Pour le **Rôle d’administrateur** de ce compte, modifiez les **Privilèges** pour ce rôle. Vérifiez qu'il possède tous les **Privilèges d'administrateur d'API** activés pour que ce compte puisse être utilisé pour l'approvisionnement.
   
     ![Cliquer sur Google Apps][27]
   
    > [!NOTE]
    > Si vous configurez un environnement de production, la meilleure pratique est de créer un compte d’administrateur dans Google Apps spécialement pour cette étape. Ce compte doit avoir un rôle d’administrateur associé qui possède les privilèges d’API nécessaires.
     
8. Dans le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

9. Si vous avez déjà configuré Google Apps pour l’authentification unique, recherchez votre instance de Google Apps à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **Google Apps** dans la galerie d’applications. Dans les résultats de la recherche, sélectionnez Google Apps, puis ajoutez-le à votre liste d’applications.

10. Sélectionnez votre instance de Google Apps, puis sélectionnez l’onglet **Approvisionnement**.

11. Définissez le **Mode d’approvisionnement** sur **Automatique**. 

     ![approvisionnement](./media/active-directory-saas-google-apps-provisioning-tutorial/provisioning.png)

12. Sous la section **informations d’identification de l’administrateur**, cliquez sur **Autoriser**. Une boîte de dialogue d’autorisation Google Apps s’ouvre dans une nouvelle fenêtre du navigateur.

13. Confirmez que vous souhaitez permettre à Azure Active Directory d’apporter des modifications à votre client Google Apps. Cliquez sur **Accepter**.
    
     ![Confirmez les autorisations.][28]

14. Dans le portail Azure, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à votre application Google Apps. Si la connexion échoue, vérifiez que votre compte Google Apps dispose des autorisations d’administrateur d’équipe et réessayez l’étape **« Autoriser »**.

15. Entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification** et cochez la case.

16. Cliquez sur **Enregistrer.**

17. Dans la section Mappages, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Google Apps.**

18. Dans la section **Mappages d’attributs**, passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD vers Google Apps. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans Google Apps pour les opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.

19. Pour activer le service d’approvisionnement Azure AD pour Google Apps, définissez **l’état de l’approvisionnement** sur **Activé** dans la section Paramètres

20. Cliquez sur **Enregistrer.**

Cette commande démarre la synchronisation initiale des utilisateurs et/ou des groupes affectés à Google Apps dans la section Utilisateurs et Groupes. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent environ toutes les 20 minutes, tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement dans votre application Google Apps.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’authentification unique](active-directory-saas-google-apps-tutorial.md)



<!--Image references-->

[10]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-auth.png
