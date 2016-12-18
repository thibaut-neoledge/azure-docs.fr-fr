---
title: 'Azure Active Directory B2C : FAQ | Microsoft Docs'
description: Forum aux questions sur Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: ed33c2ca-76d0-442a-abb1-8b7b7bb92d6a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0612949c412cf23272bf62d3c0ea424046366b36


---
# <a name="azure-active-directory-b2c-faqs"></a>Azure Active Directory B2C : FAQ
Cette page répond aux questions fréquemment posées sur Azure Active Directory (Azure AD) B2C. N'hésitez pas à la consulter pour vous tenir au courant des mises à jour.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Puis-je utiliser les fonctionnalités d’Azure AD B2C dans mon client Azure AD existant, basé sur les employés ?
Actuellement, les fonctionnalités Azure AD B2C ne peuvent pas être activées dans votre client Azure AD existant. Il est recommandé de créer un client séparé pour utiliser les fonctionnalités d’Azure AD B2C pour gérer vos clients.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Puis-je utiliser Azure AD B2C pour activer la connexion à des réseaux sociaux (Facebook et Google+) dans Office 365 ?
Azure AD B2C ne peut pas être utilisé avec Microsoft Office 365. En général, il ne peut pas offrir d’authentification aux applications SaaS (Office 365, Salesforce, Workday, etc.). Il ne permet la gestion des identités et de l’accès que pour les applications web et mobiles grand public, et n’est pas applicable aux scénarios impliquant des employés ou des partenaires.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Que sont les comptes locaux dans Azure AD B2C ? En quoi sont-ils différents des comptes professionnels ou scolaires dans Azure AD ?
Dans un client Azure AD, chaque utilisateur (à l’exception des utilisateurs avec des comptes Microsoft existants) se connecte avec une adresse e-mail sous la forme `<xyz>@<tenant domain>`, où `<tenant domain>` est l’un des domaines vérifiés dans le client ou le domaine `<...>.onmicrosoft.com` initial. Ce type de compte est un compte professionnel ou scolaire.

Dans un client Azure AD B2C, la plupart des applications veulent que l’utilisateur se connecte avec n’importe quelle adresse e-mail arbitraire (par exemple, joe@comcast.net,, bob@gmail.com,, sarah@contoso.com, ou jim@live.com).). Ce type de compte est un compte local. Aujourd’hui, nous prenons également en charge les noms d’utilisateur arbitraires (chaînes simples) en tant que comptes locaux (par exemple, joe, bob, sarah ou jim). Vous pouvez choisir l’un de ces deux types de comptes locaux dans le service Azure AD B2C.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Quels fournisseurs d’identité sociaux prenez-vous en charge maintenant ? Lesquels envisagez-vous de prendre en charge à l'avenir ?
Nous prenons actuellement en charge Facebook, Google+, LinkedIn et Amazon. Nous ajouterons la prise en charge d’autres fournisseurs d’identité sociaux populaires en fonction de la demande des clients.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Puis-je configurer des étendues pour collecter plus d’informations sur les clients depuis différents fournisseurs d’identité sociaux ?
Non, mais cette fonctionnalité est sur notre feuille de route. Les étendues par défaut utilisées pour notre jeu de fournisseurs d'identité sociaux pris en charge sont :

* Facebook : courrier électronique
* Google+ : courrier électronique
* Compte Microsoft : profil de messagerie openid
* Amazon : profil
* LinkedIn : r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Mon application doit-elle être exécutée sous Azure pour fonctionner avec Azure AD B2C ?
Non, vous pouvez héberger votre application n'importe où (dans le cloud ou sur site). Pour interagir avec Azure AD B2C, il suffit qu’elle puisse envoyer et recevoir des requêtes HTTP sur les points de terminaison accessibles publiquement.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Je dispose de plusieurs clients Azure AD B2C. Comment puis-je les gérer sur le portail Azure ?
Chaque client Azure AD B2C a son propre panneau de fonctionnalités B2C sur le portail Azure. Consultez [Azure Active Directory B2C : inscription de votre application](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) pour savoir comment accéder au panneau de fonctionnalités B2C d’un client spécifique sur le portail Azure. Le basculement d’un répertoire Azure AD B2C à l’autre sur le portail Azure ne garde pas le panneau de fonctionnalités B2C ouvert dans la plupart des navigateurs.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Comment puis-je personnaliser les courriers électroniques de vérification (le contenu et le champ « De: ») envoyés par Azure AD B2C ?
Vous pouvez utiliser la [fonctionnalité de personnalisation de la société](../active-directory/active-directory-add-company-branding.md) pour personnaliser le contenu des courriers électroniques de vérification. Plus précisément, ces deux éléments du message peuvent être personnalisés :

* **Logo de bannière**: affiché en bas à droite.
* **Couleur d’arrière-plan**: affiché en haut.
  
    ![Capture d’écran d’un message électronique de vérification personnalisée](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

La signature de courrier électronique contient le nom du client B2C que vous avez fourni lors de la création du client B2C. Vous pouvez modifier le nom à l’aide de ces instructions :

* Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/) en tant qu’administrateur d’abonnements.
* Accédez à votre client B2C.
* Cliquez sur l’onglet **Configurer** .
* Modifiez le champ **Nom** dans la section **Propriétés du répertoire**.
* Cliquez sur **Enregistrer** au bas de la page.

Il n’existe actuellement aucun moyen de modifier le champ « De : » du courrier électronique. Si cette fonctionnalité vous intéresse et que vous souhaitez personnaliser entièrement le corps du message électronique de vérification, votez pour la fonctionnalité sur [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails).

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Comment puis-je migrer mes noms d’utilisateur, mots de passe et profils existants à partir de ma base de données vers Azure AD B2C ?
Vous pouvez utiliser l’API Azure AD Graph pour écrire l’outil de migration. Pour plus d’informations, consultez [l’exemple d’API Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) . Nous fournirons diverses options de migration et des outils prêts à l'emploi ultérieurement.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Quelle stratégie de mot de passe est utilisée pour les comptes locaux dans Azure AD B2C ?
La stratégie de mot de passe Azure AD B2C pour les comptes locaux est basée sur la stratégie pour Azure AD. Les stratégies de réinitialisation du mot de passe, d’inscription ou de connexion et d’inscription Azure AD B2C utilisent des mots de passe « forts » et qui n’expirent pas. Pour plus d’informations, consultez [Stratégie de mot de passe dans Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) .

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Puis-je utiliser Azure AD Connect pour migrer les identités de clients stockées dans mon répertoire Active Directory local vers Azure AD B2C ?
Non, Azure AD Connect n'est pas conçu pour fonctionner avec Azure AD B2C. Nous fournirons diverses options de migration et des outils prêts à l'emploi ultérieurement.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C fonctionne-t-il avec les systèmes CRM, tels que Microsoft Dynamics ?
Pas actuellement. L'intégration de ces systèmes est sur notre feuille de route.

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C fonctionne-t-il avec SharePoint localement 2016 ou version antérieure ?
Pas actuellement. Azure AD B2C ne prend pas en charge les jetons SAML 1.1 dont les portails et applications de commerce électronique basés sur SharePoint localement ont besoin. Notez qu’Azure AD B2C n’est pas conçu pour le scénario de partage partenaire externe SharePoint ; consultez plutôt l’article [Learn all about the Azure AD B2B Collaboration Preview!](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) (Découvrez tout ce qu’il y a à savoir sur la version préliminaire d’Azure AD B2B Collaboration !).

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Dois-je utiliser Azure AD B2C ou B2B pour gérer les identités externes ?
Lisez cet article sur les [identités externes](../active-directory/active-directory-b2b-compare-external-identities.md) pour en savoir plus sur l’application des fonctionnalités appropriées à vos scénarios d’identités externes.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Quelles sont les fonctionnalités de création de rapports et d’audit proposées par Azure AD B2C ? Sont-elles identiques à celles d’Azure AD Premium ?
Non, Azure AD B2C ne prend pas en charge le même ensemble de rapports qu'Azure AD Premium. Azure AD B2C publiera bientôt des API de base de création de rapports et d'audit.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Puis-je localiser l'interface utilisateur des pages présentées par Azure AD B2C ? Quelles sont les langues prises en charge ?
Actuellement, Azure AD B2C est optimisé pour l'anglais uniquement. Nous prévoyons de déployer des fonctionnalités de localisation dès que possible.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Puis-je utiliser mes propres URL dans les pages d’inscription et de connexion présentées par Azure AD B2C ? Par exemple, puis-je remplacer l’URL login.microsoftonline.com par login.contoso.com ?
Pas actuellement. Cette fonctionnalité est sur notre feuille de route. Notez également que la vérification de votre domaine dans l’onglet **Domaines** de votre client sur le portail Azure Classic ne permet pas de le faire.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Comment supprimer mon client Azure AD B2C ?
Procédez comme suit pour supprimer votre client Azure AD B2C :

* Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
* Accédez aux panneaux **Applications**, **Fournisseurs d’identité** et **Toutes les stratégies**, puis supprimez toutes les entrées dans chacun d’eux.
* À présent, connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/) en tant qu’administrateur d’abonnements. (Il s’agit du compte professionnel ou scolaire, ou du compte Microsoft que vous avez utilisé pour vous inscrire à Azure.)
* Accédez à l’extension Active Directory sur la gauche, puis cliquez sur votre client B2C.
* Cliquez sur l’onglet **Users** .
* Sélectionnez chaque utilisateur tour à tour (excluez l’utilisateur que vous utilisez actuellement pour la connexion, par exemple, l’administrateur d’abonnement). Cliquez sur **Supprimer** en bas de la page et sur **OUI** lorsque vous y êtes invité.
* Cliquez sur l’onglet **Applications** .
* Sélectionnez **Applications que ma société possède** dans la liste déroulante **Afficher** et cochez la case.
* Une application appelée **b2c-extensions-app** apparaît dans la liste. Cliquez sur **Supprimer** en bas de la page et sur **OUI** lorsque vous y êtes invité.
* Accédez à nouveau à l’extension Active Directory et sélectionnez votre client B2C.
* Cliquez sur **Supprimer** en bas de la page. Suivez les instructions à l’écran pour terminer le processus.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Puis-je obtenir Azure AD B2C dans le cadre d’Enterprise Mobility Suite ?
Non, Azure AD B2C est un service Azure avec paiement à l’utilisation et ne fait pas partie d’Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Comment signaler des problèmes avec Azure AD B2C ?
Consultez [Azure Active Directory B2C : dépôt de demandes de support](active-directory-b2c-support.md).

## <a name="more-information"></a>Plus d’informations
Vous pouvez également consulter les [limites, restrictions et contraintes de service actuelles](active-directory-b2c-limitations.md).




<!--HONumber=Nov16_HO3-->


