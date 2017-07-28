---
title: "Bien démarrer avec la gestion des licences dans Azure Active Directory | Microsoft Docs"
description: Fonctionnement de la gestion des licences Azure Active Directory, prise en main et bonnes pratiques
services: active-directory
keywords: Gestion des licences Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b62571e446aa0680c653d0a9d109207af26ad786
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017

---

# <a name="license-yourself-and-your-users-in-azure-active-directory"></a>Accorder une licence à vos utilisateurs et à vous-même dans Azure Active Directory

> [!div class="op_single_selector"]
> * [Instructions pour le portail Azure](active-directory-licensing-get-started-azure-portal.md)
> * [Obtenir des informations sur le portail Azure Classic](active-directory-licensing-what-is.md)
>
>

Azure Active Directory (Azure AD) est une plateforme et une solution Microsoft de type IDaaS (Identity as a Service). Azure AD est proposé en différentes éditions de service :

* Azure Active Directory Free, disponible avec n’importe quel service Microsoft tel qu’Office 365, Dynamics, Microsoft Intune ou Azure. Azure AD ne génère aucun frais de consommation dans ce mode.

* Éditions Azure AD payantes telles que :
  - Enterprise Mobility + Security 
  - Azure AD Premium (P1 et P2)
  - Azure AD Standard
  - Azure Multi-Factor Authentication

À l’instar de nombreux services en ligne Microsoft, la plupart des versions Azure AD payantes sont fournies par le biais de droits par utilisateur, comme dans Office 365, Microsoft Intune et Azure AD. Dans ces cas-là, l’achat de services est représenté par un ou plusieurs abonnements incluant chacun des licences prépayées dans votre locataire. Les droits par utilisateur sont obtenus des manières suivantes :

* Attribution d’une licence 
* Création d’un lien entre l’utilisateur et le produit
* Activation des composants de service pour l’utilisateur
* Utilisation de l’une des licences prépayées

[Essayez Azure AD Premium maintenant.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

Pour obtenir une présentation détaillée des fonctionnalités des services Azure AD, consultez [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md). Pour plus d’informations, consultez la page [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/).

> [!NOTE]
> Les abonnements Azure de type paiement à l’utilisation autorisent la création de ressources Azure et les mappent à votre mode de paiement. Aucun nombre de licences n’est associé à l’abonnement. Quand vous accordez à un utilisateur l’autorisation d’utiliser des ressources Azure mappées à l’abonnement, il est associé à cet abonnement et peut gérer ses ressources.

## <a name="how-does-azure-active-directory-licensing-work"></a>Comment la gestion des licences Azure Active Directory fonctionne-t-elle ?

Les services Azure AD basés sur des licences reposent sur l’activation d’un abonnement dans votre locataire de service Azure AD. Une fois l’abonnement actif, les fonctionnalités de service sont gérées par des administrateurs Azure et utilisées par des utilisateurs sous licence.

### <a name="manage-subscription-information"></a>Gérer les informations d’abonnement

Quand vous achetez Enterprise Mobility + Security, Azure AD Premium ou Azure AD Basic, votre locataire est mis à jour avec l’abonnement, notamment sa période de validité et les licences prépayées. Les informations de votre abonnement, notamment le nombre de licences attribuées ou disponibles, sont accessibles par l’intermédiaire du portail Azure : sous **Azure Active Directory**, ouvrez la vignette **Licences** de l’annuaire en question. Le panneau **Licences** constitue également l’emplacement idéal pour gérer vos attributions de licence.

Chaque abonnement se compose d’un ou plusieurs plans de service, tels qu’Azure AD, Multi-Factor Authentication, Intune, Exchange Online ou SharePoint Online.  La gestion des licences Azure AD ne nécessite *aucune* gestion au niveau des plans de service. Office 365 diffère, car il s’appuie sur ce mode de configuration avancée pour gérer l’accès aux services inclus. Azure AD repose sur la configuration intégrée au service pour activer les fonctionnalités et gérer les différentes autorisations.

> [!IMPORTANT]
> Les abonnements Azure AD Premium, Azure AD Basic et Enterprise Mobility + Security sont confinés à leur annuaire/locataire alloué. Les abonnements ne peuvent pas être fractionnés entre plusieurs annuaires, ni utilisés pour habiliter des utilisateurs à exploiter d’autres annuaires. Le déplacement d’un abonnement entre annuaires est possible, mais nécessite l’envoi d’un ticket de support ou une annulation et un rachat pour les achats directs.
>
> En cas d’achat d’Azure AD ou Enterprise Mobility + Security par l’intermédiaire d’un abonnement de licence en volume, et quand l’abonnement comprend d’autres services Microsoft Online (par exemple Office 365), l’activation a lieu automatiquement. 

### <a name="assign-licenses"></a>Attribuer des licences

Bien qu’il suffise d’obtenir un abonnement pour configurer les fonctionnalités payantes, vous devez quand même distribuer des licences pour les fonctionnalités Azure AD payantes aux utilisateurs. Une licence doit être attribuée à tout utilisateur ayant besoin d’accéder à une fonctionnalité Azure AD payante ou géré par le biais de ce type de fonctionnalité. L’attribution de licence est un mappage entre un utilisateur et un service acheté, comme Azure AD Premium, Azure AD Basic ou Enterprise Mobility + Security.


La gestion des utilisateurs de votre annuaire qui doivent disposer d’une licence nécessite les étapes suivantes : 

* Attribution de licences à des groupes dans le [portail Azure](active-directory-licensing-whatis-azure-portal.md)
* Attribution de licences directement aux utilisateurs par l’intermédiaire du portail, de PowerShell ou des API 

Quand vous attribuez des licences à un groupe, tous les membres de ce groupe disposent d’une licence. Si des utilisateurs sont ajoutés au groupe ou en sont supprimés, la licence appropriée leur est attribuée ou retirée. L’attribution à un groupe peut utiliser toute gestion de groupe qui vous est accessible, et est cohérente avec l’attribution par groupe aux applications.

Vous pouvez utiliser l’[attribution de licence basée sur le groupe](active-directory-licensing-whatis-azure-portal.md) pour configurer des règles telles que les suivantes :
* Tous les utilisateurs de votre annuaire obtiennent automatiquement une licence
* Toute personne avec la fonction appropriée obtient une licence
* Vous pouvez déléguer la décision à d’autres responsables au sein de l’organisation (à l’aide de [groupes en libre-service](active-directory-accessmanagement-self-service-group-management.md))

Pour obtenir une discussion détaillée de l’attribution des licences à des groupes, avec notamment des scénarios avancés et des scénarios d’attribution de licences Office 365, consultez [Affecter des licences à un groupe d’utilisateurs dans Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md).

## <a name="get-started-with-azure-ad-licensing"></a>Bien démarrer avec la gestion des licences Azure AD

La prise en main d’Azure AD est simple. Vous pouvez toujours créer votre annuaire dans le cadre de l’inscription à une [évaluation gratuite d’Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).

Les bonnes pratiques suivantes peuvent vous aider à garantir que votre locataire est aligné avec d’autres services Microsoft que vous pouvez consommer et avec vos objectifs pour le service :

- Si vous utilisez déjà l’un des services d’organisation de Microsoft, vous disposez déjà d’un locataire Azure AD. Il est préférable de continuer à utiliser le même locataire pour les autres services, afin que la gestion des identités principales, notamment l’approvisionnement et l’authentification unique hybride, soit utilisable parmi l’ensemble des services. Avec l’authentification unique, vos utilisateurs bénéficient de fonctionnalités enrichies dans tous les services. Ainsi, si vous décidez d’acheter un service Azure AD payant pour votre personnel, nous vous recommandons d’utiliser le même locataire cette opération.

- Nous vous recommandons d’utiliser un nouveau locataire dans le portail Azure si vous envisagez :
  - D’utiliser Azure AD pour un ensemble d’utilisateurs différent (par exemple des partenaires ou des clients)
  - D’évaluer les services Azure AD de manière isolée par rapport à votre service de production
  - De configurer un environnement de bac à sable pour vos services

  Ce nouvel annuaire est créé avec votre compte en tant qu’utilisateur externe avec des autorisations d’administrateur général. Quand vous vous connecterez au portail Azure avec ce compte, vous pourrez voir ce locataire et accéder à toutes les tâches d’administration.

> [!NOTE]
> Azure AD prend en charge les « utilisateurs invités », qui sont des comptes d’utilisateur d’un locataire Azure AD qui ont été créés par le biais d’un compte Microsoft ou d’une identité Azure AD d’un autre locataire. Actuellement, le portail d’administration Office 365 ne prend pas en charge ces utilisateurs. Les utilisateurs invités avec des comptes Microsoft ne peuvent pas accéder au portail d’administration Office 365, tandis que les utilisateurs invités d’autres locataires Azure AD sont ignorés. Dans ce dernier cas, seul le compte local de l’utilisateur, dans le locataire Azure AD ou Office 365 où l’utilisateur a été créé initialement, est accessible.

### <a name="select-one-or-more-license-trials"></a>Sélectionner une ou plusieurs versions d’évaluation avec licences

Vous pouvez activer un abonnement d’évaluation à Azure AD Premium ou à Enterprise Mobility + Security sous **Azure Active Directory** &gt; **Démarrage rapide**.

![Sélectionner une licence d’évaluation](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

Les licences d’évaluation sont disponibles dans le panneau **Licences**.

### <a name="assign-licenses-to-users-and-groups"></a>Attribuer des licences aux utilisateurs et groupes

Une fois l’abonnement actif, vous devez vous attribuer une licence. Ensuite, actualisez le navigateur pour être sûr de voir toutes les fonctionnalités. L’étape suivante consiste à attribuer des licences aux utilisateurs qui doivent accéder aux fonctionnalités Azure AD payantes. Comme décrit dans [Attribuer des licences](#assign-licenses), l’une des manières les plus simples d’attribuer des licences consiste à identifier le groupe qui représente l’audience souhaitée et à lui affecter la licence. Ainsi, quand vous ajoutez ou supprimez un utilisateur du groupe pendant son cycle de vie, vous lui affectez ou lui retirez la licence.

> [!NOTE]
> Certains services Microsoft ne sont pas disponibles dans tous les emplacements. Avant de pouvoir attribuer une licence à un utilisateur, l’administrateur doit spécifier la propriété **Emplacement d’utilisation** pour l’utilisateur. Vous pouvez définir cette propriété sous **Utilisateur** &gt; **Profil** &gt; **Paramètres** dans le portail Azure. Quand vous attribuez une licence à un groupe, tout utilisateur dont l’emplacement d’utilisation n’est pas spécifié hérite de l’emplacement de l’annuaire.

Pour attribuer une licence, sous **Azure Active Directory** &gt; **Licences** &gt; **Tous les produits**, sélectionnez un ou plusieurs produits, puis sélectionnez **Affecter** dans la barre de commandes.

![Sélectionner une licence à attribuer](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

Vous pouvez utiliser le panneau **Utilisateurs et groupes** pour sélectionner plusieurs utilisateurs ou groupes ou pour désactiver des plans de service dans le produit. Utilisez la zone de recherche en haut pour rechercher des noms d’utilisateurs et de groupes.

![Sélectionner un utilisateur ou un groupe pour l’attribution de licences](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

Quand vous attribuez une licence au groupe, un certain temps peut être nécessaire pour que tous les utilisateurs héritent de la licence, en fonction du nombre d’utilisateurs dans le groupe. Vous pouvez suivre l’avancement du traitement dans le panneau **Groupe**, sous la vignette **Licences**.

![Statut d’affectation de licence](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

Des erreurs d’affectation peuvent se produire lors de l’affectation des licences Azure AD, mais elles restent relativement rares lors de la gestion des produits Azure AD et Enterprise Mobility + Security. Les erreurs d’attribution se limitent aux problèmes suivants :
- Conflit d’attribution : lorsqu’un utilisateur a précédemment reçu une licence incompatible avec la licence actuelle. Dans ce cas, l’affectation de la nouvelle licence nécessite la suppression de la licence active.
- Dépassement du nombre de licences disponibles : quand le nombre d’utilisateurs des groupes attribués dépasse le nombre de licences disponibles, le statut d’affectation d’un utilisateur reflète l’échec de l’affectation du fait de licences manquantes.

#### <a name="azure-ad-b2b-collaboration-licensing"></a>Affectation de licences Azure Active Directory B2B Collaboration

La collaboration B2B vous permet d’inviter des utilisateurs invités dans votre locataire Azure AD pour fournir l’accès à des services Azure AD et à toutes ressources Azure que vous mettez à disposition.  

L’invitation d’utilisateurs B2B et leur attribution à une application dans Azure AD n’entraînent aucun frais. Jusqu’à 10 applications par utilisateur invité et trois rapports de base sont également gratuits pour les utilisateurs B2B Collaboration. Si des licences appropriées sont affectées à votre utilisateur dans le locataire Azure AD du partenaire, il disposera également d’une licence dans votre locataire.

Cela n’est pas obligatoire, mais si vous souhaitez fournir l’accès à des fonctionnalités Azure AD payantes, vous devez accorder à ces utilisateurs invités B2B des licences Azure AD appropriées. Un locataire invitant avec une licence Azure payée peut accorder des droits d’utilisateur B2B Collaboration à cinq utilisateurs invités supplémentaires. Pour plus d’informations et pour voir des scénarios, consultez le [Guide d’attribution de licences pour Azure Active Directory B2B Collaboration](active-directory-b2b-licensing.md).

### <a name="view-assigned-licenses"></a>Visualiser les licences attribuées

Un récapitulatif des licences attribuées et disponibles est disponible sous **Azure Active Directory** &gt; **Licences** &gt; **Tous les produits**.

![Afficher le récapitulatif des licences](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

Une liste détaillée des utilisateurs et groupes attribués est disponible quand vous sélectionnez un produit spécifique. La liste **Utilisateurs sous licence** affiche tous les utilisateurs consommant actuellement une licence et indique si la licence a été attribuée directement à l’utilisateur ou si elle est héritée d’un groupe.

![Afficher le détail des licences](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

De même, la liste **Groupes sous licence** montre tous les groupes auxquels des licences ont été attribuées. Sélectionnez un utilisateur ou un groupe pour ouvrir le panneau **Licences**, qui montre toutes les licences attribuées à cet objet.

### <a name="remove-a-license"></a>Supprimer une licence

Pour supprimer une licence, accédez à l’utilisateur ou au groupe et ouvrez la vignette **Licences**. Sélectionnez la licence, puis cliquez sur **Supprimer**.

![Supprimer une licence](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

Les licences héritées par l’utilisateur à partir d’un groupe ne peuvent pas être supprimées directement. Au lieu de cela, supprimez l’utilisateur du groupe à partir duquel il a hérité de la licence.

### <a name="extend-trials"></a>Prolonger des évaluations

Des prolongations d’évaluation pour les clients sont disponibles grâce à une inscription libre-service par le biais du portail Office 365. Un administrateur de client peut accéder au portail Office (l’accès dépend des autorisations relatives au portail Office) et sélectionner la version d’évaluation d’Azure AD Premium. En cliquant sur le lien **Étendre la période d’évaluation**, le processus d’extension démarre. Une carte de crédit est nécessaire, mais elle n’est pas débitée.

![Prolonger une évaluation dans le portail Azure](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les scénarios avancés de gestion des licences par l’intermédiaire des groupes, consultez l’article [Affecter des licences à un groupe](active-directory-licensing-group-assignment-azure-portal.md).

Voici des informations sur la manière de configurer et d’utiliser d’autres fonctionnalités payantes d’Azure AD :

* [Réinitialisation de mot de passe en libre service](active-directory-manage-passwords.md)
* [Gestion des groupes en libre service](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* [Achat direct de licences Azure AD Premium](http://aka.ms/buyaadp)

