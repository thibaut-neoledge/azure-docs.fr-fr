<properties title="Add a user in RemoteApp" pageTitle="Ajout d'un utilisateur dans RemoteApp" description="Découvrez comment ajouter des utilisateurs dans RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" />

#Ajout d'un utilisateur dans RemoteApp
 
Avant que vos utilisateurs puissent afficher et utiliser les applications de RemoteApp, vous devez leur accorder l'accès. C'est l'étape la plus facile : sous l'onglet **Accès utilisateur**, entrez les informations de compte de l'utilisateur auquel accorder l'accès à ce service.

Quelles sont les informations de compte dont vous avez besoin ? Cela dépend du type de collection que vous avez créé (cloud ou hybride) et si vous utilisez Office 365 ProPlus dans cette collection.

##Informations de compte d'utilisateur cloud ou hybride
La collection cloud prend en charge les comptes Microsoft et les comptes Azure Active Directory dont les annuaires sont synchronisés (qui sont également des comptes Office 365). 

La collection hybride prend en charge uniquement les comptes Azure Active Directory synchronisés (à l'aide d'un outil tel que DirSync) à partir d'un déploiement de Windows Server Active Directory. Plus précisément, ils doivent être synchronisés avec l'option de synchronisation de mot de passe ou la fédération des services ADFS doit être configurée. Consultez [Configuration d'Active Directory pour Azure RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-ad/) pour des informations sur la configuration requise d'Azure AD.

**Remarque :** les utilisateurs Azure Active Directory doivent se trouver dans le locataire associé à votre abonnement. (Vous pouvez afficher et modifier votre abonnement sous l'onglet **Paramètres** du portail. Consultez [Modification du locataire Azure Active Directory utilisé par RemoteApp](http://msdn.microsoft.com/fr-fr/3d6c4fd1-c981-4c57-9402-59fe31b11883) pour plus d'informations.)

##Informations de compte d'utilisateur Office 365 ProPlus
Si vous utilisez l'image de modèle Office 365 ProPlus dans votre collection *ou* si vous avez créé une image personnalisée qui utilise Office 365, vous êtes uniquement autorisé à ajouter des utilisateurs Azure Active Directory disposant d'abonnements Office 365 pour le domaine par défaut de votre abonnement. Consultez [Utilisation d'Office 365 avec Azure RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-o365/) pour plus d'informations.

<!--HONumber=35.2-->
