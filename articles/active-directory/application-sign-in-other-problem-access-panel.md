---
title: "Problèmes de connexion à une application à partir du volet d’accès | Microsoft Docs"
description: "Comment dépanner les problèmes d’accès à une application à partir du volet d’accès Microsoft Azure AD sur myapps.microsoft.com"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: b89569b818b0e2c79934a842fdc428011674c000
ms.contentlocale: fr-fr
ms.lasthandoff: 04/17/2017

---

<a id="problems-signing-in-to-an-application-from-the-access-panel" class="xliff"></a>

# Problèmes de connexion à une application à partir du volet d’accès

Le volet d’accès est un portail web qui permet à un utilisateur disposant d’un compte professionnel ou scolaire dans Azure Active Directory (Azure AD) d’afficher et de démarrer des applications basées sur le cloud auxquelles l’administrateur Azure AD lui a accordé un accès. 

Ces applications sont configurées pour le compte de l’utilisateur dans le portail Azure AD. Pour que l’application soit visible dans le volet d’accès, elle doit être correctement configurée et affectée à l’utilisateur ou à un groupe dont est membre l’utilisateur.

Les types d’applications que peut voir l’utilisateur tombent dans les catégories suivantes :

-   Applications Office 365

-   Applications Microsoft et tierces configurées avec l’authentification unique (SSO) basée sur la fédération

-   Applications à authentification unique (SSO) par mot de passe

-   Applications avec solutions d’authentification unique (SSO) existantes

<a id="general-issues-to-check-first" class="xliff"></a>

## Problèmes d’ordre général à vérifier en premier

-   Vérifiez que votre **navigateur** répond à la configuration minimale requise pour le volet d’accès.

-   Vérifiez que l’URL de l’application figure dans la liste des **sites de confiance** du navigateur de l’utilisateur.

-   Vérifiez que l’application est **configurée** correctement.

-   Vérifiez que les connexions sont **activées** dans le compte de l’utilisateur.

-   Vérifiez que le compte d’utilisateur **n’est pas verrouillé**.

-   Vérifiez que le **mot de passe de l’utilisateur n’a pas expiré ou qu’il n’a pas été oublié**.

-   Vérifiez que **Multi-Factor Authentication** ne bloque pas l’accès utilisateur.

-   Vérifiez qu’une **stratégie d’accès conditionnel** ou qu’une **stratégie de protection d’identité** ne bloque pas l’accès utilisateur.

-   Vérifiez que les **informations de contact d’authentification** de l’utilisateur sont à jour et permettent d’appliquer les stratégies d’accès conditionnel ou de Multi-Factor Authentication.

-   Effacez les cookies de votre navigateur, puis réessayez de vous connecter.

<a id="meeting-browser-requirements-for-the-access-panel" class="xliff"></a>

## Configuration requise du navigateur pour le volet d’accès

Le volet d’accès nécessite un navigateur qui prend en charge JavaScript et dans lequel le CSS est activé. Pour utiliser l’authentification unique par mot de passe dans le volet d’accès, l’extension du volet d’accès doit être installée dans le navigateur de l’utilisateur. Cette extension est téléchargée automatiquement lorsqu’un utilisateur sélectionne une application configurée pour l’authentification unique (SSO) avec mot de passe.

Pour l’authentification unique par mot de passe, les navigateurs de l’utilisateur final peuvent être :

-   Internet Explorer 8, 9, 10, 11 -- sur Windows 7 ou version ultérieure

-   Chrome -- sur Windows 7 ou ultérieur, et sur Mac OS X ou ultérieur

-   Firefox 26.0 ou ultérieur -- sur Windows XP SP2 ou ultérieur, et sur Mac OS X 10.6 ou ultérieur

**Remarque** : L’extension de l’authentification unique par mot de passe sera disponible pour Edge dans Windows 10 quand les extensions de navigateur seront prises en charge pour Edge.

<a id="how-to-install-the-access-panel-browser-extension" class="xliff"></a>

## Comment installer l’extension de navigateur du volet d’accès

Pour installer l’extension de navigateur du volet d’accès, effectuez les étapes suivantes :

1.  Ouvrez le [volet d’accès](https://myapps.microsoft.com) dans l’un des navigateurs pris en charge et connectez-vous en tant qu’**utilisateur** dans Azure AD.

2.  Cliquez sur une **application avec authentification unique par mot de passe** dans le volet d’accès.

3.  Dans l’invite vous demandant d’installer le logiciel, sélectionnez **Installer maintenant**.

4.  En fonction de votre navigateur, vous allez être redirigé vers le lien de téléchargement. **Ajoutez** l’extension à votre navigateur.

5.  Si votre navigateur vous le demande, sélectionnez l’option **Activer** ou **Autoriser** pour l’extension.

6.  Une fois installée, **redémarrez** votre session de navigateur.

7.  Connectez-vous au volet d’accès et essayez de **lancer** vos applications à authentification unique par mot de passe.

Vous pouvez également télécharger l’extension pour Chrome et Firefox à partir des liens directs ci-dessous :

-   [Extension du volet d’accès pour Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extension du volet d’accès pour Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

<a id="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application" class="xliff"></a>

## Comment configurer l’authentification unique fédérée pour une application de la galerie Azure AD

Un didacticiel pas à pas est disponible pour toutes les applications de la galerie Azure AD dans lesquelles est activée la fonctionnalité Enterprise Single Sign-On. Pour une aide pas à pas détaillée, vous pouvez accéder à la [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).

Pour configurer une application à partir de la galerie Azure AD, vous devez effectuer les opérations suivantes :

-   [Ajouter une application à partir de la galerie Azure AD](#add-an-application)

-   [Configurer les valeurs de métadonnées de l’application dans Azure AD (URL de connexion, identificateur, URL de réponse)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Sélectionner l’identificateur de l’utilisateur et ajouter les attributs d’utilisateur à envoyer à l’application](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Récupérer le certificat et les métadonnées Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configurer les valeurs de métadonnées Azure AD dans l’application (URL de connexion, émetteur, URL de déconnexion et certificat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Affecter des utilisateurs à l’application](#assign-users-to-the-application)

<a id="add-an-application-from-the-azure-ad-gallery" class="xliff"></a>

### Ajouter une application à partir de la galerie Azure AD

Pour ajouter une application à partir de la galerie Azure AD, effectuez les étapes suivantes :

1.  Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous en tant qu’**administrateur général** ou **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Autres services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur le bouton **Ajouter** dans le coin supérieur droit du panneau **Applications d’entreprise**.

6.  Dans la zone de texte **Entrer un nom** de la section **Ajouter à partir de la galerie**, tapez le nom de l’application.

7.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

8.  Avant d’ajouter l’application, vous pouvez la renommer dans la zone de texte **Nom**.

9.  Pour ajouter l’application, cliquez sur le bouton **Ajouter**.

Après une courte période, vous pourrez voir le panneau de configuration de l’application.

<a id="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery" class="xliff"></a>

### Configurer l’authentification unique pour une application à partir de la galerie Azure AD

Pour configurer l’authentification unique pour une application, effectuez les étapes suivantes :

1.  <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général** ou **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Autres services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste de toutes vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Sélectionnez **Authentification basée sur SAML** dans la liste déroulante **Mode**.

9.  Entrez les valeurs obligatoires dans **Domaine et URL**. Ces valeurs doivent vous être communiquées par le fournisseur de l’application.

   1. Pour configurer l’application en tant qu’application à authentification unique lancée par le fournisseur de services, une URL de connexion est obligatoire. Pour certaines applications, l’identificateur est également une valeur obligatoire.

   2. Pour configurer l’application en tant qu’application à authentification unique lancée par le fournisseur d’identité, une URL de réponse est obligatoire. Pour certaines applications, l’identificateur est également une valeur obligatoire.

10. **Facultatif** : Pour afficher les valeurs non obligatoires, cliquez sur **Afficher les paramètres d’URL avancés**.

11. Dans **Attributs d’utilisateur**, sélectionnez l’identificateur unique de vos utilisateurs dans la liste déroulante **Identificateur de l’utilisateur**.

12. **Facultatif** : Pour modifier les attributs à envoyer à l’application dans le jeton SAML lorsque l’utilisateur se connecte, cliquez sur **Afficher et modifier tous les autres attributs utilisateur**.

   Pour ajouter un attribut :

   1. Cliquez sur **Ajouter un attribut**. Entrez le **Nom**, puis sélectionnez la **Valeur** dans la liste déroulante.

   2. Cliquez sur **Enregistrer**. Le nouvel attribut s’affiche dans le tableau.

13. Cliquez sur **Configurer &lt;nom de l’application&gt;** pour accéder à la documentation sur la façon de configurer l’authentification unique. En outre, vous disposez des URL de métadonnées et du certificat nécessaires à la configuration de l’authentification unique.

14. Pour enregistrer la configuration, cliquez sur **Enregistrer**.

15. Affectez des utilisateurs à l’application.

<a id="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application" class="xliff"></a>

### Sélectionner l’identificateur de l’utilisateur et ajouter les attributs d’utilisateur à envoyer à l’application

Pour sélectionner l’identificateur de l’utilisateur ou ajouter des attributs d’utilisateur, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant qu’**administrateur général** ou **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Autres services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste de toutes vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6.  Sélectionnez l’application pour laquelle vous avez configuré l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Dans la section **Attributs d’utilisateur**, sélectionnez l’identificateur unique de vos utilisateurs dans la liste déroulante **Identificateur de l’utilisateur**. L’option sélectionnée doit correspondre à la valeur attendue dans l’application pour authentifier l’utilisateur.

    >[!NOTE]
    >Azure AD sélectionne le format de l’attribut NameID (Identificateur d’utilisateur) en fonction de la valeur sélectionnée ou du format demandé par l’application dans la demande d’authentification SAML. Pour plus d’informations, consultez l’article [Protocole SAML d’authentification unique](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) dans la section NameIDPolicy.
    >
    >

9.  Pour ajouter des attributs d’utilisateur, cliquez sur **Afficher et modifier tous les autres attributs utilisateur** afin de modifier les attributs à envoyer à l’application dans le jeton SAML lorsque l’utilisateur se connecte.

   Pour ajouter un attribut :

   1. Cliquez sur **Ajouter un attribut**. Entrez le **Nom**, puis sélectionnez la **Valeur** dans la liste déroulante.

   2. Cliquez sur **Enregistrer**. Le nouvel attribut s’affiche dans le tableau.

<a id="download-the-azure-ad-metadata-or-certificate" class="xliff"></a>

### Télécharger les métadonnées ou le certificat Azure AD

Pour télécharger les métadonnées ou le certificat de l’application à partir d’Azure AD, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant qu’**administrateur général** ou **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Autres services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste de toutes vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6.  Sélectionnez l’application pour laquelle vous avez configuré l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Accédez à la section **Certificat de signature SAML**, puis cliquez sur la valeur de colonne **Télécharger**. En fonction de ce que l’application nécessite pour configurer l’authentification unique, vous voyez soit l’option de téléchargement des métadonnées XML, soit le certificat.

    Azure AD ne fournit pas une URL permettant d’obtenir les métadonnées. Les métadonnées peuvent uniquement être récupérées sous forme de fichier XML.

<a id="how-to-configure-federated-single-sign-on-for-a-non-gallery-application" class="xliff"></a>

## Comment configurer l’authentification unique fédérée pour une application non issue de la galerie

Pour configurer une application non issue de la galerie, vous devez disposer d’Azure Active Directory Premium et votre application doit prendre en charge SAML 2.0. Pour plus d’informations sur les versions d’Azure AD, consultez [Tarification de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Configurer les valeurs de métadonnées de l’application dans Azure AD (URL de connexion, identificateur, URL de réponse)](#configuring-single-sign-on)

-   [Sélectionner l’identificateur de l’utilisateur et ajouter les attributs d’utilisateur à envoyer à l’application](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Récupérer le certificat et les métadonnées Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configurer les valeurs de métadonnées Azure AD dans l’application (URL de connexion, émetteur, URL de déconnexion et certificat)](#configuring-single-sign-on)

<a id="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url" class="xliff"></a>

### Configurer les valeurs de métadonnées de l’application dans Azure AD (URL de connexion, identificateur, URL de réponse)

Pour configurer l’authentification unique pour une application qui n’est pas issue de la galerie Azure AD, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant qu’**administrateur général** ou **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Autres services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur le bouton **Ajouter** dans le coin supérieur droit du panneau **Applications d’entreprise**.

6.  Cliquez sur **Application ne figurant pas dans la galerie** dans la section **Ajouter votre propre application**.

7.  Entrez le nom de votre application dans la zone de texte **Nom**.

8.  Pour ajouter l’application, cliquez sur le bouton **Ajouter**.

9.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

10. Sélectionnez **Authentification basée sur SAML** dans la liste déroulante **Mode**.

11. Entrez les valeurs obligatoires dans **Domaine et URL**. Ces valeurs doivent vous être communiquées par le fournisseur de l’application.

  1. Pour configurer l’application en tant qu’application à authentification unique lancée par le fournisseur d’identité, entrez l’URL de réponse et l’identificateur.

  2. **Facultatif** : Pour configurer l’application en tant qu’application à authentification unique lancée par le fournisseur de services, une URL de connexion est obligatoire.

12. Dans **Attributs d’utilisateur**, sélectionnez l’identificateur unique de vos utilisateurs dans la liste déroulante **Identificateur de l’utilisateur**.

13. **Facultatif** : Pour modifier les attributs à envoyer à l’application dans le jeton SAML lorsque l’utilisateur se connecte, cliquez sur **Afficher et modifier tous les autres attributs utilisateur**.

   Pour ajouter un attribut :

   1. Cliquez sur **Ajouter un attribut**. Entrez le **Nom**, puis sélectionnez la **Valeur** dans la liste déroulante.

   2. Cliquez sur **Enregistrer**. Le nouvel attribut s’affiche dans le tableau.

14. Cliquez sur **Configurer &lt;nom de l’application&gt;** pour accéder à la documentation sur la façon de configurer l’authentification unique. En outre, vous disposez des URL et du certificat Azure AD nécessaires pour l’application.

<a id="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application" class="xliff"></a>

### Sélectionner l’identificateur de l’utilisateur et ajouter les attributs d’utilisateur à envoyer à l’application

Pour sélectionner l’identificateur de l’utilisateur ou ajouter des attributs d’utilisateur, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant qu’**administrateur général** ou **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Autres services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste de toutes vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6.  Sélectionnez l’application pour laquelle vous avez configuré l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Dans la section **Attributs d’utilisateur**, sélectionnez l’identificateur unique de vos utilisateurs dans la liste déroulante **Identificateur de l’utilisateur**. L’option sélectionnée doit correspondre à la valeur attendue dans l’application pour authentifier l’utilisateur.

   >[!NOTE]
   >Azure AD sélectionne le format de l’attribut NameID (Identificateur d’utilisateur) en fonction de la valeur sélectionnée ou du format demandé par l’application dans la demande d’authentification SAML. Pour plus d’informations, consultez l’article [Protocole SAML d’authentification unique](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) dans la section NameIDPolicy.
   >
   >

9.  Pour ajouter des attributs d’utilisateur, cliquez sur **Afficher et modifier tous les autres attributs utilisateur** afin de modifier les attributs à envoyer à l’application dans le jeton SAML lorsque l’utilisateur se connecte.

   Pour ajouter un attribut :

   1. Cliquez sur **Ajouter un attribut**. Entrez le **Nom**, puis sélectionnez la **Valeur** dans la liste déroulante.

   2. Cliquez sur **Enregistrer**. Le nouvel attribut s’affiche dans le tableau.

<a id="download-the-azure-ad-metadata-or-certificate" class="xliff"></a>

### Télécharger les métadonnées ou le certificat Azure AD

Pour télécharger les métadonnées ou le certificat de l’application à partir d’Azure AD, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant qu’**administrateur général** ou **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Autres services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste de toutes vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6.  Sélectionnez l’application pour laquelle vous avez configuré l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Accédez à la section **Certificat de signature SAML**, puis cliquez sur la valeur de colonne **Télécharger**. En fonction de ce que l’application nécessite pour configurer l’authentification unique, vous voyez soit l’option de téléchargement des métadonnées XML, soit le certificat.

    Azure AD ne fournit pas une URL permettant d’obtenir les métadonnées. Les métadonnées peuvent uniquement être récupérées sous forme de fichier XML.

<a id="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application" class="xliff"></a>

## Comment configurer l’authentification unique par mot de passe pour une application de la galerie Azure AD

Pour configurer une application à partir de la galerie Azure AD, vous devez effectuer les opérations suivantes :

-   [Ajouter une application à partir de la galerie Azure AD](#add-an-application)

-   [Configurer l’application pour l’authentification unique par mot de passe](#configure-the-application)

<a id="add-an-application-from-the-azure-ad-gallery" class="xliff"></a>

### Ajouter une application à partir de la galerie Azure AD

Pour ajouter une application à partir de la galerie Azure AD, effectuez les étapes suivantes :

1.  Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous en tant qu’**administrateur général** ou **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Autres services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur le bouton **Ajouter** dans le coin supérieur droit du panneau **Applications d’entreprise**.

6.  Dans la zone de texte **Entrer un nom** de la section **Ajouter à partir de la galerie**, tapez le nom de l’application.

7.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

8.  Avant d’ajouter l’application, vous pouvez la renommer dans la zone de texte **Nom**.

9.  Pour ajouter l’application, cliquez sur le bouton **Ajouter**.

Après une courte période, vous pourrez voir le panneau de configuration de l’application.

<a id="configure-the-application-for-password-single-sign-on" class="xliff"></a>

### Configurer l’application pour l’authentification unique par mot de passe

Pour configurer l’authentification unique pour une application, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant qu’**administrateur général** ou **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Autres services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste de toutes vos applications.

 * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Sélectionnez le mode **Authentification par mot de passe**.

9.  Affectez des utilisateurs à l’application.

10. En outre, vous pouvez également fournir des informations d’identification pour le compte de l’utilisateur en sélectionnant les lignes des utilisateurs, en cliquant sur **Mettre à jour les informations d’identification** et en entrant le nom d’utilisateur et le mot de passe à la place des utilisateurs. Autrement, les utilisateurs devront entrer les informations d’identification eux-mêmes lors du lancement.

<a id="how-to-configure-password-single-sign-on-for-a-non-gallery-application" class="xliff"></a>

## Comment configurer l’authentification unique par mot de passe pour une application ne figurant pas dans la galerie

Pour configurer une application à partir de la galerie Azure AD, vous devez effectuer les opérations suivantes :

-   [Ajouter une application ne figurant pas dans la galerie](#add-a-non-gallery-application)

-   [Configurer l’application pour l’authentification unique par mot de passe](#configure-the-application-for-password-single-sign-on)

<a id="add-a-non-gallery-application" class="xliff"></a>

### Ajouter une application ne figurant pas dans la galerie

Pour ajouter une application à partir de la galerie Azure AD, effectuez les étapes suivantes :

1.  Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous en tant qu’**administrateur général** ou **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Autres services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur le bouton **Ajouter** dans le coin supérieur droit du panneau **Applications d’entreprise**.

6.  Cliquez sur **Application ne figurant pas dans la galerie**.

7.  Entrez le nom de votre application dans la zone de texte **Nom**. Sélectionnez **Ajouter**.

Après une courte période, vous pourrez voir le panneau de configuration de l’application.

<a id="configure-the-application-for-password-single-sign-on" class="xliff"></a>

### Configurer l’application pour l’authentification unique par mot de passe

Pour configurer l’authentification unique pour une application, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant qu’**administrateur général** ou **coadministrateur**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Autres services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste de toutes vos applications.

 * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8.  Sélectionnez le mode **Authentification par mot de passe**.

9.  Entrez **l’URL de connexion**. Il s’agit de l’URL où les utilisateurs entrent leurs nom d’utilisateur et mot de passe pour se connecter. Vérifiez que les champs de connexion sont visibles dans l’URL.

10. Affectez des utilisateurs à l’application.

11. En outre, vous pouvez également fournir des informations d’identification pour le compte de l’utilisateur en sélectionnant les lignes des utilisateurs, en cliquant sur **Mettre à jour les informations d’identification** et en entrant le nom d’utilisateur et le mot de passe à la place des utilisateurs. Autrement, les utilisateurs devront entrer les informations d’identification eux-mêmes lors du lancement.

<a id="how-to-assign-a-user-to-an-application-directly" class="xliff"></a>

## Comment affecter un utilisateur directement à une application

Pour affecter un ou plusieurs utilisateurs directement à une application, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant qu’**administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Autres services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste de toutes vos applications.

  * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6.  Dans la liste qui s’affiche, sélectionnez l’application à laquelle vous souhaitez assigner un utilisateur.

7.  Une fois l’application chargée, cliquez sur **Utilisateurs et groupes** dans le menu de navigation de gauche de l’application.

8.  Cliquez sur le bouton **Ajouter** en haut de la liste **Utilisateurs et groupes** pour ouvrir le panneau **Ajouter une attribution**.

9.  Cliquez sur le sélecteur **Utilisateurs et groupes** à partir du panneau **Ajouter une attribution**.

10. Tapez **le nom complet** ou **l’adresse de messagerie** de l’utilisateur souhaité pour l’attribution dans la zone de recherche **Rechercher par nom ou adresse de messagerie**.

11. Pointez sur **l’utilisateur** dans la liste pour afficher une **case à cocher**. Cliquez sur la case à cocher en regard de la photo de profil ou du logo de l’utilisateur pour ajouter ce dernier à la liste **Sélectionné**.

12. **Facultatif :** Si vous souhaitez **ajouter plusieurs utilisateurs**, entrez un autre **nom complet** ou une autre **adresse de messagerie** dans la zone de recherche **Rechercher par nom ou adresse de messagerie**, puis cliquez sur la case à cocher pour ajouter cet utilisateur à la liste **Sélectionné**.

13. Lorsque vous avez fini de sélectionner les utilisateurs, cliquez sur le bouton **Sélectionner** pour les ajouter à la liste des utilisateurs et des groupes à affecter à l’application.

14. **Facultatif :** Cliquez sur le sélecteur **Sélectionner un rôle** dans le panneau **Ajouter une attribution** pour sélectionner un rôle à affecter aux utilisateurs que vous avez sélectionnés.

15. Cliquez sur le bouton **Attribuer** pour affecter l’application aux utilisateurs sélectionnés.

Après une courte période, les utilisateurs que vous avez sélectionnés sont en mesure de lancer ces applications dans le volet d’accès.

<a id="if-these-troubleshooting-steps-do-not-the-resolve-the-issue" class="xliff"></a>

## Si ces étapes de dépannage ne résolvent pas le problème

Créez un ticket de support en fournissant les informations suivantes, si disponibles :

-   ID d’erreur de corrélation

-   Nom d’utilisateur principal (adresse de messagerie de l’utilisateur)

-   ID de locataire

-   Type de navigateur

-   Fuseau horaire et heure/période à laquelle l’erreur s’est produite

-   Traces Fiddler

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
[Fournir une authentification unique à vos applications avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md)


