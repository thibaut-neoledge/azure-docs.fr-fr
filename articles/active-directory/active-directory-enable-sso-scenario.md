<properties 
    pageTitle="Je peux compter sur Azure AD pour activer l’authentification unique (SSO) sur toutes mes applications | Microsoft Azure" 
    description="Découvrez comment Azure Active Directory vous permet d’étendre la portée d’une identité et d’en assurer la gestion." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>

<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/12/2015" 
    ms.author="markvi" />


# Je peux compter sur Azure AD pour activer l’authentification unique (SSO) sur toutes mes applications
Cet article est destiné aux responsables informatiques qui souhaitent découvrir la valeur commerciale qu’offre Azure Active Directory pour les applications utilisées par votre organisation.

## Exécution d’applications dans un environnement de cloud  

Aujourd’hui avec le cloud, une multitude d’applications s’offre à nous pour mener à bien toutes sortes de projets. Il arrive même que les services informatiques ne connaissent pas toutes les applications SaaS utilisées dans leur organisation. Bien souvent, ils savent que l’application est utilisée, mais l’acquisition d’un niveau de gouvernance et de sécurité se révèle coûteuse et prend du temps. La plupart des applications ne prennent pas en charge l’intégration avec les systèmes d’identité d’entreprise. Dans d’autres cas, les intégrations uniques sont évitées en raison des coûts et de la complexité.

Par conséquent,

- les organisations sont souvent préoccupées par l’accès non autorisé aux données d’entreprise, les éventuelles fuites de données et d’autres risques de sécurité inhérents aux applications introduits par des applications non gérées. Comme elles ignorent le nombre et la nature des applications utilisées, la simple ébauche d’un plan visant à gérer ces risques peut sembler décourageante.
- Les administrateurs doivent gérer individuellement tous les fournisseurs de services IAM introduits par les applications gérées dans votre environnement. Ils doivent par exemple assumer les tâches de création et de suppression d’utilisateurs et de groupes, ainsi que d’octroi ou d’interdiction d’accès aux applications.
- Les utilisateurs doivent mémoriser différentes informations d’identification pour accéder aux applications dont ils ont besoin. Les mots de passe oubliés ont un impact important sur les coûts de fonctionnement de nombreuses organisations. Le point commun de tous ces problèmes est qu’ils affectent de manière négative la productivité des utilisateurs et vos coûts de fonctionnement.  
 
## En quoi Azure Active Directory peut-il vous aider ?
Azure Active Directory vous aide à résoudre ces problèmes en vous permettant d’étendre facilement votre infrastructure d’identité existante dans le cloud et ainsi :

- Étendre la portée de vos utilisateurs mobiles à une application 
- Étendre la portée de la gestion de l’accès à une application cloud 
- Détecter les applications auxquelles les utilisateurs accèdent


### Étendre la portée de votre identité à une application 

Par défaut, Azure Active Directory fournit l’authentification unique pour les applications cloud hébergées sur Azure et d’autres services en ligne Microsoft tels Office 365, CRM Online et Intune.

Il peut également faire office de répartiteur d’identité pour l’authentification unique à des applications situées dans un autre cloud public tel que Google, Amazon, IBM ou autre. <br> Si une application est connue ou prisée, nous l’intégrons à la galerie d’applications d’Azure Active Directory. Près de 2 500 applications pré-intégrées sont actuellement disponibles dans la galerie d’applications, un nombre qui augmente de manière continue. <br> La configuration de l’authentification unique pour les applications pré-intégrées de la galerie ne prend que quelques secondes. Comment faire si mon application ne figure pas encore dans la galerie d’applications ? Vous pouvez intégrer n’importe quelle application publique dans Azure Active Directory à l’aide d’un Assistant, et ainsi activer l’authentification unique pour celle-ci. Pour en savoir plus, consultez [Déploiement de l’authentification unique à l’aide d’Azure Active Directory pour les applications SaaS récemment acquises](active-directory-single-sign-on-newly-acquired-saas-apps.md) et [Intégration de l’authentification unique Azure Active Directory aux applications existantes](active-directory-sso-integrate-existing-apps.md)<br> Qu’en est-il des applications développées par votre organisation ? Nous fournissons une documentation associée, qui permet à vos développeurs d’intégrer rapidement les applications développées par votre organisation dans Azure Active Directory. Pour en savoir plus, consultez [Azure AD et applications : un guide pour les développeurs](active-directory-applications-guiding-developers-for-lob-applications.md).

Azure Active Directory prend en charge les applications pré-intégrées, les applications publiques ne figurant pas dans la galerie d’applications et les applications développées par votre organisation, et propose l’authentification unique pour toutes vos applications cloud.

Azure Active Directory présente bien d’autres avantages que la pris en charge d’applications cloud. Avec Azure Active Directory, vous pouvez également étendre la portée des identités Azure en fournissant un accès distant sécurisé à des applications locales via l’authentification unique. Il s’agit en fait d’un accès distant qui ne nécessite pas de technologies telles que les réseaux privés virtuels (VPN) ou une autre infrastructure d’accès distant classique pour accéder à des applications web locales. Azure Active Directory offre cette fonctionnalité via les fonctionnalités de proxy d’application.

Le modèle classique d’authentification unique est basé sur un mappage de deux compte individuels situés dans deux référentiels d’identité. Avec Azure Active Directory, vous pouvez même mapper un compte individuel avec un compte d’entreprise partagé tel que votre compte Twitter d’entreprise. En implémentant l’authentification unique pour vos comptes partagés d’entreprise, vous n’avez pas besoin de transmettre les informations d’identification de compte à vos utilisateurs, ce qui améliore considérablement la protection de ces comptes. Pour en savoir plus, consultez [Partage de comptes avec Azure AD](active-directory-sharing-accounts.md)

En étendant la portée de vos identités, un seul mot de passe vous permet d’accéder à des milliers d’applications.



### Extension de la portée de la gestion d’accès à une application cloud

La gestion de l’accès à vos applications cloud est coûteuse si vous devez le faire manuellement pour chacune. Avec Azure Active Directory, vous pouvez gérer l’accès à vos applications cloud par groupes à partir d’un point central, le portail Azure. Vous pouvez affecter l’accès à des utilisateurs individuels ou à des groupes. Pour plus d’informations, consultez [Gestion de l’accès aux applications](active-directory-managing-access-to-apps.md).

Certaines applications telles que Salesforce, Box, Google Apps et Concur fournissent des interfaces d’automatisation pour la création et la suppression (ou désactivation) de comptes. Si un fournisseur offre une interface de ce type, elle est exploitée par Azure Active Directory. En d’autres termes, Azure Active Directory fournit la prise en charge de l’approvisionnement automatique des utilisateurs pour les applications offrant une interface d’automatisation associée.

Avec l’approvisionnement automatique des utilisateurs :

- Lorsqu’un utilisateur a accès à une application associée, Azure Active Directory approvisionne automatiquement le compte d’authentification unique requis pour l’application.
- Lorsqu’un compte d’utilisateur est supprimé dans Azure Active Directory, le compte correspondant dans l’application est désactivé ou supprimé. La configuration de l’approvisionnement automatique des utilisateurs vous offre les avantages suivants :
- 	Il réduit vos coûts de fonctionnement, car il automatise les tâches administratives normalement effectuées par les membres du service informatique.
- Il améliore la sécurité de votre environnement, car il diminue le risque d’accès inutile aux applications.

Pour plus d’informations sur l’approvisionnement automatique des utilisateurs, consultez [Automatiser l’approvisionnement et la désactivation des utilisateurs pour les applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md).


### Détection des applications auxquelles les utilisateurs accèdent

Avec l’authentification unique et l’approvisionnement de compte, Azure Active Directory vous fournit des mécanismes performants pour améliorer l’utilisation que font vos utilisateurs et administrateurs des applications dans votre environnement que vous connaissez. Toutefois, dans les entreprises modernes, les services informatiques n’ont souvent pas connaissance de toutes les applications cloud utilisées. Avec Cloud App Discovery, Azure Active Directory fournit également une solution permettant de détecter ces applications. Avec Cloud App Discovery, vous pouvez :

- détecter les applications utilisées et mesurer l’utilisation par nombre d’utilisateurs, volume de trafic ou nombre de demandes web à l’application ;
- identifier les utilisateurs d’une application ;
- exporter des données pour effectuer une analyse hors ligne approfondie ;
- affecter une priorité aux applications à mettre sous contrôle du service informatique et intégrer les applications en toute simplicité pour activer la gestion des utilisateurs et l’authentification unique.

Pour en savoir plus sur Cloud App Discovery, consultez [Comment puis-je détecter les applications cloud non approuvées utilisées au sein de mon organisation ?](active-directory-cloudappdiscovery-whatis.md)


## Comment faire pour démarrer ?

Avant tout, si vous n’utilisez pas déjà Azure AD et que vous êtes administrateur informatique :

- [Essayez-le !](https://azure.microsoft.com/trial/get-started-active-directory) Vous pouvez vous inscrire pour une période d’essai gratuit de 30 jours, puis déployer votre première solution cloud en moins de 5 minutes.

<!---HONumber=Oct15_HO3-->