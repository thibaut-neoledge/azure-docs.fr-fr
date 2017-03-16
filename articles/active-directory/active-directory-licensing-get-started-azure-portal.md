---

title: "Prise en main de la gestion des licences dans la version préliminaire d’Azure Active Directory | Microsoft Docs"
description: "Description de la gestion des licences Azure Active Directory, de son mode de fonctionnement, de sa mise en route et des meilleures pratiques associées concernant les éditions Office 365, Microsoft Intune et Azure Active Directory Premium et Basic"
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
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 09f0ed3f7624bb242c40868710fb3eae49cda906
ms.openlocfilehash: a798de49ed08eedaf27267c6bac12f8b0b64739f
ms.lasthandoff: 03/01/2017


---

# <a name="license-yourself-and-your-users-in-azure-active-directory-preview"></a>Accorder une licence à vos utilisateurs et à vous-même dans la version préliminaire d’Azure Active Directory

> [!div class="op_single_selector"]
> * [portail Azure](active-directory-licensing-get-started-azure-portal.md)
> * [portail Azure Classic](active-directory-licensing-what-is.md)
>
>

Azure Active Directory (Azure AD) est une plateforme et une solution Microsoft de type IDaaS (Identity as a Service). Azure AD est proposé en différentes versions fonctionnelles et techniques, depuis la version Azure AD gratuite, disponible avec n’importe quel service Microsoft tel qu’Office 365, Dynamics, Microsoft Intune et Azure (Azure AD ne génère aucun frais de consommation dans ce mode), jusqu’aux versions Azure AD payantes comme Enterprise Mobility Suite (EMS), Azure AD Premium (P1 et P2) et Azure AD Standard, ainsi qu’Azure Multi-Factor Authentication (MFA). À l’instar de nombreux services en ligne Microsoft, la plupart des versions Azure AD payantes sont fournies par le biais de droits par utilisateur, comme dans Office 365, Microsoft Intune et Azure AD. Dans ces cas-là, l’achat de services est représenté par un ou plusieurs abonnements incluant chacun un certain nombre de licences prépayées dans votre client. Les droits par utilisateur sont obtenus par l’attribution de licences, entraînant ainsi la création d’un lien entre l’utilisateur et le produit, l’activation des composants de service pour l’utilisateur et la consommation de l’une des licences prépayées.

[Essayez Azure AD Premium maintenant.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

Pour découvrir une présentation détaillée des fonctionnalités des services Azure AD, consultez l’article [Qu’est-ce qu’Azure Active Directory ?](https://azure.microsoft.com/en-us/documentation/articles/active-directory-whatis/). Pour plus d’informations, consultez la page Contrats de niveau de service.

> [!NOTE]
> Les abonnements Azure de type paiement à l’utilisation sont différents : bien qu’ils soient également représentés dans votre annuaire, ces abonnements activent la création de ressources Azure et les mappent sur votre mode de paiement. Dans ce cas, AUCUN nombre de licences n’est associé à l’abonnement. L’association d’utilisateurs à l’abonnement, qui définit l’accès des utilisateurs à la gestion des ressources d’abonnement, s’effectue en leur octroyant des autorisations de manipulation des ressources Azure mappées sur l’abonnement.

## <a name="how-does-azure-ad-licensing-work"></a>Comment la gestion des licences Azure AD fonctionne-t-elle ?

Les services Azure AD basés sur des licences reposent sur l’activation d’un abonnement dans votre annuaire/client de service Azure AD. Une fois que l’abonnement est actif, les fonctionnalités de service peuvent être gérées par les administrateurs d’annuaire/de service et utilisées par les utilisateurs sous licence.

Lorsque vous achetez ou activez Enterprise Mobility Suite, Azure AD Premium ou Azure AD Standard, votre annuaire est mis à jour avec l’abonnement, y compris sa période de validité et les licences prépayées. Les informations de votre abonnement, notamment le nombre de licences attribuées ou disponibles, sont accessibles par l’intermédiaire du portail Azure sous la vignette Azure Active Directory &gt; **Licences** du répertoire considéré. Cette vignette constitue également l’emplacement idéal pour gérer vos attributions de licence.

Chaque abonnement se compose d’un ou de plusieurs plans de services, mappant chacun le niveau fonctionnel inclus du type de service ; par exemple, Azure AD, Azure MFA, Microsoft Intune, Exchange Online ou SharePoint Online.  La gestion des licences Azure AD ne nécessite AUCUNE gestion au niveau des plans de services. Cette approche diffère de celle d’Office 365, qui s’appuie sur ce mode de configuration avancée pour gérer l’accès aux services inclus. Azure AD repose sur la configuration des services pour activer les fonctionnalités et gérer les autorisations individuelles.

> [!IMPORTANT]
> Les abonnements Azure AD Premium et Standard, ainsi que les abonnements Enterprise Mobility Suite, sont confinés à leur annuaire/client alloué. Les abonnements ne peuvent pas être fractionnés entre plusieurs annuaires, ni utilisés pour habiliter des utilisateurs à exploiter d’autres annuaires. Le déplacement d’un abonnement entre annuaires est possible, mais nécessite l’envoi d’un ticket de support ou une annulation et un rachat dans le cas des achats directs.
>
> Lors de l’achat d’Azure AD ou Enterprise Mobility Suite via la licence en volume, l’activation de l’abonnement se produit automatiquement lorsque l’accord comprend d’autres services Microsoft Online, comme Office 365.

### <a name="assigning-licenses"></a>Attribution de licences

Alors que la configuration des fonctionnalités payantes ne nécessite rien d’autre qu’un abonnement, l’utilisation des fonctionnalités Azure AD payantes requiert la distribution de licences aux personnes adéquates. En général, une licence doit être attribuée à tout utilisateur ayant besoin d’accéder à une fonctionnalité Azure AD payante ou géré par le biais de ce type de fonctionnalité. L’attribution d’une licence est un mappage entre un utilisateur et un service acheté, comme Azure AD Premium, Azure AD Standard ou Enterprise Mobility Suite.

La gestion des utilisateurs de votre annuaire qui doivent disposer d’une licence est simple. Vous pouvez assurer ce processus en attribuant des licences à des groupes dans le portail Azure, ou en attribuant les licences directement aux utilisateurs adéquats par l’intermédiaire du portail, de PowerShell ou d’API. Lorsque vous attribuez des licences à un groupe, tous les membres de ce groupe disposent alors d’une licence. Si des utilisateurs sont ajoutés au groupe ou en sont supprimés, la licence appropriée leur est également attribuée ou retirée. L’attribution à un groupe peut utiliser toute gestion de groupe qui vous est accessible, et est cohérente avec l’attribution par groupe aux applications. Grâce à cette approche, vous pouvez configurer des règles telles que l’attribution automatique à tous les utilisateurs de votre annuaire, vous assurer que toute personne occupant la fonction appropriée dispose bien d’une licence, ou même déléguer la décision à d’autres gestionnaires de l’organisation. Pour une présentation détaillée de l’attribution de licences à des groupes, avec notamment des scénarios avancés et des scénarios d’attribution de licences Office 365, consultez cet article.

## <a name="getting-started-with-azure-ad-licensing"></a>Prise en main de la gestion des licences Azure AD

La prise en main d’Azure AD est d’une grande simplicité ; vous pouvez toujours créer votre annuaire dans le cadre de votre inscription à la version d’évaluation gratuite d’Azure. [En savoir plus sur l’inscription en tant qu’organisation](https://azure.microsoft.com/en-us/documentation/articles/sign-up-organization/). Les informations ci-après peuvent vous aider à vérifier que votre annuaire s’harmonise au mieux avec d’autres services Microsoft que vous consommez ou envisagez de consommer, ainsi qu’avec vos objectifs en obtenant le service.

Voici quelques meilleures pratiques :

- Si vous utilisez déjà l’un des services d’organisation de Microsoft, vous disposez déjà d’un annuaire Azure AD. Dans ce cas, vous devez continuer à utiliser le même annuaire pour les autres services, afin que la gestion des identités principales, y compris l’approvisionnement et l’authentification unique hybride, soit utilisable dans l’ensemble des services. Vos utilisateurs bénéficieront alors d’une expérience d’authentification unique et de fonctionnalités enrichies dans tous les services. Par conséquent, si vous décidez d’acheter un service Azure AD payant pour vos ressources, nous vous recommandons d’utiliser le même annuaire pour effectuer cette opération.

- Si vous prévoyez d’utiliser Azure AD pour un autre groupe d’utilisateurs (partenaires, clients, etc.), que vous souhaitez évaluer des services Azure AD indépendamment de votre service de production, ou que vous cherchez à configurer un environnement de bac à sable (sandbox) pour vos services, nous vous recommandons de commencer par créer un autre annuaire par le biais du portail Azure Classic. En savoir plus sur la création d’un nouveau répertoire Azure AD dans le portail Azure Classic. Ce nouvel annuaire sera créé avec votre compte en tant qu’utilisateur externe avec des autorisations d’administrateur globales. Lorsque vous vous connecterez au portail Azure avec ce compte, vous pourrez voir ce répertoire et accéder à toutes les tâches d’administration de répertoire.

> [!NOTE]
> Azure AD prend en charge les « utilisateurs externes », qui correspondent aux comptes d’utilisateurs d’une instance Azure AD qui ont été créés à l’aide d’un compte Microsoft (MSA) ou d’une identité Azure AD à partir d’un autre annuaire. Bien que nous ayons entrepris d’étendre cette fonctionnalité à tous les services d’organisation Microsoft, ces comptes ne sont actuellement pas pris en charge dans les expériences de certains services ; par exemple, le portail d’administration d’Office 365 ne prend pas en charge ces utilisateurs pour l’instant. En conséquence, les utilisateurs externes dotés de comptes Microsoft ne seront pas en mesure d’accéder au portail d’administration d’Office 365, tandis que les utilisateurs externes d’autres annuaires Azure AD seront ignorés. Dans ce dernier cas, seuls le compte local de l’utilisateur, l’annuaire Azure AD ou l’annuaire Office 365 dans lesquels l’utilisateur a été créé, seront accessibles par le biais de ces expériences.

Comme indiqué précédemment, Azure AD existe en différentes versions payantes. Ces versions présentent de légères différences sur le plan de leur disponibilité d’achat :

|  Produit       |              Contrat Entreprise/Licence en volume  | Ouverts  | CSP |  Droits d’utilisation MPN  | Achat direct |  Version d’évaluation |
|  -------------- | ------------- | ------- | ------ | ----- | ---------------- | ----------------- | ------- |
|  Enterprise Mobility Suite  | X   |    X  |    X  |     X | &nbsp;  | X |
|  Azure AD Premium P2     |    X    |   X   |   X   | &nbsp;  |  X  |   X  
|  Azure AD Premium P1     |    X   |    X    |  X   |  &nbsp; |  X  |  &nbsp; |             
|  Azure AD Standard          |    X   |    X   |  X  |  X  | &nbsp; | &nbsp;  |

### <a name="select-one-or-more-license-trials"></a>Sélectionner une ou plusieurs versions d’évaluation avec licences

Vous pouvez activer un abonnement d’évaluation à Azure AD Premium ou à Enterprise Mobility Suite sous Azure Active Directory &gt; **Démarrage rapide**.

![sélectionner une licence d’évaluation](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

Les licences d’évaluation seront désormais disponibles sur le panneau **Licences**.

### <a name="assign-licenses-to-users-and-groups"></a>Attribuer des licences aux utilisateurs et groupes

Une fois l’inscription active, vous devez vous attribuer une licence et actualiser le navigateur pour être en mesure de visualiser toutes les fonctionnalités. L’étape suivante consiste à attribuer des licences aux utilisateurs qui devront accéder aux fonctionnalités Azure AD payantes ou être inclus dans ces dernières. Comme nous l’avons indiqué plus haut à la section [Attribution de licences](#assigning-licenses), le meilleur moyen de procéder consiste à identifier le groupe représentant l’audience souhaitée et à lui attribuer la licence ; de cette façon, si des utilisateurs sont ajoutés au groupe ou en sont supprimés pendant son cycle de vie, la licence leur sera également attribuée ou retirée.

> [!NOTE]
> Certains services Microsoft ne sont pas disponibles partout ; avant d’attribuer une licence à un utilisateur, l’administrateur doit spécifier la propriété « Emplacement d’utilisation » pour l’utilisateur. Pour ce faire, il doit se rendre à la section Utilisateur &gt; Profil &gt; Paramètres dans le portail Azure. Lorsque vous attribuez une licence à un groupe, tous les utilisateurs sans emplacement d’utilisation spécifié hériteront de l’emplacement du répertoire.

Pour attribuer une licence à un groupe ou à des utilisateurs individuels, sous Azure Active Directory &gt; Licences &gt; Tous les produits, sélectionnez un ou plusieurs produits et cliquez sur le bouton **Attribuer** de la barre de commandes.

![sélectionner une licence à attribuer](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

Cela fera apparaître un nouveau panneau où vous pourrez choisir plusieurs utilisateurs ou groupes, et éventuellement désactiver les plans de service dans le produit. La barre de recherche en haut peut être utilisée pour rechercher des utilisateurs et des groupes par nom.

![sélectionner un utilisateur ou un groupe pour l’attribution de licences](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

Lorsque vous attribuez une licence au groupe, un certain temps peut être nécessaire pour que tous les utilisateurs héritent de la licence, en fonction du nombre d’utilisateurs dans le groupe. L’avancement du traitement peut être suivi dans le panneau du groupe, sous la vignette **Licences**.

![statut d’attribution de licence](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

Lors de l’attribution des licences Azure AD, des erreurs d’attribution peuvent se produire, mais restent relativement rares lors de la gestion de produits Azure AD et EMS. Les erreurs d’attribution se limitent aux problèmes suivants :
- Conflit d’attribution : lorsqu’un utilisateur a précédemment reçu une licence incompatible avec la licence actuelle. Dans ce cas, l’attribution de la nouvelle licence nécessitera la suppression de la licence actuelle.
- Dépassement du nombre de licences disponibles : lorsque le nombre d’utilisateurs des groupes attribués dépasse le nombre de licences disponibles, l’état d’attribution des utilisateurs reflétera l’échec de l’attribution du fait de licences manquantes.

Des informations détaillées sur l’attribution d’une licence à un groupe sont disponibles dans cet article.

### <a name="view-assigned-licenses"></a>Visualiser les licences attribuées

Un résumé des licences attribuées et disponibles est disponible sous Azure Active Directory &gt; **Licences** &gt; **Tous les produits**.

![afficher le résumé des licences](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

Une liste détaillée des utilisateurs et groupes attribués est disponible en cliquant sur un produit spécifique. **Utilisateurs sous licence** affiche tous les utilisateurs consommant actuellement une licence et indique si la licence a été attribuée directement à l’utilisateur ou si elle est héritée d’un groupe.

![afficher le détail des licences](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

De même, **Groupes sous licence** affiche tous les groupes auxquels des licences ont été attribuées. Cliquez sur l’utilisateur ou sur le groupe dans ces vues pour ouvrir le panneau **Licences**, dans lequel figurent toutes les licences attribuées à l’objet concerné.

### <a name="removing-a-license"></a>Suppression d’une licence

Pour supprimer une licence, accédez à l’utilisateur ou au groupe et ouvrez la vignette **Licences**. Sélectionnez la licence, puis cliquez sur **Supprimer**.

![supprimer une licence](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

Veuillez noter que les licences héritées par l’utilisateur à partir d’un groupe ne peuvent pas être supprimées directement. Au lieu de cela, supprimez l’utilisateur du groupe à partir duquel il a hérité de la licence.

### <a name="extending-trials"></a>Extension des versions d’évaluation

Des extensions de version d’évaluation pour les clients sont disponibles en libre service par le biais du portail Office 365. Un administrateur de client peut accéder au portail Office (l’accès dépend des autorisations relatives au portail Office) et sélectionner la version d’évaluation d’Azure AD Premium. En cliquant sur le lien **Étendre la période d’évaluation**, le processus d’extension démarre. Une carte de crédit est requise, mais elle ne sera pas débitée.

![prolonger une évaluation dans le portail Azure](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les scénarios avancés de gestion des licences via les groupes, lisez cet article 

Vous voilà prêt à configurer et à utiliser certaines fonctionnalités d’Azure AD Premium.

* [Réinitialisation de mot de passe en libre service](active-directory-manage-passwords.md)
* [Gestion des groupes en libre service](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Attribution de groupe aux applications](active-directory-manage-groups.md)
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* [Achat direct de licences Azure AD Premium](http://aka.ms/buyaadp)

