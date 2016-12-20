---
title: "Sécurisation des ressources de cloud avec Azure MFA et AD FS"
description: "Voici la page d&quot;authentification multifacteur Azure qui explique la prise en main de l&quot;authentification multifacteur Azure et d’AD FS 2.0 dans le cloud."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 0927fc67-8090-4fdd-913a-b3cfed3fbe77
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 0a9ab0aca1a77245f360d0d8976aa9b8f59f15a0


---
# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Sécurisation des ressources de cloud avec le serveur Azure Multi-Factor Authentication et AD FS
Si votre organisation est fédérée avec Azure Active Directory AD, utilisez l’authentification multifacteur Azure ou les services de fédération d’Active Directory pour sécuriser les ressources auxquelles Azure AD accède. Utilisez les procédures suivantes pour sécuriser les ressources Azure Active Directory avec l’authentification multifacteur Azure ou les services de fédération d’Active Directory.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Sécurisation des ressources Azure AD à l’aide d’AD FS
Pour sécuriser vos ressources de cloud, activez tout d’abord un compte pour les utilisateurs, puis définissez une règle de revendication. Suivez cette procédure pour les différentes étapes :

1. Utilisez la procédure décrite dans [Activez l’authentification multifacteur pour vos utilisateurs](multi-factor-authentication-get-started-cloud.md#turn-on-two-step-verification-for-users) pour activer un compte.
2. Démarrez la console de gestion AD FS.
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)
3. Accédez aux **Approbations de partie de confiance** et cliquez avec le bouton droit de la souris sur Approbation de partie de confiance. Sélectionnez **Modifier les règles de revendication...**
4. Cliquez sur **Ajouter une règle...**
5. Dans la liste déroulante, sélectionnez **Envoyer les revendications en utilisant une règle personnalisée**, puis cliquez sur **Suivant**.
6. Donnez un nom à cette règle.
7. Sous Règle personnalisée : ajoutez le texte suivant :

    ```
    => issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");
    ```

    Revendication correspondante :

    ```
    <saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
    <saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
    </saml:Attribute>
    ```
8. Cliquez sur **OK** puis **Terminer**. Fermez la console de gestion AD FS.

Les utilisateurs peuvent ensuite procéder à la connexion à l'aide de la méthode locale (par exemple, carte à puce).

## <a name="trusted-ips-for-federated-users"></a>Adresses IP de confiance pour les utilisateurs fédérés
Les adresses IP approuvées permettent aux administrateurs de contourner la vérification en deux étapes pour des adresses IP spécifiques ou pour les utilisateurs fédérés qui ont des requêtes provenant de leur propre intranet. Les sections suivantes décrivent comment configurer des adresses IP approuvées Azure Multi-Factor Authentication avec des utilisateurs fédérés et comment contourner la vérification en deux étapes, lorsqu’une requête provient d’un intranet d’utilisateurs fédérés. Pour cela, vous devez configurer AD FS pour utiliser un passthrough ou filtrer un modèle de revendication entrante avec le type de revendication Dans le périmètre du réseau d’entreprise.

Cet exemple utilise Office 365 pour nos approbations de la partie de confiance.

### <a name="configure-the-ad-fs-claims-rules"></a>Configuration des règles de revendications AD FS
La première chose à faire consiste à configurer les revendications AD FS. Nous allons créer deux règles de revendications : une pour le type de revendication Dans le périmètre du réseau d’entreprise et l’autre pour maintenir les utilisateurs connectés.

1. Ouvrez Gestion AD FS.
2. Sur la gauche, sélectionnez **Approbations de partie de confiance**.
3. Cliquez avec le bouton droit sur la **Plateforme d’identité Microsoft Office 365** et sélectionnez **Modifier les règles de revendication…**
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. Sous Règles de transformation d’émission, cliquez sur **Ajouter une règle.**
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. Dans l’Assistant Ajout de règle de revendication de transformation, sélectionnez **Passer ou filtrer une revendication entrante** dans la liste déroulante et cliquez sur **Suivant**.
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. Dans la zone en regard du nom de la règle de revendication, nommez votre règle. Par exemple : InsideCorpNet.
7. Dans la liste déroulante, en regard du type de revendication entrante, sélectionnez **Dans le périmètre du réseau d’entreprise**.
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Cliquez sur **Terminer**.
9. Sous Règles de transformation d’émission, cliquez sur **Ajouter une règle**.
10. Dans l’Assistant Ajout de règle de revendication de transformation, sélectionnez **Envoyer les revendications en utilisant une règle personnalisée** dans la liste déroulante et cliquez sur **Suivant**.
11. Dans la zone sous Nom de la règle de revendication : entrez *Keep Users Signed In (Maintenir les utilisateurs connectés)*.
12. Dans la zone Règle personnalisée, entrez :

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Cliquez sur **Terminer**.
14. Cliquez sur **Apply**.
15. Cliquez sur **OK**.
16. Fermez Gestion AD FS.

### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Configuration d'adresses IP de confiance Azure Multi-Factor Authentication avec des utilisateurs fédérés
Maintenant que les revendications sont en place, nous pouvons configurer des adresses IP approuvées.

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Cliquez à gauche sur **Active Directory**.
3. Sous Annuaire, sélectionnez l’annuaire dans lequel vous souhaitez configurer les adresses IP approuvées.
4. Dans l’annuaire que vous avez sélectionné, cliquez sur **Configurer**.
5. Dans la section Authentification multifacteur, cliquez sur **Gérer les paramètres du service**.
6. Dans la page Paramètres du service, sous Adresses IP approuvées, sélectionnez **Ignorer l’authentification multifacteur pour les demandes issues d’utilisateurs fédérés provenant de mon intranet.**
   ![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Cliquez sur **save**.
8. Une fois les mises à jour appliquées, cliquez sur **Fermer**.

Et voilà ! À ce stade, les utilisateurs fédérés d'Office 365 doivent pouvoir utiliser uniquement MFA lorsqu'une revendication provient de l'extérieur de l'intranet de l'entreprise.



<!--HONumber=Dec16_HO2-->


