<properties
   pageTitle="Qu’est-ce que la gestion des licences Microsoft Azure Active Directory ? | Microsoft Azure"
	description="Description de la gestion des licences Microsoft Azure AD, de son mode de fonctionnement, de sa mise en route et des meilleures pratiques associées concernant les éditions Office 365, Microsoft Intune et Azure Active Directory Premium et Standard"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="swadhwa"
	editor=""/>

<tags
   ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="07/16/2015"
	ms.author="curtand"/>

# Qu’est-ce que la gestion des licences Microsoft Azure Active Directory ?

##Description
Azure Active Directory (AD) est une plateforme et une solution Microsoft de type IDaaS (Identity as a Service). Azure AD est proposé en différentes versions fonctionnelles et techniques, depuis la version Azure AD gratuite, disponible avec n’importe quel service Microsoft tel qu’Office 365, Dynamics, Microsoft Intune et Azure (Azure AD ne génère aucun frais de consommation dans ce mode), jusqu’aux versions Azure AD payantes comme Enterprise Mobility Suite (EMS), Azure AD Premium et Standard, ainsi qu’Azure Multi-Factor Authentication (MFA). À l’instar de nombreux services en ligne Microsoft, la plupart des versions Azure AD payantes sont fournies par le biais de droits par utilisateur, comme dans Office 365, Microsoft Intune et Azure AD. Dans ces cas-là, l’achat de services est représenté par un ou plusieurs abonnements incluant chacun un certain nombre de licences prépayées dans votre client. Les droits par utilisateur sont obtenus par l’attribution de licences, entraînant ainsi la création d’un lien entre l’utilisateur et le produit, l’activation des composants de service pour l’utilisateur et la consommation de l’une des licences prépayées.

[Essayez Azure AD Premium maintenant.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

> [AZURE.NOTE]Le portail d’administration d’Azure AD fait partie intégrante du Portail de gestion Azure. Alors que l’utilisation d’Azure AD ne nécessite pas l’achat d’Azure, l’accès à ce portail requiert un abonnement Azure actif ou un [abonnement à la version d’évaluation d’Azure](http://azure.microsoft.com/pricing/free-trial/).

Pour découvrir une présentation détaillée des fonctionnalités des services Azure AD, consultez l’article [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md). [En savoir plus sur les niveaux de service Azure AD](http://azure.microsoft.com/support/legal/sla/)

> [AZURE.NOTE]Les abonnements Azure de type paiement à l’utilisation sont différents : bien qu’ils soient également représentés dans votre annuaire, ces abonnements activent la création de ressources Azure et les mappent sur votre mode de paiement. Dans ce cas, AUCUN nombre de licences n’est associé à l’abonnement. L’association d’utilisateurs à l’abonnement, qui définit l’accès des utilisateurs à la gestion des ressources d’abonnement, s’effectue en leur octroyant des autorisations de manipulation des ressources Azure mappées sur l’abonnement.


##Comment la gestion des licences Azure AD fonctionne-t-elle ?

Les services Azure AD basés sur des licences (sur des droits) reposent sur l’activation d’un abonnement dans votre annuaire/client de service Azure AD. Une fois que l’abonnement est actif, les fonctionnalités de service peuvent être gérées par les administrateurs d’annuaire/de service et utilisées par les utilisateurs sous licence.

Lorsque vous achetez ou activez Enterprise Mobility Suite, Azure AD Premium ou Azure AD Standard, votre annuaire est mis à jour avec l’abonnement, y compris sa période de validité et les licences prépayées. Les informations de votre abonnement, notamment l’état, les événements de cycle de vie suivants et le nombre de licences attribuées ou disponibles, sont accessibles par l’intermédiaire du Portail de gestion Azure AD sous l’onglet Licences de l’annuaire considéré. Cet onglet constitue également l’emplacement idéal pour gérer vos attributions de licence.

Chaque abonnement se compose d’un ou de plusieurs plans de services, mappant chacun le niveau fonctionnel inclus du type de service ; par exemple, Azure AD, Azure MFA, Microsoft Intune, Exchange Online ou SharePoint Online. La gestion des licences Azure AD ne nécessite AUCUNE gestion au niveau des plans de services. Cette approche diffère de celle d’Office 365, qui s’appuie sur ce mode de configuration avancée pour gérer l’accès aux services inclus. Azure AD repose sur la configuration des services pour activer les fonctionnalités et gérer les autorisations individuelles.

En général, les informations d’abonnement Azure AD sont gérées par l’intermédiaire du Portail de gestion Azure, sous l’onglet Licences de l’annuaire considéré. À l’exception d’Azure AD Premium, les abonnements Azure AD n’apparaissent PAS dans le portail Office.

> [AZURE.IMPORTANT]Les abonnements Azure AD Premium et Standard, ainsi que les abonnements Enterprise Mobility Suite, sont confinés à leur annuaire/client alloué. Les abonnements ne peuvent pas être fractionnés entre plusieurs annuaires, ni utilisés pour habiliter des utilisateurs à exploiter d’autres annuaires. Le déplacement d’un abonnement entre annuaires est possible, mais nécessite l’envoi d’un ticket de support ou une annulation et un rachat dans le cas des achats directs.

> [AZURE.IMPORTANT]Lors de l'achat d'Azure AD ou Enterprise Mobility Suite via la licence en volume, l’activation de l'abonnement se produit automatiquement lorsque l'accord comprend d’autres services Microsoft Online, comme Office 365.

Les fonctionnalités Azure AD payantes couvrent l’étendue de l’annuaire. Exemples : - Fonctionnalité d’attribution par groupe aux applications, activée sous l’application que vous gérez. - Fonctionnalités de gestion de groupe avancées et en libre service, disponibles sous la configuration de l’annuaire ou au sein du groupe concerné. - Rapports de sécurité Premium, accessibles dans l’onglet Génération de rapports. - Découverte des applications cloud, accessible dans le portail Azure en version préliminaire sous Identité.

###Attribution de licences
Alors que la configuration des fonctionnalités payantes ne nécessite rien d’autre qu’un abonnement, l’utilisation des fonctionnalités Azure AD payantes requiert la distribution de licences aux personnes adéquates. En général, une licence doit être attribuée à tout utilisateur ayant besoin d’accéder à une fonctionnalité Azure AD payante ou géré par le biais de ce type de fonctionnalité. L’attribution d’une licence est un mappage entre un utilisateur et un service acheté, comme Azure AD Premium, Azure AD Standard ou Enterprise Mobility Suite.

La gestion des utilisateurs de votre annuaire qui doivent disposer d’une licence est simple. Vous pouvez assurer ce processus en attribuant des licences à un groupe pour créer des règles d’attribution par le biais du Portail de gestion Azure AD, ou en attribuant les licences directement aux utilisateurs adéquats par l’intermédiaire du portail, de PowerShell ou d’API. Lorsque vous attribuez des licences à un groupe, tous les membres de ce groupe disposent alors d’une licence. Si des utilisateurs sont ajoutés au groupe ou en sont supprimés, la licence appropriée leur est également attribuée ou retirée. L’attribution à un groupe peut utiliser toute gestion de groupe qui vous est accessible, et est cohérente avec l’attribution par groupe aux applications. Grâce à cette approche, vous pouvez configurer des règles telles que l’attribution automatique à tous les utilisateurs de votre annuaire, vous assurer que toute personne occupant la fonction appropriée dispose bien d’une licence, ou même déléguer la décision à d’autres gestionnaires de l’organisation.

Avec l’attribution de licences par groupe, tout utilisateur non associé à un emplacement d’utilisation héritera de l’emplacement de l’annuaire lors de l’attribution. Cet emplacement est modifiable à tout moment par l’administrateur. Dans les cas où l’attribution automatisée échoue en raison d’une erreur, les informations de l’utilisateur sous ce type de licence indiqueront cet état.

##Prise en main de la gestion des licences Azure AD

La prise en main d’Azure AD est d’une grande simplicité ; vous pouvez toujours créer votre annuaire dans le cadre de votre inscription à la version d’évaluation gratuite d’Azure. [En savoir plus sur l’inscription en tant qu’organisation](sign-up-organization.md). Les informations ci-après peuvent vous aider à vérifier que votre annuaire s’harmonise au mieux avec d’autres services Microsoft que vous consommez ou envisagez de consommer, ainsi qu’avec vos objectifs en obtenant le service.

Voici quelques bonnes pratiques : - Si vous utilisez déjà l’un des services d’organisation de Microsoft, vous disposez déjà d’un annuaire Azure AD. Dans ce cas, vous devez continuer à utiliser le même annuaire pour les autres services, afin que la gestion des identités principales, y compris l’approvisionnement et l’authentification unique hybride, soit utilisable dans l’ensemble des services. Vos utilisateurs bénéficieront alors d’une expérience d’ouverture de session unique et de fonctionnalités enrichies dans tous les services. Par conséquent, si vous décidez d’acheter un service Azure AD payant pour vos ressources, nous vous recommandons d’utiliser le même annuaire pour effectuer cette opération. - Si vous prévoyez d’utiliser Azure AD pour un autre groupe d’utilisateurs (partenaires, clients, etc.), que vous souhaitez évaluer des services Azure AD indépendamment de votre service de production, ou que vous cherchez à configurer un environnement de bac à sable (sandbox) pour vos services, nous vous recommandons de commencer par créer un autre annuaire par le biais du Portail de gestion Azure. [En savoir plus sur la création d’un annuaire Azure AD dans le Portail de gestion Azure](active-directory-licensing-directory-independence.md). Ce nouvel annuaire sera créé avec votre compte en tant qu’utilisateur externe avec des autorisations d’administrateur globales. Lorsque vous vous connecterez au Portail de gestion Azure avec ce compte, vous pourrez voir cet annuaire et accéder à toutes les tâches d’administration d’annuaire. Nous vous recommandons de créer un compte local avec les privilèges appropriés pour gérer les autres services Microsoft (ceux qui ne sont pas accessibles par le biais du Portail de gestion Azure). [En savoir plus sur la création de comptes d’utilisateurs dans Azure AD](active-directory-create-users.md).

> [AZURE.NOTE]Azure AD prend en charge les « utilisateurs externes », qui correspondent aux comptes d’utilisateurs d’une instance Azure AD qui ont été créés à l’aide d’un compte Microsoft (MSA) ou d’une identité Azure AD à partir d’un autre annuaire. Bien que nous ayons entrepris d’étendre cette fonctionnalité à tous les services d’organisation Microsoft, ces comptes ne sont actuellement pas pris en charge dans les expériences de certains services ; par exemple, le portail d’administration d’Office 365 ne prend pas en charge ces utilisateurs pour l’instant. En conséquence, les utilisateurs externes dotés de comptes Microsoft ne seront pas en mesure d’accéder au portail d’administration d’Office 365, tandis que les utilisateurs externes d’autres annuaires Azure AD seront ignorés. Dans ce dernier cas, seuls le compte local de l’utilisateur, l’annuaire Azure AD ou l’annuaire Office 365 dans lesquels l’utilisateur a été créé, seront accessibles par le biais de ces expériences.

Comme indiqué précédemment, Azure AD existe en différentes versions payantes. Ces versions présentent de légères différences sur le plan de leur disponibilité d’achat :


| Produit | Contrat Entreprise/Licence en volume | Ouvrir | 	CSP | 	Droits d’utilisation MPN | 	Achat direct | Version d’évaluation |
|---|---|---|---|---|---|---|
| Enterprise Mobility Suite |	X |	X |	X |	X | |	 	X |
| Azure AD Premium | X | X | X | | X | X |
| Azure AD Standard | X | X | X | X | <br /> | <br /> |

###Sélectionner une ou plusieurs versions d’évaluation avec licences
 Dans tous les cas, vous pouvez activer un abonnement à une version d’évaluation d’Azure AD Premium ou d’Enterprise Mobility Suite en sélectionnant la version d’évaluation souhaitée dans l’onglet Licences de votre annuaire. Chaque version d’évaluation contient un abonnement de 30 jours avec 100 licences.

![Plans de licence de version d’évaluation Azure Active Directory](./media/active-directory-licensing-what-is/trial_plans.png)

![Plans de licence de version d’évaluation Enterprise Mobility Suite](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![Plans de licence de version d’évaluation actifs](./media/active-directory-licensing-what-is/active_license_trials.png)

###Attribuer des licences
Une fois l’inscription active, vous devez vous attribuer une licence et actualiser le navigateur pour être en mesure de visualiser toutes les fonctionnalités. L’étape suivante consiste à attribuer des licences aux utilisateurs qui devront accéder aux fonctionnalités Azure AD payantes ou être inclus dans ces dernières. Comme nous l’avons indiqué plus haut à la section « Attribution de licences », le meilleur moyen de procéder consiste à identifier le groupe représentant l’audience souhaitée et à l’attribuer à la licence ; de cette façon, si des utilisateurs sont ajoutés au groupe ou en sont supprimés pendant son cycle de vie, la licence leur sera également attribuée ou retirée.

Pour attribuer une licence à un groupe ou à des utilisateurs spécifiques, sélectionnez le plan de licence que vous souhaitez attribuer, puis cliquez sur**Attribuer** sur la barre de commandes.

![Plans de licence de version d’évaluation actifs](./media/active-directory-licensing-what-is/assign_licenses.png)

Une fois que la boîte de dialogue d’attribution pour le plan sélectionné s’est affichée, vous pouvez sélectionner des utilisateurs et les ajouter à la colonne **Attribuer** sur la droite. Vous pouvez parcourir la liste des utilisateurs ou rechercher des personnes spécifiques à l’aide de la loupe dans la partie supérieure droite de la grille des utilisateurs. Pour attribuer des groupes, sélectionnez « Groupes » dans le menu **Afficher**, puis cliquez sur le bouton représentant une coche sur la droite pour actualiser les attributions qui sont affichées.

![Attribution de licences à des groupes](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

Vous pouvez alors rechercher ou parcourir les groupes et les ajouter à la colonne **Attribuer** de la même façon. Vous pouvez utiliser ces derniers pour attribuer une combinaison d’utilisateurs et de groupes en une seule opération. Pour achever le processus d’attribution, cliquez sur le bouton représentant une coche dans le coin inférieur droit de la page.

![Message de progression de l’attribution de licence](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

Lorsqu’un groupe est attribué, ses membres héritent des licences dans les 30 minutes qui suivent, mais le plus souvent en l’espace de 1 à 2 minutes seulement.

Lors de l’attribution des licences de portail Azure AD, des erreurs d’attribution peuvent se produire, mais restent relativement rares. Les erreurs d’attribution se limitent aux problèmes suivants : - Conflit d’attribution, lorsqu’un utilisateur a précédemment reçu une licence incompatible avec la licence actuelle. Dans ce cas, l’attribution de la nouvelle licence nécessitera la suppression de la licence précédente. - Dépassement du nombre de licences disponibles : lorsque le nombre d’utilisateurs des groupes attribués dépasse le nombre de licences disponibles, l’état d’attribution des utilisateurs reflétera l’échec de l’attribution du fait de licences manquantes.

###Visualiser les licences attribuées

L’onglet **Licences** présente une vue récapitulative des licences attribuées, indiquant le nombre de licences disponibles et attribuées, et l’événement de cycle de vie d’abonnement suivant.

![Visualiser le nombre de licences attribuées](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

L’accès à un plan de licence permet de visualiser une liste détaillée des utilisateurs et groupes attribués, comprenant l’état d’attribution et le chemin d’accès (attribution directe ou héritée d’un ou de plusieurs groupes).

![Affichage détaillé des licences attribuées pour un plan de licence](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

La suppression de licences se révèle tout aussi simple que l’attribution de licences. Si l’utilisateur est attribué directement ou dans le cas d’un groupe attribué, vous pouvez supprimer la licence en choisissant le type de licence, en sélectionnant **Supprimer**, en ajoutant l’utilisateur ou le groupe à la liste de suppression, puis en confirmant l’action. Une autre possibilité consiste à ouvrir un type de licence, à sélectionner l’utilisateur ou le groupe concernés, puis à appuyer sur **Supprimer** sur la barre de commandes. Pour mettre fin à l’héritage d’une licence par un utilisateur à partir d’un groupe, supprimez simplement l’utilisateur de ce groupe.

###Extension des versions d’évaluation

Des extensions de version d’évaluation pour les clients sont disponibles en libre service par le biais du portail Office 365. Un administrateur de client peut accéder au [portail Office](https://portal.office.com/#Billing) (l’accès dépend des autorisations relatives au portail Office) et sélectionner votre version d’évaluation d’Azure AD Premium. Cliquez sur le lien **Étendre la version d’évaluation** et suivez les instructions. Vous devrez entrer un numéro de carte de crédit, mais cette dernière ne sera pas facturée.

![Extension d’une version d’évaluation avec licences dans le portail Office](./media/active-directory-licensing-what-is/extend_license_trial.png)

Les clients peuvent également demander une extension de version d’évaluation en envoyant une demande de support. Un administrateur de client peut accéder à la [page de support](http://aka.ms/extendAADtrial) du portail Office (l’accès dépend des autorisations relatives à la page de support Office). Sur cette page, sélectionnez « Abonnements et versions d’évaluation » sous Fonctionnalités, et « Questions de version d’évaluation » sous Symptôme. Pour finir, entrez les informations sur les circonstances.

![Extension d’une version d’évaluation avec licences à l’aide d’une demande de support](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## Étapes suivantes

Vous voilà prêt à configurer et à utiliser certaines fonctionnalités d’Azure AD Premium.

- [Réinitialisation de mot de passe en libre service](active-directory-manage-passwords.md)
- [Gestion des groupes en libre service](active-directory-accessmanagement-self-service-group-management.md)
- [Azure AD Connect Health](active-directory-aadconnect-health.md)
- [Attribution de groupe aux applications](active-directory-manage-groups.md)
- [Azure Multi-Factor Authentication](multi-factor-authentication.md)
- [Achat direct de licences Azure AD Premium](http://aka.ms/buyaadp)

<!---HONumber=August15_HO9-->