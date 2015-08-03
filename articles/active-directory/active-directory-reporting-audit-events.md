<properties
   pageTitle="Événements de rapport d'audit d'Azure Active Directory"
   description="Événements audités disponibles pour l'affichage et le téléchargement à partir d'Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/18/2015"
   ms.author="kenhoff"/>

# Événements de rapport d'audit d'Azure Active Directory

Le rapport d'audit d'Azure Active Directory permet aux clients d'identifier les actions privilégiées qui se sont produites dans leur Azure Active Directory. Ces actions privilégiées incluent les modifications d'élévation (par exemple la création de rôle ou les réinitialisations de mot de passe), la modification des configurations de stratégie (par exemple les stratégies de mot de passe) ou bien les modifications apportées à la configuration de répertoire (par exemple les modifications apportées aux paramètres de fédération de domaine). Les rapports fournissent l’enregistrement d’audit pour le nom d’événement, l’acteur qui a effectué l’action, la ressource cible affectée par la modification, ainsi que la date et l’heure (UTC). Les clients sont en mesure de récupérer la liste des événements d'audit pour leur Azure Active Directory via le [Portail de gestion Azure](https://manage.windowsazure.com/), comme décrit dans [Afficher vos rapports d'accès et d’utilisation](active-directory-view-access-usage-reports.md).

## Rétention des rapports d’audit

Les événements du rapport d’audit d’Azure AD sont conservés pendant 180 jours. Pour plus d’informations sur la rétention des rapports, consultez la page [Stratégies de rétention des rapports Azure Active Directory](active-directory-reporting-retention.md).

Pour les clients intéressés par le stockage de leurs événements d’audit pour des périodes de rétention plus longues, l’API de création de rapports peut être utilisée pour extraire régulièrement des événements d’audit dans un magasin de données distinct. Consultez la rubrique [Prise en main de l’API de création de rapports](active-directory-reporting-api-getting-started.md) pour en savoir plus.

## Propriétés incluses dans chaque événement d'audit

| Propriété | Description |
| ------	| ------								|
| Date et heure | La date et l'heure auxquelles l'audit s'est produit |
| Acteur | L'utilisateur ou le principal du service qui a effectué l'action |
| Action | L'action qui a été effectuée |
| Cible | L'utilisateur ou le principal du service pour qui l'action a été effectuée |

## Liste d'événements de rapport d'audit

<!--- audit event descriptions should be in the past tense --->

| Événements | Description de l'événement |
| ------------------------------	| -------																					|
| Ajouter un utilisateur | Ajout d'un utilisateur au répertoire |
| Supprimer l'utilisateur | Suppression de l'utilisateur du répertoire |
| Définir les propriétés de licence | Définition des propriétés de la licence pour un utilisateur du répertoire |
| Réinitialiser le mot de passe de l'utilisateur | Réinitialisation du mot de passe pour un utilisateur du répertoire |
| Modifier le mot de passe de l'utilisateur | Modification du mot de passe pour un utilisateur du répertoire |
| Modifier la licence de l'utilisateur | Modification de la licence attribuée à un utilisateur du répertoire |
| Mettre à jour l'utilisateur | Mise à jour d'un utilisateur dans le répertoire |
| Ajouter un membre au rôle | Ajout d'un utilisateur à un rôle de répertoire |
| Supprimer un membre du rôle | Suppression d'un utilisateur d'un rôle de répertoire |
| Définir les informations de contact d'entreprise | Définition des préférences de contact au niveau de l'entreprise Cela inclut les adresses de messagerie pour le marketing, ainsi que les notifications techniques à propos de Microsoft Online Services. |
| Ajouter un partenaire à l'entreprise | Ajout d'un partenaire au répertoire |
| Supprimer le partenaire de l'entreprise | Suppression d'un partenaire du répertoire |
| Ajouter un principal du service | Ajout d'un principal du service dans le répertoire |
| Supprimer le principal du service | Suppression d'un principal du service du répertoire |
| Ajouter les informations d'identification du principal du service | Ajout des informations d'identification à un principal du service |
| Supprimer les informations d'identification du principal du service | Suppression des informations d'identification d'un principal du service |
| Ajouter le domaine à l'entreprise | Ajout d'un domaine au répertoire |
| Supprimer le domaine de l'entreprise | Suppression d'un domaine du répertoire |
| Mettre à jour le domaine | Mise à jour d'un domaine dans le répertoire |
| Définir l'authentification de domaine | Modification du paramètre de domaine par défaut de l'entreprise |
| Définir les paramètres de fédération du domaine | Mise à jour des paramètres de fédération d'un domaine |
| Vérifier le domaine | Vérification d'un domaine dans le répertoire |
| Vérifier le domaine vérifié par courrier électronique | Vérification d'un domaine dans le répertoire à l'aide de la vérification par courrier électronique. |
| Ajouter l'entrée de délégation | Ajout d'une entrée de délégation au répertoire |
| Définir l'entrée de délégation | Mise à jour d'une entrée de délégation au répertoire |
| Supprimer l'entrée de délégation | Suppression d'une entrée de délégation du répertoire |
| Définir l'indicateur DirSyncEnabled de l'entreprise | Définition de la propriété qui active un répertoire pour Azure AD Sync |
| Définir la stratégie de mot de passe | Définition des contraintes de longueur et de caractères des mots de passe utilisateur |
| Définir les informations de l'entreprise | Mise à jour des informations au niveau de l'entreprise Consultez l'applet de commande Powershell [Get-msolcompanyinformation permet](https://msdn.microsoft.com/library/azure/dn194126.aspx) pour obtenir plus de détails. |
| Définir le mot de passe utilisateur | Définition de la propriété qui force un utilisateur à modifier son mot de passe lors de la connexion |

<!---

List of events that still need descriptions:

Restore Application
Set String Auth Policy
Promote tenant to partner

--->

### Attributs de l'utilisateur inclus dans l'événement d'audit de mise à jour utilisateur

L'événement d'audit « Mettre à jour l'utilisateur » inclut des informations supplémentaires sur les attributs utilisateur qui ont été mis à jour. Pour chaque attribut, la valeur précédente et la nouvelle valeur sont toutes les deux incluses.

| Attribut | Description |
| ---------------------------------	| ---------																			|
| AccountEnabled | L'utilisateur est autorisé à se connecter. |
| AssignedLicense | Toutes les licences attribuées à l'utilisateur |
| AssignedPlan | Plans de service résultant des licences attribuées à l'utilisateur |
| LicenseAssignmentDetail | Plus d'informations sur les licences attribuées à l'utilisateur Par exemple, si la licence basée sur le groupe a été impliquée, cela inclut le groupe qui a accordé ladite licence. |
| Mobile | Téléphone mobile de l'utilisateur |
| OtherMail | Adresse de messagerie alternative de l'utilisateur |
| OtherMobile | Téléphone mobile alternatif de l'utilisateur |
| StrongAuthenticationMethod | Liste des méthodes de vérification configurées par l'utilisateur pour l'authentification multi-facteur, comme l'appel vocal, le SMS ou la vérification du code à partir d'une application mobile. |
| StrongAuthenticationRequirement | Dans le cas où l'authentification multi-facteur est appliquée, activée ou désactivée pour cet utilisateur |
| StrongAuthenticationUserDetails | Numéro de téléphone de l'utilisateur, numéro de téléphone alternatif et adresse de messagerie utilisés pour la vérification de l'authentification multi-facteur et de la réinitialisation du mot de passe. |
| TelephoneNumber | Numéro de téléphone de l'utilisateur |

Les enregistrements d'audit sont un contrôle requis pour de nombreuses réglementations de conformité. Pour que les clients utilisant la création de rapports d'audit d'Azure Active Directory Azure puissent respecter les réglementations de conformité, il leur est recommandé d'envoyer une copie de cette rubrique d'aide avec la copie de leur rapport d'audit exporté afin d'expliquer les détails du rapport. Si vous souhaitez comprendre les réglementations de conformité qui sont actuellement respectées par Azure, dirigez-vous vers la [page de conformité](http://azure.microsoft.com/support/trust-center/compliance/) de Microsoft Azure Trust Center.
 

<!---HONumber=July15_HO4-->