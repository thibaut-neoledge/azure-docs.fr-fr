<properties
   pageTitle="Bibliothèque de documents pour la gestion des applications | Microsoft Azure"
   description="Rubriques sur la gestion des applications Azure Active Directory, avec des liens de référence technique pour les procédures, la résolution de problèmes et les FAQ"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/03/2015"
   ms.author="kgremban"/>

# Bibliothèque de documents pour la gestion des applications

Cette page contient des liens utiles pour les principales fonctionnalités de l’authentification unique avec Azure Active Directory. Les liens sont organisés selon les catégories suivantes :

- **Vue d’ensemble :** vue d’ensemble de la fonctionnalité, avec des cas d’utilisation et une présentation des différentes rubriques.
- **Procédure :** des instructions détaillées pour bien démarrer avec des scénarios spécifiques.
- **Résolution de problèmes :** des conseils pour diagnostiquer les problèmes courants.
- **Forum Aux Questions :** des réponses aux questions et préoccupations les plus fréquentes.  

## Cloud App Discovery

| | |
| ------ | ------ |
| Vue d’ensemble | [Comment puis-je détecter les applications cloud non approuvées utilisées au sein de mon organisation ?](active-directory-cloudappdiscovery-whatis.md) |
| Procédure | [Prise en main de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) <br><br> [Considérations relatives à la confidentialité et à la sécurité de Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) <br><br> [Guide de déploiement d’une stratégie de groupe Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx) <br><br> [Guide de déploiement de Cloud App Discovery System Center](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx) <br><br> [Paramètres de Registre de Cloud App Discovery pour les services de proxy](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| Forum Aux Questions | [Cloud App Discovery - Forum aux Questions](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |

## Affectation d’application

| | |
| ------ | ------ |
| Vue d’ensemble | [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md) |
| Procédure | [Création d’une règle simple pour configurer l’appartenance dynamique au groupe](active-directory-accessmanagement-simplerulegroup.md) <br><br> [Utilisation d’un groupe pour gérer l’accès aux applications SaaS](active-directory-accessmanagement-group-saasapps.md) <br><br> [Configuration d’Azure AD pour gérer l’accès aux applications en libre-service](active-directory-accessmanagement-self-service-group-management.md) <br><br> [Gestion des propriétaires d’un groupe](active-directory-accessmanagement-managing-group-owners.md) <br><br> [Gestion des groupes de sécurité dans Azure Active Directory](active-directory-accessmanagement-manage-groups.md) <br><br> [Groupes dédiés dans Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md) <br><br> [Utilisation d’attributs pour créer des règles avancées](active-directory-accessmanagement-groups-with-advanced-rules.md) <br><br> [Automatisation de l’approvisionnement et de la l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md) <br><br> [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md) |

## Authentification unique fédérée

| | |
| ------ | ------ |
| Vue d’ensemble |[Configuration de la fédération avec AD FS](active-directory-aadconnect-get-started-custom.md)
| Procédure | [DirSync avec authentification unique](https://msdn.microsoft.com/library/azure/dn441213.aspx) <br><br> [Microsoft Azure prend désormais en charge la fédération avec Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory/) <br><br> [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md) <br><br> [Marketplace Active Directory](https://azure.microsoft.com/marketplace/active-directory/) |
| Résolution de problèmes | [Résolution des problèmes de l’Assistant de configuration de l’outil DirSync Azure AD](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx) <br><br> [Résolution des problèmes liés à l’installation de l’outil de synchronisation Azure Active Directory et des erreurs de l’Assistant de configuration](https://support.microsoft.com/kb/2684395) <br><br> [Résoudre les problèmes liés à la synchronisation d’annuaires](https://msdn.microsoft.com/library/azure/jj151787.aspx) <br><br> [Résoudre les problèmes d’authentification unique](https://msdn.microsoft.com/library/azure/jj151834.aspx) |
| Forum Aux Questions | [Gestion des certificats pour l’authentification unique fédérée sur Azure Active Directory](active-directory-sso-certs.md) <br><br> [FAQ Azure Active Directory Connect](active-directory-aadconnect-faq.md) |

## Authentification unique avec mot de passe

| | |
| ------ | ------ |
| Procédure | [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md) <br><br> [DirSync avec synchronisation des mots de passe](https://msdn.microsoft.com/library/azure/dn441214.aspx) |
| Résolution de problèmes | [Résolution des problèmes de l’Assistant de configuration de l’outil DirSync Azure AD](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx) <br><br> [Résolution des problèmes liés à l’installation de l’outil de synchronisation Azure Active Directory et des erreurs de l’Assistant de configuration](https://support.microsoft.com/kb/2684395) <br><br> [Résoudre les problèmes liés à la synchronisation d’annuaires](https://msdn.microsoft.com/library/azure/jj151787.aspx) |
| Forum Aux Questions | [FAQ Azure Active Directory Connect](active-directory-aadconnect-faq.md) |

## Approvisionnement d’applications enrichi

| | |
| ------ | ------ |
| Vue d’ensemble | [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md) |
| Procédure | [Didacticiel : Intégration d’Azure Active Directory à Concur](active-directory-saas-concur-tutorial.md) <br><br> [Didacticiel : Intégration d’Azure Active Directory avec DocuSign](active-directory-saas-docussign-tutorial.md) <br><br> [Didacticiel : Intégration d’Azure Active Directory à Dropbox for Business](active-directory-saas-dropboxforbusiness-tutorial.md) <br><br> [Didacticiel : intégration de Google Apps avec Azure Active Directory](active-directory-saas-google-apps-tutorial.md) <br><br> [Didacticiel : Intégration de Salesforce avec Azure Active Directory](active-directory-saas-salesforce-tutorial.md) <br><br> [Didacticiel : Intégration d’Azure Active Directory à ServiceNow](active-directory-saas-servicenow-tutorial.md) <br><br> [Didacticiel : Configuration de Workday pour la synchronisation entrante](active-directory-saas-workday-inbound-tutorial.md) |

## Galerie d’applications Azure AD

| | |
| ------ | ------ |
| Procédure | [Affichage de votre application dans la galerie d’applications Azure AD](active-directory-app-gallery-listing.md) <br><br> [« Apportez votre propre application » avec une configuration SAML libre-service Azure AD](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) |

## Applications personnalisées

| | |
| ------ | ------ |
| Vue d’ensemble | [« Apportez votre propre application » avec une configuration SAML libre-service Azure AD](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) |
| Procédure | [Didacticiel : intégration de Salesforce avec Azure Active Directory](active-directory-saas-salesforce-tutorial.md) |

## Proxy d’application

| | |
| ------ | ------ |
| Vue d’ensemble | [Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md) |
| Procédure | [Activation du proxy d’application Azure AD](active-directory-application-proxy-enable.md) <br><br> [Publier des applications avec le Proxy d’application Azure AD](active-directory-application-proxy-publish.md) <br><br> [Utilisation de l’accès conditionnel](active-directory-application-proxy-conditional-access.md) <br><br> [Utilisation des domaines personnalisés dans le proxy d’application Azure AD](active-directory-application-proxy-custom-domains.md) <br><br> [Utiliser des applications prenant en charge les revendications dans le proxy d’application](active-directory-application-proxy-claims-aware-apps.md) <br><br> [Authentification unique avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md) |
| Résolution de problèmes | [Résoudre les problèmes du proxy d’application](active-directory-application-proxy-troubleshoot.md) |

## Expérience de lancement d’application

| | |
| ------ | ------ |
| Vue d’ensemble | [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md) |
| Procédure | [Volet d’accès Azure Active Directory - EMS](http://blogs.msdn.com/b/haddy_el-haggan_blog/archive/2015/04/02/azure-active-directory-access-panel-ems.aspx) <br><br> [Intégration avec Azure Active Directory](active-directory-how-to-integrate.md) |

<!---HONumber=AcomDC_1210_2015-->