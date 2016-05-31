<properties
	pageTitle="Forum Aux Questions sur Azure Active Directory | Microsoft Azure"
	description="FAQ Azure Active Directory qui fournit des réponses aux questions conjointement avec l’accès à Azure et Azure Active Directory, la gestion des mots de passe et l’accès aux applications."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/20/2016"
	ms.author="markusvi"/>

# Forum Aux Questions sur Azure Active Directory

Azure Active Directory est une solution IDaaS (Identity as a Service) complète qui couvre tous les aspects de l’identité, la gestion des accès et la sécurité.


Pour plus d’informations, consultez [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)



## Accès à Azure et Azure Active Directory


**Q : Pourquoi le message « Aucun abonnement trouvé » s’affiche-t-il lorsque j’essaie d’accéder à Azure AD dans le portail Azure Classic (https://manage.windowsazure.com)?**

**R :** pour accéder au portail Azure Classic, chaque utilisateur doit disposer d’autorisations sur un abonnement Azure. Si vous détenez une licence Office 365 ou Azure AD payante, accédez à [http://aka.ms/accessAAD](http://aka.ms/accessAAD) pour une étape d’activation unique, sinon vous devrez activer une [version d’essai Azure](https://azure.microsoft.com/pricing/free-trial/) complète ou un abonnement payant.

Pour plus d'informations, consultez la page suivante :

- [Association des abonnements Azure avec Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Gestion du répertoire de l’abonnement Office 365 dans Azure](active-directory-manage-o365-subscription.md)

---

**Q : Quelle est la relation entre Azure AD, Office 365 et Azure ?**

**R :** Azure Active Directory vous fournit une identité et des fonctionnalités d’accès communes à tous les services Microsoft Online Services. Que vous utilisiez Office 365, Microsoft Azure, Intune ou d’autres outils, vous utilisez déjà Azure AD pour activer l’authentification et la gestion des accès pour tous ces services.

En fait, tous les utilisateurs que vous avez activés pour les services Microsoft Online Services sont définis en tant que comptes d’utilisateurs dans une ou plusieurs instances d’Azure AD. Vous pouvez activer ces comptes pour des fonctionnalités Azure AD gratuites, telles que l’accès aux applications de cloud.
 
En outre, les services Azure AD payants (par exemple : Azure AD de base, Premium, EMS, etc.) complètent d’autres services en ligne tels qu’Office 365 et Microsoft Azure, avec des solutions de gestion et de sécurité à l’échelle de l’entreprise.


---



## Prise en main d’Azure AD hybride


**Q : Comment puis-je connecter mon annuaire local à Azure AD ?**

**R :** Vous pouvez connecter votre annuaire local à Azure AD à l’aide d’**Azure AD Connect**.

Pour plus d’informations, consultez [Intégration des identités locales avec Azure Active Directory](active-directory-aadconnect.md).


---

**Q : Comment configurer l’authentification unique entre mon annuaire local et mes applications cloud ?**

**R :** Vous devez uniquement configurer l’authentification unique entre votre annuaire local et Azure AD. Tant que vous accédez à vos applications cloud via Azure AD, le service conduit automatiquement vos utilisateurs à s’authentifier correctement avec leurs informations d’identification locales.

L’implémentation du SSO à partir de l’emplacement local peut être facilement mise en œuvre à l’aide de solutions de fédération telles qu’ADFS ou en configurant la synchronisation du hachage de mot de passe. Vous pouvez facilement déployer les deux options à l’aide de l’Assistant de configuration Azure AD Connect.
  

Pour plus d’informations, consultez [Intégration des identités locales avec Azure Active Directory](active-directory-aadconnect.md)
  

---

**Q : Azure Active Directory fournit-il un portail libre-service aux utilisateurs de mon organisation ?**

**R :** Oui, Azure Active Directory fournit le [Panneau d’accès Azure AD](http://myapps.microsoft.com) pour l’accès aux applications en libre-service. Si vous êtes un client Office 365, vous trouverez la plupart des mêmes fonctionnalités dans le portail Office 365.

Pour plus d’informations, consultez la [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).



---

**Q : Azure AD m’aide-t-il à gérer mon infrastructure locale ?**

**R :** Oui, c’est le cas. L’édition Azure AD Premium vous propose **Connect Health**. Azure AD Connect Health vous permet de surveiller et d’analyser votre infrastructure d’identité locale et les services de synchronisation.

Pour en savoir plus, consultez [Surveillez votre infrastructure d’identité locale et vos services de synchronisation dans le cloud](active-directory-aadconnect-health.md).

---

## Gestion des mots de passe

**Q : Puis-je utiliser l’écriture différée de mot de passe Azure AD sans synchronisation de mot de passe ? (c’est-à-dire : j’aimerais utiliser la réinitialisation de mot de passe libre-service Azure AD avec l’écriture différée de mot de passe, mais je ne veux pas que mes mots de passe soient stockés dans le cloud)**

**R :** vous n’avez pas besoin de synchroniser vos mots de passe AD sur Azure AD afin d’activer l’écriture différée. Dans un environnement fédéré,l’authentification unique Azure AD repose sur l’annuaire local pour authentifier l’utilisateur. Ce scénario ne nécessite pas le suivi du mot de passe local dans Azure AD.

---

**Q : Combien de temps faut-il pour qu’un mot de passe soit écrit de manière différée sur AD en local ?**

**R :** L’écriture différée de mot de passe fonctionne en temps réel.

Pour en savoir plus, voir [Prise en main de la gestion de mot de passe](active-directory-passwords-getting-started.md).


---

**Q : Puis-je utiliser l’écriture différée de mot de passe avec des mots de passe gérés par un administrateur ?**

**R :** Oui, si cette fonction est activée, les opérations de mot de passe effectuées par un administrateur sont écrites de manière différée dans votre environnement local.

Pour voir d’autres questions relatives aux mots de passe, voir [Forum aux questions - Gestion des mots de passe](active-directory-passwords-faq.md).

---

## Accès aux applications


**Q : Où puis-je trouver une liste des applications qui sont déjà intégrées à Azure AD et leurs fonctionnalités ?**

**R :** Azure AD dispose de plus de 2600 applications pré-intégrées issues de Microsoft, de fournisseurs de services d’application ou de partenaires. Toutes les applications pré-intégrées prennent en charge l’authentification unique. L’authentification unique vous permet d’utiliser vos informations d’identification professionnelles pour accéder à vos applications. Certaines applications prennent également en charge l’approvisionnement et l’annulation d’approvisionnement automatisés

Pour obtenir une liste complète des applications déjà intégrées, voir [Marketplace Active Directory](https://azure.microsoft.com/marketplace/active-directory/).


---

**Q : Que se passe-t-il si l’application dont j’ai besoin ne figure pas dans le Marketplace Azure AD ?**

**R :** Azure AD Premium vous permet d’ajouter et de configurer n’importe quelle application. Vous pouvez configurer, selon les fonctionnalités de votre application et vos préférences, l’authentification unique et l’approvisionnement automatisé.

Pour plus d'informations, consultez la page suivante :

- [Configuration de l'authentification unique pour les applications ne faisant pas partie de la galerie d'applications Azure Active Directory.](active-directory-saas-custom-apps.md)
- [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](active-directory-scim-provisioning.md) 


---

**Q: Comment les utilisateurs peuvent-ils se connecter aux applications à l’aide d’Azure Active Directory ?**
 
**R :** Azure Active Directory fournit aux utilisateurs plusieurs moyens pour afficher et accéder à leurs applications, tels que :

- Panneau d’accès Azure AD

- Lanceur d’applications Office 365

- Authentification directe pour les applications fédérées

- Liens ciblés vers des applications fédérées, avec mot de passe ou des applications existantes

Pour plus d’informations, voir [Déploiement d’applications Azure AD intégrées pour les utilisateurs](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).


---

**Q: Par quels moyens Azure Active Directory active-t-il l’authentification et l’authentification unique pour la connexion aux applications ?**
 
**R :** Azure Active Directory prend en charge de nombreux protocoles standardisés pour l’authentification et l’autorisation, tels que SAML 2.0, OpenID Connect, OAuth 2.0 et WS-Federation. Azure AD prend également en charge la mise en coffre du mot de passe et les fonctionnalités d’authentification automatisées pour les applications qui prennent uniquement en charge l’authentification basée sur les formulaires.

Pour plus d'informations, consultez les pages suivantes :

- [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md)

- [Protocoles d’authentification Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [Fonctionnement de l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**Q : Puis-je ajouter des applications si l’exécution s’effectue en local ?**

**R :** Le proxy d’application Azure AD vous offre un accès facile et sécurisé aux applications web en local de votre choix. Vous pouvez accéder à ces applications de la même façon que vous accédez à vos applications SaaS dans Azure Active Directory. Il est inutile d’avoir recours à un VPN ou à la modification de votre infrastructure réseau.

Pour en savoir plus, voir [Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md).


--- 

**Q : Comment faire pour exiger l’authentification multifacteur pour les utilisateurs accédant à une application particulière ?**

**R :** L’accès conditionnel Azure AD vous permet d’affecter une stratégie d’accès unique à chaque application. Dans votre stratégie, vous pouvez exiger MFA en permanence, ou lorsque les utilisateurs ne sont pas connectés au réseau local.

Pour en savoir plus, voir [Sécurisation de l’accès à Office 365 et à d’autres applications connectées à Azure Active Directory](active-directory-conditional-access.md).


---

**Q : Qu’est-ce que l’approvisionnement automatique des utilisateurs pour les applications SaaS ?**

**R :** Azure Active Directory vous permet d’automatiser la création, la maintenance et la suppression d’identités utilisateur dans plusieurs applications cloud (SaaS) populaires comme Dropbox, Salesforce, ServiceNow et bien plus encore.

Pour plus d’informations, voir [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md)

---

<!---HONumber=AcomDC_0525_2016-->