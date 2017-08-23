---
title: Modifier la facturation pour Azure RemoteApp | Microsoft Docs
description: "Apprenez comment arrêter la facturation pour Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 8f94da9a-7848-4ddc-b7b7-d9c280ccf4d7
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mbaldwin
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 32fc673eeef01e80c73375bf264206beea8cfbe5
ms.contentlocale: fr-fr
ms.lasthandoff: 07/18/2017

---
# <a name="migrate-from-azure-remoteapp-to-mycloudit"></a>Migrer d’Azure RemoteApp vers MyCloudIT 

**Vous utilisez actuellement Microsoft Azure RemoteApp ?** : MyCloudIT a créé un outil automatisé pour migrer vos collections Azure RemoteApp (ARA) vers la plateforme de gestion MyCloudIT tout en continuant à s’exécuter sur Microsoft Azure.

**Profitez du portail Azure Resource Manager** : la migration vers la plateforme MyCloudIT offre un accès immédiat au nouveau portail Azure Resource Manager d’Azure. Ce portail contient toutes les nouvelles fonctionnalités et innovations offertes par Microsoft Azure, notamment l’accès aux différentes tailles de machines virtuelles pour vous garantir un déploiement adapté aux besoins de votre entreprise.

**Effectuez des tests en parallèle pour vous assurer de choisir la solution adaptée à vos besoins** : l’outil de migration MyCloudIT est conçu pour lancer le processus de migration et effectuer des tests en parallèle pendant que vos utilisateurs ARA actuels continuent d’utiliser ARA.  Vos utilisateurs continuent d’exécuter ARA jusqu’à la fin de la migration et des tests.  L’outil de migration est conçu pour gérer les collections ARA classiques.  Si vous pensez que votre cas est spécifique et non standard, contactez-nous à l’adresse [ sales@conexlink.com ](mailto:sales@conexlink.com) afin de nous puissions vous proposer un plan de migration personnalisé.

**Fonctionnalités de Desktop-as-a-Service** : notez que MyCloudIT offre non seulement les fonctionnalités de RemoteApp que vous connaissez déjà, mais elle propose également des fonctionnalités Desktop-as-a-Service complètes pour le même coût par mois sans conditions minimales requises en matière d’utilisateurs.

## <a name="what-we-will-do-for-you"></a>Ce que nous allons faire pour vous

MyCloudIT automatise la migration de votre modèle Azure RemoteApp à partir du portail Azure Classic de votre abonnement vers le portail Azure Resource Manager de votre abonnement avec notre outil de migration automatisé.  

> [!NOTE]
> Le modèle Azure RemoteApp doit rester dans la même région Azure que votre déploiement d’Azure RemoteApp d’origine.  Si vous devez modifier des régions ou des abonnements Azure pendant la migration, contactez-nous pour obtenir des conseils supplémentaires à l’adresse [ sales@conexlink.com ](mailto:sales@conexlink.com).

Pour plus d’informations sur le processus de migration automatisé avec l’outil de migration MyCloudIT, lisez ce qui suit :

1. L’outil de migration analyse vos abonnements en cours à la recherche de tous les déploiements ARA existants.  
2. Sélectionnez une collection ARA à migrer à la fois.  Si vous avez plusieurs collections, exécutez notre outil autant de fois que nécessaire.
3. Vous pouvez copier les disques de profil utilisateur vers votre nouveau déploiement afin de récupérer des données héritées, ou mapper manuellement ces disques vers le nouveau déploiement. Si vous choisissez de copier vos disques de profil utilisateur, nous les enregistrons et incluons un fichier texte mappant le nom des disques à chaque nom d’utilisateur.  Les disques de profil utilisateur sont copiés vers un partage sur le serveur RDSMGMT `F:\Shares\LegacyUPD` et sont présentés via le partage `\\RDSmgmt\LegacyUPD`. 
4. Votre migration ne perturbera pas votre déploiement ARA actuel.  Toutefois, si des modifications sont apportées aux disques de profil utilisateur (à partir d’ARA) après la copie, ces dernières ne seront pas répercutées dans les disques de profil utilisateur stockés dans le portail Azure Resource Manager. 
5. Si vous avez des machines virtuelles supplémentaires telles que des contrôleurs de domaine et des serveurs de fichiers dans votre réseau virtuel Azure classique, nous définissons une homologation de réseaux virtuels entre votre réseau virtuel Azure classique existant et le nouveau réseau virtuel que nous créons pour vous, dans le nouveau portail Azure Resource Manager.
6. Notre solution automatisée n’établira l’homologation de réseaux virtuels entre votre réseau virtuel classique Azure existant et le nouveau réseau virtuel que si votre déploiement ARA existant est un déploiement hybride. Autrement dit, vous vous authentifiez avec un contrôleur de domaine Windows Server Active Directory dans le réseau virtuel classique existant. Si nous n’établissons pas d’homologation de réseaux virtuels pour votre collection, mais si vous en avez besoin, contactez-nous à l’adresse [ sales@conexlink.com ](mailto:sales@conexlink.com) et nous serons heureux de la configurer pour vous sans coût supplémentaire.
7. Notre solution automatisée permet à votre configuration Azure DNS d’être mise à jour avec les nouveaux paramètres de réseau virtuel, ce qui vous garantit que votre nouveau déploiement peut se connecter à votre contrôleur de domaine existant dans le réseau virtuel classique.
8. Notre solution automatisée garantit l’absence de tout conflit d’adresse IP, car nous créons un nouveau réseau virtuel et établissons l’homologation de réseaux virtuels pour les déploiements avec un serveur Windows Server Active Directory existant.
9. Si vous utilisez Azure AD uniquement pour l’authentification, MyCloudIT crée un domaine Windows Server Active Directory et utilise Azure AD Connect pour synchroniser les utilisateurs entre l’instance Azure AD existante et le nouveau domaine Windows Server Active Directory créé par MyCloudIT.
10. Si vous utilisez un domaine Windows Server Active Directory pour authentifier les utilisateurs d’ARA, notre solution automatisée connecte votre nouveau déploiement MyCloudIT à votre contrôleur de domaine Windows Server Active Directory existant via l’homologation de réseaux virtuels.
11. Si vous utilisez Azure Active Directory Domain Services pour l’authentification, la migration est possible, mais contactez-nous afin que nous puissions vous proposer un plan de migration personnalisé.  Envoyez-nous un e-mail à l’adresse [sales@conexlink.com](mailto:sales@conexlink.com). 
12. Une fois que la collection à migrer est confirmée, laissez faire notre solution automatisée qui migrera votre collection ARA et vos disques de profil utilisateur (facultatif) vers la nouvelle solution d’applications distantes basée sur MyCloudIT.
13. À la fin du déploiement, nous republierons les mêmes applications que dans ARA et vous pourrez ensuite publier des applications supplémentaires.

## <a name="post-migration-benefits"></a>Avantages post-migration

1. Nous fournissons la console de gestion qui vous permettra de gérer le cycle de vie complet de votre déploiement d’applications distantes.
2. Vous pourrez gérer vos machines virtuelles à partir de notre portail.  Démarrez/arrêtez et redimensionnez des machines virtuelles individuelles si nécessaire.
3. La console de gestion vous permet de créer et de gérer des utilisateurs/groupes à partir de notre portail de gestion.
4. La console de gestion permet de synchroniser les utilisateurs avec Office 365 pour une expérience d’authentification identique.
5. La console de gestion permet de créer des applications distantes et des collections de bureaux supplémentaires sans coûts supplémentaires pour les utilisateurs en double ni conditions requises en matière de nombre minimum d’utilisateurs. 
6. La console de gestion permet de publier de nouvelles applications distantes.
7. La console de gestion permet de planifier le démarrage et l’arrêt de votre déploiement d’applications distantes si vous ne vous servez de votre solution qu’à certaines heures.
8. La console de gestion permet d’automatiser l’installation et la configuration de l’agent Azure Backup qui fournit un historique de rétention de document pour vos données client.
9. La console de gestion fournit un accès aux indicateurs de performance de votre déploiement.  Cela vous permet d’identifier les goulots d’étranglement potentiels au niveau des performances sans avoir à installer d’outils de gestion des performances supplémentaires.
10. Si vous avez plusieurs hôtes de session, vous pourrez activer la mise à l’échelle automatique afin que seuls les hôtes de session nécessaires soient exécutés.
11. MyCloudIT fournit un accès au serveur de passerelle RDWeb via un nom de domaine MyCloudIT.  Vous pouvez également remapper l’URL vers une URL personnalisée pour la personnalisation des utilisateurs finaux.

## <a name="prerequisites-for-migration"></a>Conditions requises pour la migration

1. Vous devez avoir accès à l’abonnement Azure qui héberge votre solution Azure RemoteApp actuelle.
2. Vous devez accorder les autorisations d’accès à notre portail au sein de votre abonnement pour migrer votre modèle et pour créer/modifier votre nouveau déploiement MyCloudIT.
3. Notez qu’en raison d’une limitation d’Azure RemoteApp, chaque collection ne peut être migrée que trois fois.  Si vous avez besoin de migrer une collection plus de trois fois, vous pouvez soumettre un ticket à Azure pour augmenter le nombre d’exports possibles, ou nous contacter pour que nous vous aidions à envoyer une demande ARA d’augmentation du nombre d’exports.

## <a name="mycloudit-billing"></a>Facturation MyCloudIT

Consultez le document PDF [MyCloudIT Pricing for RemoteApp Solutions](https://mcitdocuments.blob.core.windows.net/terms/MyCloudIT_Pricing_Overview.pdf) (Tarification MyCloudIT pour les solutions RemoteApp) pour savoir comment estimer et gérer vos coûts Azure globaux.

Si vous avez d’autres questions, contactez-nous à l’adresse [ sales@conexlink.com ](mailto:sales@conexlink.com) ou regardez la vidéo de démonstration complète [Azure RemoteApp Migration Tool - MyCloudIT](https://www.youtube.com/watch?v=YQ_1F-JeeLM&t=482s) (Outil de migration Azure RemoteApp - MyCloudIT). 


