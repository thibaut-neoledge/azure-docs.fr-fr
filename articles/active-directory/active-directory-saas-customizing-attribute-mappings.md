---
title: Personnalisation des mappages d’attributs | Microsoft Docs
description: Découvrez ce que sont les mappages d’attributs pour les applications SaaS dans Azure Active Directory et comment les modifier pour répondre aux besoins de votre entreprise.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: markusvi

---
# <a name="customizing-attribute-mappings"></a>Personnalisation des mappages d’attributs
Microsoft Azure AD prend en charge l’approvisionnement d’utilisateurs pour les applications SaaS tierces telles que Salesforce, Google Apps et autres. Si vous avez activé l’approvisionnement d’utilisateurs pour une application SaaS tierce, le portail de gestion Azure contrôle ses valeurs d’attributs sous forme d’une configuration appelée « mappage d’attributs ».

Il existe un ensemble préconfiguré de mappages d’attributs entre les objets utilisateur Azure AD et les objets utilisateur de chaque application SaaS. Certaines applications gèrent d’autres types d’objets, tels que des groupes ou des contacts. <br> 
 Vous pouvez personnaliser les mappages d’attributs par défaut en fonction des besoins de votre entreprise. Cela signifie que vous pouvez modifier ou supprimer des mappages d’attributs existants ou en créer de nouveaux.

Dans le portail Azure AD, vous pouvez accéder à cette fonctionnalité en cliquant sur Attributs dans la barre d’outils d’une application SaaS.

> [!NOTE]
> Le lien **Attributs** est disponible uniquement si vous avez activé l’approvisionnement d’utilisateurs pour une application SaaS. 
> 
> 

![Salesforce][1] 

Quand vous cliquez sur Attributs dans la barre d’outils, la liste des mappages actuels configurés pour une application SaaS s’affiche.

La capture d’écran suivante montre un exemple :

![Salesforce][2]  

Dans l’exemple ci-dessus, vous pouvez voir que l’attribut **firstName** d’un objet géré dans Salesforce est rempli avec la valeur **givenName** de l’objet Azure AD lié.

Si vous souhaitez personnaliser les mappages d’attributs ou rétablir la configuration par défaut des paramètres personnalisés, cliquez sur le bouton correspondant dans la barre d’outils en bas d’une application.

![Salesforce][3]  

Des applications SaaS nécessitent certains mappages d’attributs pour fonctionner correctement. Dans la table d’attributs, les mappages d’attributs associés ont **Oui** comme valeur pour l’attribut **Requis**. Si un mappage d’attributs est requis, vous ne pouvez pas le supprimer. Dans ce cas, l’option **Supprimer** n’est pas disponible.

Pour modifier un mappage d’attribut existant, sélectionnez le mappage et cliquez sur **Modifier**. Ceci affiche une page de boîte de dialogue qui vous permet de modifier le mappage d’attribut sélectionné.

![Modifier le mappage d’attribut][4]  

## <a name="understanding-attribute-mapping-types"></a>Présentation des types de mappages d’attributs
Avec les mappages d’attributs, vous contrôlez la façon dont les attributs sont renseignés dans une application SaaS tierce. Quatre différents types de mappages sont pris en charge :

* **Direct** : l’attribut cible est renseigné avec la valeur d’un attribut de l’objet lié dans Azure AD.
* **Constant** : l’attribut cible est renseigné avec une chaîne spécifique que vous avez spécifiée.
* **Expression** : l’attribut cible est renseigné en fonction du résultat d’une expression semblable à un script. Pour plus d’informations, consultez [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).
* **Aucun** : l’attribut cible reste inchangé. Toutefois, si l’attribut cible est vide, il sera renseigné avec la valeur par défaut que vous spécifiez.

Outre ces quatre types de mappages d’attributs de base, les mappages d’attributs personnalisés prennent en charge le concept d’affectation de valeur **par défaut** . L’affectation de valeur par défaut garantit qu’un attribut cible est renseigné avec une valeur s’il n’existe aucune valeur ni dans Azure AD, ni sur l’objet cible.

Microsoft Azure AD fournit une implémentation très efficace d’un processus de synchronisation. Dans un environnement initialisé, seuls les objets nécessitant des mises à jour sont traités pendant un cycle de synchronisation. La mise à jour des mappages d’attributs a un impact sur les performances d’un cycle de synchronisation. En effet, une mise à jour de la configuration des mappages d’attributs nécessite une réévaluation de tous les objets gérés. Pour cette raison, il est recommandé de limiter au minimum le nombre de modifications consécutives de vos mappages d’attributs.

## <a name="related-articles"></a>Articles connexes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Automatiser l’approvisionnement/annuler l’approvisionnement des utilisateurs pour les applications SaaS](active-directory-saas-app-provisioning.md)
* [Écriture d’expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtres d’étendue pour l’approvisionnement des utilisateurs](active-directory-saas-scoping-filters.md)
* [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](active-directory-scim-provisioning.md)
* [Notifications d’approvisionnement de comptes](active-directory-saas-account-provisioning-notifications.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png



<!--HONumber=Oct16_HO2-->


