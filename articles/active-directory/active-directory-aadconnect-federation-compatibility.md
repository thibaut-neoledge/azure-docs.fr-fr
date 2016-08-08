<properties
	pageTitle="Liste de compatibilité de fédération Azure AD"
	description="Cette page présente les fournisseurs d’identité non-Microsoft qui peuvent être utilisés pour mettre en œuvre l’authentification unique."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="billmath"/>

# Liste de compatibilité de fédération Azure AD
Azure Active Directory fournit l’authentification unique et une sécurité de l’accès aux applications améliorée pour Office 365 et d’autres ressources de Microsoft Online Services pour des implémentations hybrides et uniquement dans le cloud ne nécessitant aucune solution non-Microsoft. À l’instar de la plupart des services Microsoft Online, Office 365 est intégré à Azure Active Directory pour les services de répertoire, l’authentification et l’autorisation. En outre, Azure Active Directory fournit l’authentification unique à des milliers d’applications SaaS et à des applications web locales. Consultez la galerie d’applications Azure Active Directory pour connaître les applications SaaS prises en charge.

Pour les entreprises qui ont investi dans des solutions de fédération non-Microsoft, cette rubrique contient des conseils relatifs à la configuration de l’authentification unique des utilisateurs Windows Server Active Directory avec les services Microsoft Online à l’aide de fournisseurs d’identité non-Microsoft qui sont inclus dans la « liste de compatibilité de fédération Azure Active Directory » ci-dessous.


![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Oxford Computer Group](http://oxfordcomputergroup.com/), organisme tiers, a testé, de la part de Microsoft, ces expériences d’authentification unique à l’aide de fournisseurs d’identité non-Microsoft par rapport à un ensemble de cas d’utilisation courants avec Azure Active Directory.

Pour plus d’informations sur la façon dont votre fournisseur d’identité tiers peut être répertorié ici, contactez Oxford Computer Groupe à l’adresse [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com).

>[AZURE.IMPORTANT] Oxford Computer Group a uniquement testé la fonctionnalité de fédération de ces scénarios d’authentification unique. Oxford Computer Group n’a pas testé les composants de synchronisation, d’authentification à 2 facteurs, etc., de ces scénarios d’authentification unique.

>Par ailleurs, l’utilisation de la connexion au moyen d’un ID secondaire à UPN n’est pas testée dans ce programme.



- [Azure Active Directory](#azure-active-directory)
- [Optimal IDM Virtual Identity Server Federation Services](#optimal-idm-virtual-identity-server-federation-services)
- [PingFederate 6.11](#pingfederate-611)
- [PingFederate 7.2](#pingfederate-72)
- [PingFederate 8.x](#pingfederate-8.x)
- [Centrify](#centrify)
- [IBM Tivoli Federated Identity Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622)
- [SecureAuth IdP 7.2.0](#secureauth-idp-720)
- [CA SiteMinder 12.52](#ca-siteminder-1252)
- [RadiantOne CFS 3.0](#radiantone-cfs-30)
- [Okta](#okta)
- [OneLogin](#onelogin)
- [NetIQ Access Manager 4.0.1](#netiq-access-manager-401)
- [BIG-IP avec Access Policy Manager BIG-IP ver. 11.3x – 11.6x](#big-ip-with-access-policy-manager-big-ip-ver-113x-116x)
- [VMware Workspace Portal version 2.1](#vmware-workspace-portal-version-21)
- [Sign&go 5.3](#signampgo-53)
- [IceWall Federation Version 3.0](#icewall-federation-version-30)
- [CA Secure Cloud](#ca-secure-cloud)
- [Dell One Identity Cloud Access Manager v7.1](#dell-one-identity-cloud-access-manager-v71)
- [AuthAnvil Single Sign On 4.5](#authavil-single-sign-on-45)

>[AZURE.IMPORTANT] Étant donné qu’il s’agit de produits tiers, Microsoft n’offre aucun support technique concernant les problèmes et questions liés au déploiement, à la configuration, au dépannage, aux meilleures pratiques, etc., de ces fournisseurs d’identité tiers. Pour toute question sur ces fournisseurs d’identité et pour bénéficier d’un support technique, contactez directement les éditeurs tiers pris en charge.

>L’interopérabilité de ces fournisseurs d’identité tiers avec les services cloud Microsoft a uniquement été testée à l’aide des protocoles WS-Federation et WS-Trust. Le protocole SAML n’a pas fait l’objet de tests.

## Azure Active Directory 
Azure Active Directory peut authentifier les utilisateurs en opérant une fédération avec votre Active Directory local ou, à défaut de serveur de fédération local, en utilisant une synchronisation de mot de passe.

Voici la matrice de prise en charge de scénarios de cette expérience d’authentification :


| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |Aucune|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |Aucune|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|
|Applications modernes utilisant ADAL (p. ex., Office 2016)| Pris en charge|Aucune|

Pour plus d’informations sur l’utilisation d’Azure Active Directory avec AD FS, consultez [Liste de contrôle : Utiliser AD FS pour mettre en œuvre et gérer l’authentification unique](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)

Pour plus d’informations sur l’utilisation d’Azure Active Directory avec une synchronisation de mot de passe, consultez [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).


## Optimal IDM Virtual Identity Server Federation Services 
Optimal IDM Virtual Identity Server Federation Services peut authentifier les utilisateurs qui résident dans les répertoires Active Directory locaux des clients.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :


| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |Aucune|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |Authentification Windows intégrée|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Pour plus d’informations sur les stratégies d’accès des clients, consultez [Limiting Access to Office 365 Services Based on the Location of the Client](https://technet.microsoft.com/library/hh526961.aspx) (Limitation de l’accès aux services Office 365 en fonction de l’emplacement du client).|



## PingFederate 6.11 

PingFederate 6.11 implémente la norme d’identité WS Federation largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :


| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |Aucune|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |Aucune (les versions antérieures doivent être mises à niveau vers la version 6.11)|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|

Pour obtenir des instructions sur la façon de configurer ce STS pour fournir une expérience d’authentification unique à vos utilisateurs Active Directory avec PingFederate, téléchargez le fichier pdf [ici.](http://go.microsoft.com/fwlink/?LinkID=266321)

## PingFederate 7.2 
PingFederate 7.2 implémente la norme d’identité WS Federation/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :


| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |Aucune|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |Aucune|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|

Pour obtenir des instructions sur la façon de configurer ce STS pour fournir une expérience d’authentification unique à vos utilisateurs Active Directory avec PingFederate, consultez [ceci.](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)

## PingFederate 8.x 
PingFederate 8.x implémente la norme d’identité WS Federation/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :


| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |Aucune|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |Aucune|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|

Pour obtenir des instructions sur la façon de configurer ce STS pour fournir une expérience d’authentification unique à vos utilisateurs Active Directory avec PingFederate, consultez [ceci.](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## Centrify 
Centrify permet d’offrir une expérience d’authentification unique fédérée pour Office 365 sans exiger l’hébergement d’un serveur de fédération local.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :


| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |Aucune|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |Aucune|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Le contrôle d’accès des clients n’est pas pris en charge. 

Pour plus d’informations sur Centrify, consultez [ceci.](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp)|

## IBM Tivoli Federated Identity Manager 6.2.2 
IBM Tivoli Federated Identity Manager 6.2.2 avec IBM Security Access Manager for Microsoft Applications 1.4 implémente la norme d’identité WS Federation/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |Aucune|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |Aucune|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|

Pour plus d’informations sur IBM Tivoli Federated Identity Manager, consultez [IBM Security Access Manager for Microsoft Applications](http://www-01.ibm.com/support/docview.wss?uid=swg24029517) (IBM Security Access Manager pour applications Microsoft).

## SecureAuth IdP 7.2.0 
SecureAuth IdP 7.2.0 implémente la norme d’identité WS Federation/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |Aucune|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |Aucune|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|

Pour plus d’informations sur SecureAuth, consultez [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293).

## CA SiteMinder 12.52 
CA SiteMinder Federation 12.52 implémente la norme d’identité WS Federation/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |Authentification Windows intégrée|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |Authentification Windows intégrée|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|

Pour plus d’informations sur CA SiteMinder, consultez [CA SiteMinder Federation.](http://www.ca.com/us/products/ca-single-sign-on.html)

## RadiantOne CFS 3.0 
RadiantOne Cloud Federation Service (CFS) 3.0 implémente la norme d’identité WS Federation/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |Aucune|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |Authentification Windows intégrée|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|

Pour plus d’informations sur RadiantOne CFS, consultez [RadiantOne CFS.](http://www.radiantlogic.com/products/radiantone-cfs/)


## Okta 
Okta implémente la norme d’identité WS Federation/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :


| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |L’authentification Windows intégrée nécessite la configuration supplémentaire d’un serveur web et de l’application Okta.|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |Authentification Windows intégrée|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|

Pour plus d’informations sur Okta, consultez [Okta.](https://www.okta.com/)
 
## OneLogin 
Testé en mai 2014, OneLogin implémente la norme d’identité WS Federation/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |Authentification Windows intégrée|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |Authentification Windows intégrée|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|

Pour plus d’informations sur OneLogin, consultez [OneLogin.](https://www.onelogin.com/)

## NetIQ Access Manager 4.0.1 
NetIQ Access Manager 4.0.1 implémente la norme d’identité WS Federation/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |*Contrats Kerberos pris en charge|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |L’authentification Windows intégrée n’est pas prise en charge|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucun|

* NetIQ prend en charge l’authentification Kerberos via la configuration d’un contrat Kerberos. Pour obtenir de l’aide sur cette configuration, contactez NetIQ ou consultez le guide d’installation et de configuration. Pour plus d’informations sur NetIQ Access Manager, consultez [NetIQ Access Manager.](https://www.netiq.com/documentation/netiqaccessmanager4/identityserverhelp/data/b12iqp0m.html)

## BIG-IP avec Access Policy Manager BIG-IP ver. 11.3x – 11.6x 
BIG-IP avec Access Policy Manager, (APM) BIG-IP ver. 11.3x – 11.6x implémente la norme d’identité SAML largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |Aucune|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Non pris en charge |Non pris en charge|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|

Pour plus d’informations sur BIG-IP Access Policy Manager, consultez [BIG-IP Access Policy Manager.](https://f5.com/products/modules/access-policy-manager)

Pour obtenir des instructions sur la façon de configurer ce STS pour fournir une expérience d’authentification unique à vos utilisateurs Active Directory avec BIG-IP Access Policy Manager, téléchargez le fichier pdf [ici.](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf)

## VMware Workspace Portal version 2.1 
VMware Workspace Portal version 2.1 implémente la norme d’identité WS Federation/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |L’authentification Windows intégrée n’est pas prise en charge|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |L’authentification Windows intégrée n’est pas prise en charge|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|

Pour plus d’informations sur VMware Workspace Portal version 2.1, téléchargez le fichier pdf [ici.](http://pubs.vmware.com/workspace-portal-21/topic/com.vmware.ICbase/PDF/workspace-portal-21-resource.pdf)

## Sign&go 5.3 
Sign&go 5.3 implémente la norme d’identité WS Federation/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |Contrats Kerberos pris en charge |
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |Aucune|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|


Sign&go 5.3 prend en charge l’authentification Kerberos via la configuration d’un contrat Kerberos. Pour obtenir de l’aide sur cette configuration, contactez Ilex ou consultez le guide d’installation et de configuration [ici.](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)


## IceWall Federation Version 3.0 
IceWall Federation Version 3.0 implémente la norme d’identité WS Federation/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |L’authentification Windows intégrée n’est pas prise en charge|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |L’authentification Windows intégrée n’est pas prise en charge|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|

Pour plus d’informations sur IceWall Federation, consultez [ceci](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) et [ceci.](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html)

## CA Secure Cloud 

CA Secure Cloud implémente la norme d’identité WS Federation/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |L’authentification Windows intégrée n’est pas prise en charge|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |L’authentification Windows intégrée n’est pas prise en charge|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|

Pour plus d’informations sur CA Secure Cloud, consultez [CA Secure Cloud.](http://www.ca.com/us/products/security-as-a-service.aspx)

## Dell One Identity Cloud Access Manager v7.1 
Dell One Identity Cloud Access Manager implémente la norme d’identité WS Federation/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et une structure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |Aucune|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |Aucune|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|

Pour en savoir plus sur Dell One Identity Cloud Access Manager, consultez [Dell One Identity Cloud Access Manager.](http://software.dell.com/products/cloud-access-manager)

 Pour savoir comment configurer ce STS afin de fournir l’expérience d’authentification unique à vos utilisateurs Office 365, consultez [Configure Office 365 Users](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365) (Configurer les utilisateurs d’Office 365).

## AuthAnvil Single Sign On 4.5 
AuthAnvil Single Sign On 4.5 implémente la norme d’identité WS Federation/WS-Trust largement utilisée pour fournir une expérience d’authentification unique et une infrastructure d’échange d’attributs.

Voici la matrice de prise en charge des scénarios pour cette expérience d’authentification unique :

| Client |Support |Exceptions|
| --------- | --------- |--------- |
| Clients web (p. ex., Exchange Web Access et SharePoint Online) | Pris en charge |L’authentification Windows intégrée n’est pas prise en charge|
| Applications clientes riches (p. ex., Lync, abonnement Office, CRM) | Pris en charge |L’authentification Windows intégrée n’est pas prise en charge|
| Clients de messagerie riches (p. ex., Outlook et ActiveSync) | Pris en charge |Aucune|


Pour plus d’informations, consultez [AuthAnvil Single Sign On.](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-)

<!---HONumber=AcomDC_0720_2016-->