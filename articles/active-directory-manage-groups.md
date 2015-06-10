<properties 
	pageTitle="Gestion des groupes dans Azure AD" 
	description="Une rubrique qui explique comment gérer les mots de passe dans Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Gestion des groupes dans Azure AD

Un groupe est un ensemble d'utilisateurs et de groupes qui peuvent être gérés comme une seule unité. Les utilisateurs et les groupes qui appartiennent à un groupe particulier sont appelés les membres du groupe. L'utilisation de groupes simplifie l'administration en affectant un ensemble d'autorisations et de droits communs à plusieurs comptes à la fois, au lieu d'affecter des autorisations et des droits à chaque compte individuellement.

Actuellement, dans Azure AD, vous ne pouvez créer que des groupes de sécurité.

Vous pouvez utiliser des groupes pour affecter facilement l'accès des utilisateurs aux applications lorsque vous devez affecter un grand nombre d'utilisateurs à la même application. Vous pouvez également utiliser des groupes lors de la configuration de la gestion de l'accès d'autres services en ligne qui contrôlent l'accès aux ressources, par exemple, SharePoint Online.

Si vous avez configuré la synchronisation de répertoires, vous pouvez afficher les groupes qui ont été synchronisés à partir de votre Windows Server Active Directory local et qui ont la valeur « Active Directory local » dans la propriété « Provenance ». Vous devez continuer à gérer ces groupes dans votre Active Directory local. Ces groupes ne peuvent pas être gérés ou supprimés dans le Portail de gestion Azure.

Si vous avez Office 365, vous pouvez afficher les groupes de distribution et les groupes de sécurité à extension messagerie qui ont été créés et gérés dans le Centre d'administration Exchange dans Office 365. Ces groupes ont la valeur « Office 365 » dans la propriété « Provenance » et doivent continuer à être gérés dans le Centre d’administration Exchange.

Vous pouvez également créer des groupes unifiés via le panneau d'accès. Dans l'onglet Configurer, sous Gestion de groupe, définissez le widget des **groupes d’utilisateurs pouvant créer O365** sur **Oui**. Si vous avez des groupes unifiés Office 365 créés dans le panneau d'accès ou dans Office 365, ces groupes auront la propriété « Provenance » définie sur « Azure Active Directory » et ils peuvent être gérés via le panneau d'accès.

Si vous avez activé la gestion de groupes libre-service pour vos utilisateurs (pour plus d'informations, consultez Gestion des groupes libre-service pour les utilisateurs dans Azure AD), en tant qu’administrateur client vous pouvez également gérer ces groupes via le Portail de gestion Azure. Vous pouvez ajouter et supprimer des membres de groupe, ajouter et supprimer des propriétaires de groupe, modifier des propriétés de groupe et afficher les historiques des groupes qui affichent quelle action a été effectuée au sein du groupe, qui a effectué cette action et à quel moment.

> [AZURE.NOTE]Pour pouvoir affecter un groupe à une application, vous devez utiliser Azure AD Premium. Si vous avez Azure AD Premium, vous pouvez également utiliser des groupes pour affecter l'accès aux applications SaaS qui sont intégrées à Azure AD. Pour plus d'informations, consultez Affectation d’accès à une application SaaS dans Azure AD pour un groupe.

## Étapes suivantes

- [Administration d’Azure AD](active-directory-administer.md)
- [Création ou modification des utilisateurs dans Azure AD](active-directory-create-users.md)
- [Gestion des mots de passe dans Azure AD](active-directory-manage-passwords.md)

<!---HONumber=58-->