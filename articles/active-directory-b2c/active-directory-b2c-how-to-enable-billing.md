---
title: "Comment lier un abonnement Azure à Azure AD B2C | Documents Microsoft"
description: "Guide détaillé pour activer la facturation pour un client Azure AD B2C dans un abonnement Azure."
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 5b9955b2af7f20a79981315fa33a0eb5380a5465
ms.contentlocale: fr-fr
ms.lasthandoff: 05/23/2017


---
# <a name="linking-an-azure-subscription-to-an-azure-b2c-tenant-to-pay-for-usage-charges"></a>Liaison d’un abonnement Azure à un client Azure B2C pour payer les frais d’utilisation

Les frais d’utilisation courants pour Azure Active Directory B2C (ou Azure AD B2C) sont facturés à un abonnement Azure. L’administrateur du client doit lier explicitement le client Azure AD B2C à un abonnement Azure après avoir créé le client B2C lui-même.  Ce lien est obtenu en créant une ressource « Client B2C » Azure AD dans l’abonnement Azure cible. De nombreux clients B2C peuvent être liés à un abonnement Azure, ainsi qu’à d’autres ressources Azure (par exemple, machines virtuelles, stockage de données, LogicApps).


> [!IMPORTANT]
> Les dernières informations sur la facturation à l’utilisation et la tarification de B2C sont disponibles sur la page suivante : [Azure Active Directory B2C Tarification](
https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="step-1---create-an-azure-ad-b2c-tenant"></a>Étape 1 - Création d’un client Azure AD B2C
Le client B2C doit être créé en premier. Ignorez cette étape si vous avez déjà créé votre client B2C. [Prise en main d’Azure Active Directory B2C](active-directory-b2c-get-started.md)

## <a name="step-2---open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Étape 2 - Ouverture du portail Azure dans le client Azure AD qui affiche votre abonnement Azure
Accédez au [portail Azure](https://portal.azure.com). Basculez vers le client Azure AD affichant l’abonnement Azure que vous souhaitez utiliser. Ce client Azure AD est différent du client B2C. Dans le portail Azure, cliquez sur le nom du compte dans le coin supérieur droit du tableau de bord pour sélectionner le client Azure AD. Un abonnement Azure est nécessaire pour continuer. [Obtenir un abonnement Azure](https://account.windowsazure.com/signup?showCatalog=True)

![Basculement vers votre client Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="step-3---create-a-b2c-tenant-resource-in-azure-marketplace"></a>Étape 3 - Création d’une ressource Client B2C dans Azure Marketplace
Ouvrez Marketplace en cliquant sur l’icône Marketplace ou en sélectionnant le « + » vert dans le coin supérieur gauche du tableau de bord.  Recherchez et sélectionnez Azure Active Directory B2C. Sélectionnez Créer.

![Sélectionnez Place de marché](./media/active-directory-b2c-how-to-enable-billing/marketplace.png)

![Rechercher AD B2C](./media/active-directory-b2c-how-to-enable-billing/searchb2c.png)

La boîte de dialogue de création de la ressource Azure AD B2C contient les paramètres suivants :

1. Client Azure AD B2C – Sélectionnez un client Azure AD B2C dans la liste déroulante.  Seuls les clients Azure AD B2C éligibles s’affichent.  Les clients B2C éligibles remplissent les conditions suivantes : vous êtes l’administrateur global du client B2C, et le client B2C n’est pas associé à un abonnement Azure.

2. Nom de la ressource Azure AD B2C - Ce nom est présélectionné et correspond au nom de domaine du client B2C.

3. Abonnement - Il s’agit d’un abonnement Azure actif dans lequel vous êtes un administrateur ou un coadministrateur.  Plusieurs clients Azure AD B2C peuvent être ajoutés à un abonnement Azure.

4. Groupe de ressources et emplacement du groupe de ressources - Cet artefact vous permet d’organiser plusieurs ressources Azure.  Ce choix n’a aucun impact sur l’emplacement, les performances ou l’état de facturation du client B2C.

5. Épinglez le client B2C au tableau de bord pour faciliter l’accès à ses informations de facturation et à ses paramètres. ![Créer une ressource B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="step-4---manage-your-b2c-tenant-resources-optional"></a>Étape 4 - Gestion de vos ressources Client B2C (facultatif)
Une fois le déploiement terminé, une nouvelle ressource « Client B2C » est créée dans le groupe de ressources cible et l’abonnement Azure concerné.  Une nouvelle ressource de type « Client B2C » s’affiche à côté de vos autres ressources Azure.

![Créer une ressource B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcedashboard.png)

Lorsque vous cliquez sur la ressource Client B2C, vous pouvez :
- cliquer sur le nom de l’abonnement et afficher les informations de facturation (consultez Facturation et utilisation) ;
- cliquer sur Azure AD B2C Settings (Paramètres Azure AD B2C) pour ouvrir un nouvel onglet de navigateur directement dans le panneau Paramètres de votre client B2C ;
- envoyer une demande de support ;
- déplacer votre ressource de client B2C vers un autre abonnement Azure ou un autre groupe de ressources.  Ce choix modifie l’abonnement Azure qui reçoit les frais d’utilisation.

![Paramètres de ressource B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Problèmes connus
- Suppression d’un locataire B2C. Si un locataire B2C est créé, supprimé et recréé avec le même nom de domaine, vous devez aussi supprimer et recréer la ressource « Liaison » avec le même nom de domaine.  Vous pouvez trouver cette ressource « Liaison » sous « Toutes les ressources » dans le locataire de l’abonnement via le portail Azure.
- Restrictions auto-imposées sur l’emplacement des ressources régionales.  Dans de rares cas, un utilisateur peut avoir établi une restriction régionale pour la création de ressources Azure.  Cette restriction peut empêcher la création de la liaison entre un abonnement Azure et un locataire B2C. Pour atténuer ce problème, assouplissez cette restriction.

## <a name="next-steps"></a>Étapes suivantes
Une fois ces étapes effectuées pour chacun de vos clients B2C, votre abonnement Azure est facturé conformément aux détails de votre contrat Azure Direct ou Enterprise.
- Consulter l’utilisation et la facturation dans l’abonnement Azure sélectionné
- Consulter les rapports détaillés d’utilisation quotidienne à l’aide de [l’API Usage Reporting](active-directory-b2c-reference-usage-reporting-api.md)

