<properties
	pageTitle="Forum Aux Questions sur Azure Active Directory | Microsoft Azure"
	description="Forum Aux Questions sur Azure Active Directory"
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
	ms.date="04/20/2016"
	ms.author="markusvi"/>

# Forum Aux Questions sur Azure Active Directory

Azure Active Directory (Azure AD) est un ensemble complet de fonctionnalités de gestion des identités et des accès basées sur le cloud qui fournit l’identité en tant que service (IDaaS). En tant que fournisseur IDaaS, Azure AD permet de contrôler « *qui est autorisé à effectuer quelles actions* » dans votre réseau. Azure AD fournit en outre l’authentification unique (SSO) à vos applications. Ce service vous permet d’améliorer considérablement l’expérience d’authentification de vos utilisateurs. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)



## Accès à Azure et Azure Active Directory

**** Q : Pourquoi est-ce que je ne trouve pas Azure AD dans le portail Azure (http://portal.azure.com)?**

**R :** L’expérience d’administration Azure AD est toujours disponible dans le portail Azure Classic (`http://manage.windowsazure.com`). Le lancement d’une version préliminaire publique des nouvelles expériences d’administration intégrées au portail Azure est prévu d’ici au mois de septembre. Pour obtenir les toutes dernières informations sur les versions, consultez le [Blog de l’équipe Active Directory](https://blogs.technet.microsoft.com/ad/).


---

**** Q : Pourquoi le message « aucun abonnement trouvé » s’affiche lorsque j’essaie d’accéder à Azure AD dans le portail Azure Classic (http://manage.windowsazure.com)?**

**R :** Vous aurez besoin des autorisations sur un abonnement Azure. Si vous détenez une licence Office 365 ou Azure AD payante, accédez à [http://aka.ms/accessAAD](http://aka.ms/accessAAD) pour une étape d’activation unique, sinon vous devrez activer une [version d’essai Azure](https://azure.microsoft.com/pricing/free-trial/) complète ou un abonnement payant.

---

**Q : Quelle est la relation entre Azure AD, Office 365 et Azure ?**

**R :** Microsoft Azure est une plateforme et une infrastructure de cloud computing ouverte, flexible et d’entreprise pour la création, le déploiement et la gestion des applications et des services via un réseau mondial de centres de données gérés par Microsoft. Azure fournit, par exemple, des solutions Software as a Service (SaaS), telles qu’Office 365. Office 365 est un groupe d’abonnements de logiciels et de services proposant des logiciels de productivité et les services en ligne associés. Azure AD complète Office 365 avec un ensemble complet de fonctionnalités de gestion des identités et des accès basées sur le cloud qui fournit l’identité en tant que service (IDaaS). Si vous détenez un abonnement à Office 365, vous disposez déjà d’un client Azure AD s’exécutant sur Azure.


---

**Q : quelles fonctionnalités Azure AD sont disponibles gratuitement ?**

**R :** Toutes les fonctionnalités communes d’un abonnement Azure sont disponibles gratuitement. Pour obtenir une liste complète de ces fonctionnalités, consultez [Fonctionnalités communes](active-directory-editions.md/#common-features).



---

Q : **Comment puis-je connecter mon annuaire local à Azure AD ?**

R : Vous pouvez connecter votre annuaire local à Azure AD à l’aide d’**Azure AD Connect**. Pour plus d’informations, consultez [Intégration des identités locales avec Azure Active Directory](active-directory-aadconnect.md).


---

**Q : Comment configurer l’authentification unique entre mon annuaire local et mes applications cloud ?**

**R :** Vous devez uniquement configurer l’authentification unique entre votre annuaire local et Azure AD. Tant que vos applications cloud sont gérées par Azure AD, vous pouvez étendre la portée de l’authentification unique à votre environnement local en implémentant une fédération avec la synchronisation des mots de passe ou ADFS. Les deux options sont incluses dans **Azure AD Connect**. Pour plus d’informations, consultez [Intégration des identités locales avec Azure Active Directory](active-directory-aadconnect.md).
  


---

**Q : Azure AD m’aide-t-il à gérer mon infrastructure locale ?**

**R :** Oui, c’est le cas. L’édition Azure AD Premium vous propose **Connect Health**. Azure AD Connect Health vous permet de surveiller et d’analyser votre infrastructure d’identité locale et les services de synchronisation. Pour en savoir plus, consultez [Surveillez votre infrastructure d’identité locale et vos services de synchronisation dans le cloud](active-directory-aadconnect-health.md).



## Gestion des mots de passe

**Q : Puis-je utiliser l’écriture différée de mot de passe Azure AD sans synchronisation de mot de passe ? (également appelée « J’aimerais utiliser la réinitialisation de mot de passe libre-service Azure AD avec l’écriture différée de mot de passe, mais je ne veux pas que mes mots de passe soient stockés dans le cloud)**

**R :** vous n’avez pas besoin de synchroniser vos mots de passe AD sur Azure AD afin d’activer l’écriture différée. Dans un environnement fédéré,l’authentification unique Azure AD repose sur l’annuaire local pour authentifier l’utilisateur. Ce scénario ne nécessite pas le suivi du mot de passe local dans Azure AD.

---

**Q : Combien de temps faut-il pour qu’un mot de passe soit écrit de manière différée sur AD en local ?**

**R :** L’écriture différée de mot de passe fonctionne en temps réel. Pour en savoir plus, voir [Prise en main de la gestion de mot de passe](active-directory-passwords-getting-started.md).


---

**Q : Puis-je utiliser l’écriture différée de mot de passe avec des mots de passe gérés par un administrateur ?**

**R :** Oui, si cette fonction est activée, les opérations de mot de passe effectuées par un administrateur sont écrites de manière différée dans votre environnement local. Pour voir d’autres questions relatives aux mots de passe, voir [Forum aux questions - Gestion des mots de passe](active-directory-passwords-faq.md).



## Accès aux applications


**Q : Où puis-je trouver une liste des applications qui sont déjà intégrées à Azure AD et leurs fonctionnalités ?**

**R :** Azure AD dispose de plus de 2600 applications pré-intégrées issues de Microsoft, de fournisseurs de services d’application ou de partenaires. Pour obtenir une liste complète des applications déjà intégrées, voir [Marketplace Active Directory](https://azure.microsoft.com/marketplace/active-directory/).


---

**Q : Que se passe-t-il si l’application dont j’ai besoin ne figure pas dans le Marketplace Azure AD ?**

**R :** Azure AD Premium vous permet d’ajouter et de configurer n’importe quelle application. En outre, vous pouvez configurer, selon les fonctionnalités de votre application et vos préférences, l’authentification unique et l’approvisionnement automatisé. Pour plus d'informations, consultez la page suivante :

- [Configuration de l'authentification unique pour les applications ne faisant pas partie de la galerie d'applications Azure Active Directory.](active-directory-saas-custom-apps.md)
- [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](active-directory-scim-provisioning.md) 



---

**Q : Puis-je ajouter des applications si l’exécution s’effectue en local ?**

**R :** Grâce au proxy d’application Azure AD, vos applications web locales sont maintenant accessibles de la même manière que vos applications SaaS dans Azure Active Directory, sans devoir recourir à un VPN ou à une modification de l’infrastructure réseau. Pour en savoir plus, voir [Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md).


--- 

**Q : Comment faire pour exiger l’authentification multifacteur pour les utilisateurs accédant à une application particulière ?**

**R :** L’accès conditionnel Azure AD vous permet d’affecter une stratégie d’accès unique à chaque application. Dans votre stratégie, vous pouvez exiger MFA en permanence, ou lorsque les utilisateurs ne sont pas connectés au réseau local. Pour en savoir plus, voir [Sécurisation de l’accès à Office 365 et à d’autres applications connectées à Azure Active Directory](active-directory-conditional-access.md).

<!---HONumber=AcomDC_0518_2016-->