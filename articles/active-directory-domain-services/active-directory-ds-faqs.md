<properties
	pageTitle="Version préliminaire des services de domaine Azure Active Directory : FAQ | Microsoft Azure"
	description="Forum aux questions sur les services de domaine Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Caractéristiques de la version préliminaire des services de domaine Azure Active Directory : FAQ

Cette page répond aux questions fréquemment posées sur la version préliminaire des services de domaine Azure Active Directory. N'hésitez pas à la consulter pour vous tenir au courant des mises à jour.

### Guide de résolution des problèmes
Reportez-vous à notre [Guide de résolution des problèmes](active-directory-ds-troubleshooting.md) pour trouver les solutions aux problèmes couramment rencontrés pendant la configuration ou l’administration des services de domaine Azure Active Directory.


### Configuration

#### Puis-je créer plusieurs domaines pour un seul annuaire Azure AD ?
Non. Vous ne pouvez créer qu’un seul domaine pris en charge par les services de domaine Azure AD par annuaire Azure AD.

#### Puis-je rendre les services de domaine Azure AD disponibles dans plusieurs réseaux virtuels au sein de mon abonnement ?
Le service lui-même ne prend pas directement en charge ce scénario. Les services de domaine Azure AD sont disponibles dans un seul réseau virtuel à la fois. Toutefois, vous pouvez configurer la connectivité entre plusieurs réseaux virtuels afin d’exposer les services de domaine Azure AD à d’autres réseaux virtuels. Cet article décrit comment vous pouvez [connecter des réseaux virtuels dans Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### Puis-je activer les services de domaine Azure AD à l’aide de PowerShell ?
Le déploiement à l’aide de PowerShell ou automatisé des services de domaine Azure AD n’est pas disponible actuellement.

#### Les services de domaine Azure AD sont-ils disponibles dans le nouveau portail Azure ?
Non. Les services de domaine Azure AD ne peuvent être configurés que dans l’ancien portail de gestion Azure (c’est-à-dire https://manage.windowsazure.com). Nous envisageons d’étendre la prise en charge pour le nouveau portail de gestion Microsoft Azure (c’est-à-dire https://portal.azure.com).

#### Puis-je ajouter des contrôleurs de domaine à un domaine géré par les services de domaine Azure AD ?
Non. Le domaine fourni par les services de domaine Azure AD est un domaine géré. Vous n’avez pas besoin d’approvisionner, de configurer ou de gérer par ailleurs les contrôleurs de ce domaine, car ces activités de gestion sont fournies en tant que service par Microsoft. Par conséquent, vous ne pouvez pas ajouter de contrôleurs de domaine supplémentaires (ni en lecture/écriture ni en lecture seule) pour le domaine géré.

### Administration et opérations

#### J’ai activé les services de domaine Azure AD. Quel compte d’utilisateur dois-je utiliser pour joindre des ordinateurs à ce domaine ?
Les comptes d’utilisateurs que vous avez ajoutés au groupe d’administration (c’est-à-dire « AAD DC Administrators ») peuvent joindre des ordinateurs au domaine. En outre, les utilisateurs de ce groupe disposent d’un accès Bureau à distance aux ordinateurs qui ont été joints au domaine.

#### Puis-je exercer des privilèges d’administrateur de domaine pour le domaine fourni par les services de domaine Azure AD ?
Non. Dans la mesure où il s’agit d’un service géré, aucun privilège d’administrateur ne vous est accordé sur le domaine. Cela signifie que les privilèges « Administrateur de domaine » et « Administrateur d’entreprise » ne sont pas disponibles dans le domaine. En outre, les groupes d’administrateurs de domaine ou d’administrateurs d’entreprise existants dans votre annuaire Azure AD ne se voient accorder aucun privilège d’administrateur de domaine ou d’entreprise sur le domaine.

#### Puis-je modifier les appartenances aux groupes à l’aide de LDAP ou d’autres outils d’administration Active Directory sur des domaines fournis par les services de domaine Azure AD ?
Non. Vous ne pouvez pas modifier les appartenances aux groupes dans des domaines pris en charge par les services de domaine Azure AD. Il en va de même pour les attributs d’utilisateur. Vous pouvez toutefois modifier les appartenances aux groupes ou les attributs d’utilisateur dans Azure AD ou sur votre domaine local. Ces modifications sont synchronisées automatiquement avec les services de domaine Azure AD.

#### Puis-je étendre le schéma du domaine fourni par les services de domaine Azure AD ?
Non. Le schéma est administré par Microsoft pour le domaine géré. Les extensions de schéma ne sont pas prises en charge par les services de domaine Azure AD.

#### Puis-je modifier les enregistrements DNS fournis par les services de domaine Azure AD ?
Oui. Les utilisateurs qui font partie du groupe d'administration (c.-à-d. « AAD DC Administrators ») disposent d’autorisations DNS Admin pour modifier les enregistrements DNS.

### Facturation et disponibilité

#### Ce service est-il payant ?
Le service est disponible à un prix réduit spécial pour la durée de la période d’évaluation publique. Il sera facturé à taux plein une fois qu’il sera mis à la disposition générale. Pour plus d’informations, consultez la page relative aux prix appliqués.

#### Le service peut-il être essayé gratuitement ?
Ce service est inclus dans l’essai gratuit d’Azure. Vous pouvez vous inscrire pour bénéficier d’un [essai gratuit d’un mois d’Azure](https://azure.microsoft.com/pricing/free-trial/).

#### Puis-je obtenir les services de domaine Azure AD dans le cadre d’Enterprise Mobility Suite (EMS) ?
Non, les services de domaine Azure AD constituent un service Azure avec paiement à l’utilisation et ne font pas partie d’EMS. Les services de domaine Azure AD sont disponibles pour toutes les références SKU d’Azure AD (Gratuit, De base et Premium) et sont facturés à l’heure d’utilisation.

#### Dans quelles régions Azure le service est-il disponible ?
Pour obtenir la liste des régions Azure où les services de domaine Azure AD sont disponibles, reportez-vous à la [page Régions](active-directory-ds-regions.md).

#### Quand les services de domaine Azure AD seront-ils mis à la disposition générale ?
Nous ne pouvons actuellement fournir aucun calendrier quant à la mise à disposition générale du service.

<!---------HONumber=AcomDC_0309_2016-->