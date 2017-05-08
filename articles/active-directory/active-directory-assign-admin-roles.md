---
title: "Attribution de rôles d’administrateur dans Azure Active Directory | Microsoft Docs"
description: "Décrit les rôles d’administrateur disponibles avec Azure Active Directory et leur utilisation."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7fc27e8e-b55f-4194-9b8f-2e95705fb731
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: fdb4ed95b25519fb7ff319fe20a0dc5c54506506
ms.lasthandoff: 04/28/2017


---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Attribution de rôles d’administrateur dans Azure Active Directory
> [!div class="op_single_selector"]
> * [portail Azure](active-directory-assign-admin-roles-azure-portal.md)
> * [Portail Azure Classic](active-directory-assign-admin-roles.md)
>
>

À l’aide d’Azure Active Directory (Azure AD), vous pouvez affecter des administrateurs distincts à différentes fonctions. Ces administrateurs ont accès à plusieurs fonctionnalités du portail Azure ou du portail Azure Classic et, selon leur rôle, peuvent créer ou modifier des utilisateurs, attribuer des rôles d’administrateur à d’autres personnes, réinitialiser les mots de passe d’utilisateur, gérer les licences utilisateur et les domaines, etc. Un utilisateur à qui un rôle d’administrateur est affecté a les mêmes autorisations sur tous les services cloud auxquels votre organisation est abonnée, peu importe que vous attribuiez le rôle dans le portail Office 365 ou dans le portail Azure Classic ou encore à l’aide du module Azure AD pour Windows PowerShell.

Les rôles d’administrateur disponibles sont les suivants :

* **Administrateur de facturation**: effectue les achats, gère les abonnements ainsi que les tickets de support et surveille l’état des services.
* **Administrateur général/Administrateur d’entreprise**: a accès à toutes les fonctionnalités d’administration. La personne qui s’inscrit pour le compte Azure devient administrateur général. Seuls les administrateurs généraux peuvent affecter d’autres rôles d’administrateur. Une entreprise peut comprendre plusieurs administrateurs généraux.

  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de l’entreprise ». Il est « Administrateur général » dans le [portail Azure](https://portal.azure.com).
  >
  >

* **Administrateur de conformité**:les utilisateurs disposant de ce rôle possèdent des autorisations de gestion dans le Centre de sécurité et conformité compatibilité de la sécurité Office 365 et le Centre d'administration Exchange, et peuvent lire les journaux d’audit dans le Centre d’administration Office 365. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrateur de service CRM** : les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft CRM Online, lorsque le service est présent. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
* **Approbateur d’accès au référentiel sécurisé client**: lorsque le service de référentiel sécurisé est activé, les utilisateurs disposant de ce rôle peuvent approuver d’accès aux informations de l’entreprise par les ingénieurs de Microsoft. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
* **Administrateurs d’appareils** : les utilisateurs disposant de ce rôle deviennent administrateurs sur tous les appareils Windows 10 joints à Azure Active Directory.
* **Lecteurs de répertoire**: il s’agit d’un rôle hérité qui doit être affecté aux applications ne prenant pas en charge [l’infrastructure de consentement](active-directory-integrating-applications.md). Vous ne devez pas l’affecter à tous les utilisateurs.
* **Comptes de synchronisation de répertoire**: n’utilisez pas cela. Ce rôle est automatiquement attribué au service Azure AD Connect et n’est pas prévu ni pris en charge pour une autre utilisation.
* **Enregistreurs de répertoire**: il s’agit d’un rôle hérité qui doit être affecté aux applications ne prenant pas en charge [l’infrastructure de consentement](active-directory-integrating-applications.md). Vous ne devez pas l’affecter à tous les utilisateurs.
* **Administrateur de service Exchange**: les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft Exchange Online, lorsque le service est présent. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
* **Administrateur de service Intune**: les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft Intune Online, lorsque le service est présent. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
* **Skype pour administrateur de service d’entreprise**: les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft Skype Entreprise, lorsque le service est présent. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d). Ce rôle a été appelé précédemment sous le nom de rôle **Administrateur de service Lync** .
* **Inviteur d’invités** : les utilisateurs ayant ce rôle peuvent gérer les invitations d’invités. Il n’inclut pas d’autres autorisations.
* **Administrateur de la boîte aux lettres** : ce rôle n’est utilisé que pour la prise en charge de la messagerie Exchange Online sur les appareils RIM Blackberry. Si votre organisation n’utilise pas la messagerie Exchange Online sur les appareils RIM Blackberry, n’employez pas ce rôle.
* **Support de niveau 1 de partenaire** : ne pas utiliser. Ce rôle est déconseillé et sera prochainement supprimé d’Azure AD. Il s’adresse à un petit nombre de partenaires revendeurs Microsoft et n’est pas destiné à une utilisation générale.
* **Support de niveau 2 de partenaire** : ne pas utiliser. Ce rôle est déconseillé et sera prochainement supprimé d’Azure AD. Il s’adresse à un petit nombre de partenaires revendeurs Microsoft et n’est pas destiné à une utilisation générale.
* **Administrateur de mots de passe/Administrateur du support technique**: réinitialise les mots de passe, gère les demandes de service et surveille l’état des services. Les administrateurs de mots de passe peuvent réinitialiser uniquement les mots de passe des utilisateurs et des autres administrateurs de mots de passe.

  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur Helpdesk ».
  >
  >

* **Administrateur de service Power BI** : les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft Power BI, lorsque le service est présent. Pour en savoir plus, consultez la rubrique [Faciliter l’administration de Power BI](https://powerbi.microsoft.com/en-us/blog/making-it-easier-to-administer-power-bi/).
* **Administrateur de rôles privilégiés** : les utilisateurs disposant de ce rôle peuvent gérer Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md) et mettre à jour les attributions de rôles pour d’autres utilisateurs.
* **Administrateur de service SharePoint**: les utilisateurs disposant de ce rôle ont des autorisations globales dans Microsoft SharePoint Online, lorsque le service est présent. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
* **Administrateur de services fédérés**: gère les demandes de service et surveille l’état des services.

  > [!NOTE]
  > Pour assigner le rôle d’administrateur du service à un utilisateur, l’administrateur général doit d’abord assigner des autorisations administratives à l’utilisateur du service, comme Exchange Online, puis lui assigner le rôle d’administrateur du service dans le portail Azure Classic.
  >
  >
* **Administrateur de comptes utilisateur**: réinitialise les mots de passe, surveille l’état des services et gère les comptes d’utilisateur, les groupes d’utilisateurs et les demandes de service. Certaines limitations s’appliquent aux autorisations des administrateurs d’utilisateurs. Par exemple, ils ne peuvent pas supprimer un administrateur général, ni créer d’autres administrateurs. Ils ne peuvent pas non plus réinitialiser les mots de passe des administrateurs de facturation, généraux et de services fédérés.
* **Lecteur de sécurité** : accès en lecture seule à certaines fonctionnalités de sécurité des services Identity Protection Center, Privileged Identity Management, Monitor Office 365 Service Health et Centre de sécurité et conformité Office 365.
* **Administrateur de sécurité** : toutes les autorisations en lecture seule du rôle **Lecteur de sécurité** plus certaines autorisations d’administration supplémentaires pour les mêmes services : Identity Protection Center, Privileged Identity Management, Monitor Office 365 Service Health et Centre de sécurité et conformité Office 365.

## <a name="administrator-permissions"></a>Autorisations des administrateurs
### <a name="billing-administrator"></a>Administrateur de facturation
| Peut | Ne peut pas |
| --- | --- |
| <p>Afficher les informations sur la société et les utilisateurs</p><p>Gérer les tickets de support Office</p><p>Effectuer des opérations de facturation et d’achat pour des produits Office</p> |<p>Réinitialiser les mots de passe utilisateur</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et groupes, et gérer les licences utilisateur</p><p>Gérer des domaines</p><p>Modifier les informations de l’entreprise</p><p>Déléguer des rôles administratifs à d’autres personnes</p><p>Utiliser la synchronisation de répertoires</p><p>Afficher des rapports</p> |

### <a name="global-administrator"></a>Administrateur général
| Peut | Ne peut pas |
| --- | --- |
| <p>Afficher les informations sur la société et les utilisateurs</p><p>Gérer les tickets de support Office</p><p>Effectuer des opérations de facturation et d’achat pour des produits Office</p> <p>Réinitialiser les mots de passe utilisateur</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et groupes, et gérer les licences utilisateur</p><p>Gérer des domaines</p><p>Modifier les informations de l’entreprise</p><p>Déléguer des rôles administratifs à d’autres personnes</p><p>Utiliser la synchronisation de répertoires</p><p>Activer ou désactiver l’authentification multifacteur</p><p>Afficher des rapports</p> |N/A |

### <a name="password-administrator"></a>Administrateur de mots de passe
| Peut | Ne peut pas |
| --- | --- |
| <p>Afficher les informations sur la société et les utilisateurs</p><p>Gérer les tickets de support Office</p><p>Réinitialiser les mots de passe utilisateur</p> |<p>Effectuer des opérations de facturation et d’achat pour des produits Office</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et groupes, et gérer les licences utilisateur</p><p>Gérer des domaines</p><p>Modifier les informations de l’entreprise</p><p>Déléguer des rôles administratifs à d’autres personnes</p><p>Utiliser la synchronisation de répertoires</p><p>Afficher des rapports</p> |

### <a name="service-administrator"></a>Administrateur de services fédérés
| Peut | Ne peut pas |
| --- | --- |
| <p>Afficher les informations sur la société et les utilisateurs</p><p>Gérer les tickets de support Office</p> |<p>Réinitialiser les mots de passe utilisateur</p><p>Effectuer des opérations de facturation et d’achat pour des produits Office</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et groupes, et gérer les licences utilisateur</p><p>Gérer des domaines</p><p>Modifier les informations de l’entreprise</p><p>Déléguer des rôles administratifs à d’autres personnes</p><p>Utiliser la synchronisation de répertoires</p><p>Afficher des rapports</p> |

### <a name="user-administrator"></a>Administrateur d’utilisateurs
| Peut | Ne peut pas |
| --- | --- |
| <p>Afficher les informations sur la société et les utilisateurs</p><p>Gérer les tickets de support Office</p><p>Réinitialisez les mots de passe utilisateur, avec certaines limitations. Ils ne peuvent pas non plus réinitialiser les mots de passe des administrateurs de facturation, généraux et de services fédérés.</p><p>Créer et gérer des vues utilisateur</p><p>Créer, modifier et supprimer des utilisateurs et groupes, et gérer les licences utilisateur, avec des restrictions. Il lui est impossible de supprimer un administrateur général ou de créer d’autres administrateurs.</p> |<p>Effectuer des opérations de facturation et d’achat pour des produits Office</p><p>Gérer des domaines</p><p>Modifier les informations de l’entreprise</p><p>Déléguer des rôles administratifs à d’autres personnes</p><p>Utiliser la synchronisation de répertoires</p><p>Activer ou désactiver l’authentification multifacteur</p><p>Afficher des rapports</p> |

### <a name="security-reader"></a>Lecteur de sécurité
| Dans | Peut |
| --- | --- |
| Identity Protection Center |Lire tous les rapports de sécurité et informations de paramètres pour les fonctionnalités de sécurité<ul><li>Anti-spam<li>Chiffrement<li>Prévention contre la perte de données<li>Anti-programme malveillant<li>Détection avancée des menaces<li>Anti-hameçonnage<li>Règles du flux de messagerie |
| Privileged Identity Management |<p>Dispose d’un accès en lecture seule à toutes les informations présentées dans Azure AD PIM : stratégies et rapports pour les attributions de rôle Azure AD, révisions de sécurité et prochainement accès en lecture aux données et rapports de stratégie pour les scénarios en plus de l’attribution de rôle Azure AD.<p>**Ne peut pas** s’inscrire auprès d’AD PIM ou y apporter des modifications. Dans le portail PIM ou via PowerShell, un membre ayant ce rôle peut activer des rôles supplémentaires (par exemple, un administrateur général ou un administrateur de rôle privilégié), s’il est éligible. |
| <p>Monitor Office 365 Service Health</p><p>Centre de sécurité et conformité Office 365</p> |<ul><li>Lire et gérer les alertes<li>Lire les stratégies de sécurité<li>Lire les informations sur les menaces, Cloud App Discovery et Mise en quarantaine dans Recherche et enquêtes<li>Lecture de tous les rapports |

### <a name="security-administrator"></a>Security Administrator
| Dans | Peut |
| --- | --- |
| Identity Protection Center |<ul><li>Toutes les autorisations du rôle lecteur de sécurité.<li>En outre, possibilité d’effectuer toutes les opérations IPC à l’exception de la réinitialisation des mots de passe. |
| Privileged Identity Management |<ul><li>Toutes les autorisations du rôle lecteur de sécurité.<li>**Ne peut pas** gérer les appartenances aux rôles Azure AD ou les paramètres. |
| <p>Monitor Office 365 Service Health</p><p>Centre de sécurité et conformité Office 365 |<ul><li>Toutes les autorisations du rôle lecteur de sécurité.<li>Peut configurer tous les paramètres de la fonctionnalité de protection avancée contre les menaces (protection contre les virus et logiciels malveillants, configuration des URL malveillantes, suivi des ’URL, etc.). |

## <a name="details-about-the-global-administrator-role"></a>Informations sur le rôle d’administrateur général
L’administrateur général a accès à toutes les fonctionnalités d’administration. Par défaut, le rôle d’administrateur général de l’annuaire est affecté à la personne qui souscrit un abonnement Azure. Seuls les administrateurs généraux peuvent affecter d’autres rôles d’administrateur.

## <a name="assign-or-remove-administrator-roles"></a>Attribution ou suppression de rôles d’administrateur
1. Dans le portail [Azure Classic](https://manage.windowsazure.com), cliquez sur **Active Directory**, puis sur le nom de l’annuaire de votre organisation.
2. Sur la page **Utilisateurs** , cliquez sur le nom d’affichage de l’utilisateur à modifier.
3. Dans la liste **Rôle organisationnel**, sélectionnez le rôle d’administrateur que vous souhaitez attribuer à cet utilisateur, ou sélectionnez **Utilisateur** pour supprimer un rôle d’administrateur existant.
4. Dans le champ **Autre adresse de messagerie**, tapez une adresse électronique. Cette adresse électronique est utilisée pour les notifications importantes, notamment la réinitialisation automatique de mot de passe. L’utilisateur doit donc être en mesure d’accéder à ce compte, qu’il ait ou non accès à Azure.
5. Sélectionnez **Autoriser** ou **Bloquer** pour indiquer si l’utilisateur est autorisé ou non à se connecter aux services et à y accéder.
6. Spécifiez un lieu en le sélectionnant dans la liste déroulante **Lieu d’utilisation** .
7. Une fois que vous avez terminé, cliquez sur **Enregistrer**.

## <a name="deprecated-roles"></a>Rôles déconseillés

Les rôles suivants ne doivent pas être utilisés. Ils sont déconseillés et seront prochainement supprimés d’Azure AD.

* Administrateur de licences ad hoc
* Créateur d’utilisateur vérifié par e-mail
* Jonction d’appareils
* Gestionnaires d’appareils
* Utilisateurs d’appareils
* Jonction d’appareils d’espace de travail

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la modification des administrateurs d’un abonnement Azure, consultez [Ajout ou modification de rôles d’administrateur Azure](../billing-add-change-azure-subscription-administrator.md)
* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](active-directory-understanding-resource-access.md)
* Pour plus d’informations sur l’association entre Azure Active Directory et votre abonnement Azure, consultez [Association des abonnements Azure avec Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gestion des utilisateurs](active-directory-create-users.md)
* [Gestion des mots de passe](active-directory-manage-passwords.md)
* [Gestion des groupes](active-directory-manage-groups.md)

