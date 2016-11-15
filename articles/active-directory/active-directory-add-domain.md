---
title: "Ajout de votre nom de domaine personnalisé à Azure Active Directory | Microsoft Docs"
description: "Comment ajouter les noms de domaine de votre société à Azure Active Directory et comment vérifier le nom de domaine."
services: active-directory
documentationcenter: 
author: jeffsta
manager: femila
editor: 
ms.assetid: 35a6e20a-9907-432b-9d36-16b916a5c249
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/30/2016
ms.author: curtand;jeffsta
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d24af6385c24860f40f6aab633e6212431809aea


---
# <a name="add-a-custom-domain-name-to-azure-active-directory"></a>Ajouter un nom de domaine personnalisé à Azure Active Directory
> [!div class="op_single_selector"]
> * [portail Azure](active-directory-domains-add-azure-portal.md)
> * [Portail Azure Classic](active-directory-add-domain.md)
> 
> 

Votre organisation utilise un ou plusieurs noms de domaine pour mener ses activités, et vos utilisateurs se connectent au réseau d’entreprise en utilisant votre nom de domaine d’entreprise. Maintenant que vous utilisez le logiciel Azure Active Directory (Azure AD), vous pouvez également ajouter votre nom de domaine d’entreprise à ce dernier. Cette tâche vous permet d’attribuer des noms de répertoire qui sont familiers à vos utilisateurs, comme ‘alice@contoso.com.’. Le processus est simple :

1. Ajoutez le nom de domaine personnalisé à votre annuaire.
2. Ajoutez une entrée DNS pour le nom de domaine au niveau du bureau d’enregistrement de noms de domaine.
3. Vérifiez le nom de domaine personnalisé dans Azure AD.

> [!NOTE]
> Si vous envisagez de configurer votre nom de domaine personnalisé à utiliser avec les services de fédération Active Directory (AD FS) ou avec un service d’émission de jeton de sécurité (STS) différent sur votre réseau d’entreprise, suivez les instructions fournies dans [Add and configure a domain for federation with Azure Active Directory](active-directory-add-domain-federated.md)(Ajouter et configurer un domaine pour la fédération avec Azure Active Directory). Cette opération est utile si vous prévoyez de synchroniser les utilisateurs de votre annuaire d’entreprise avec Azure AD, et que la [synchronisation du hachage de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md) ne répond pas à vos besoins.
> 
> 

## <a name="add-a-custom-domain-name-to-your-directory"></a>Ajouter un nom de domaine personnalisé à votre annuaire
1. Connectez-vous au [Portail Azure Classic](https://manage.windowsazure.com/) en utilisant le compte d’un administrateur général de votre annuaire Azure AD.
2. Dans **Active Directory**, ouvrez votre annuaire et sélectionnez l’onglet **Domaines**.
3. Dans la barre de commandes, sélectionnez **Ajouter**. Entrez le nom de votre domaine personnalisé, par exemple « contoso.com ». Veillez à inclure .com, .net ou une autre extension de premier niveau, et ne cochez pas la case de l’option « Authentification unique » (fédération).
4. Sélectionnez **Ajouter**.
5. Sur la deuxième page de l’Assistant Ajouter un domaine, obtenez l’entrée DNS qu’Azure AD doit utiliser pour vérifier que votre organisation possède le nom de domaine personnalisé.

Maintenant que vous avez ajouté le nom de domaine, Azure AD doit vérifier que votre organisation possède le nom de domaine. Pour qu’Azure AD puisse effectuer cette vérification, vous devez ajouter une entrée DNS dans le fichier de zone DNS pour le nom de domaine. Cette tâche peut être effectuée sur le site web pour le bureau d’enregistrement de noms de domaine associé au nom de domaine.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Ajouter l’entrée DNS pour le domaine au niveau du bureau d’enregistrement de noms de domaine
L’étape suivante pour utiliser votre nom de domaine personnalisé avec Azure AD consiste à mettre à jour le fichier de zone DNS pour le domaine. Cette opération permet à Azure AD de vérifier que votre organisation possède le nom de domaine personnalisé.

1. Connectez-vous au Bureau d’enregistrement des noms de domaine pour le domaine. Si vous n’avez pas accès à ce bureau pour mettre à jour l’entrée DNS, demandez à la personne ou à l’équipe disposant de cet accès d’exécuter l’étape 2 et de vous avertir une fois l’opération effectuée.
2. Mettez à jour le fichier de zone DNS pour le domaine en ajoutant l’entrée DNS fournie par Azure AD. Cette entrée DNS permet à Azure AD de vérifier que vous êtes le propriétaire du domaine. Elle ne modifiera aucun comportement comme le routage du courrier ou l’hébergement web.

Pour plus d’informations sur cette procédure d’ajout de l’entrée DNS, voir [Instructions pour ajouter une entrée DNS à des Bureaux d’enregistrement DNS courants](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Vérifier le nom de domaine avec Azure AD.
Une fois que vous avez ajouté l’entrée DNS, vous êtes prêt à vérifier le nom de domaine avec Azure AD.

Si l’Assistant **Ajouter un domaine** est toujours ouvert, sélectionnez **Vérifier** dans sa troisième page. Lorsque vous sélectionnez **Vérifier**, Azure AD recherche l’entrée DNS dans le fichier de zone DNS pour le domaine. Azure AD ne peut vérifier le nom de domaine qu’une fois les enregistrements DNS propagés. Cette propagation ne prend généralement que quelques secondes, mais peut parfois nécessiter une heure ou davantage. Si la vérification ne fonctionne pas la première fois, réessayez ultérieurement.

Si l’Assistant **Ajouter un domaine** n’est plus ouvert, vous pouvez vérifier le domaine dans le [Portail Azure Classic](https://manage.windowsazure.com/):

1. Connectez-vous à l’aide d’un compte d’administrateur général de votre annuaire Azure AD.
2. Ouvrez votre annuaire, puis sélectionnez l’onglet **Domaines** .
3. Sélectionnez le nom de domaine que vous souhaitez vérifier, puis choisissez **Vérifier** dans la barre de commandes.
4. Sélectionnez **Vérifier** dans la boîte de dialogue pour effectuer la vérification.

Vous pouvez désormais [affecter des noms d’utilisateur incluant votre nom de domaine personnalisé](active-directory-add-domain-add-users.md).

## <a name="troubleshooting"></a>Résolution de problèmes
Si vous ne parvenez pas à vérifier un nom de domaine personnalisé, essayez d’effectuer les opérations ci-après. Nous irons de la plus courante à la moins courante.

1. **Attendez une heure**. Les enregistrements DNS doivent être propagés avant qu’Azure AD puisse vérifier le domaine. Cette opération peut prendre une heure ou davantage.
2. **Vérifiez que l’enregistrement DNS a été entré et qu’il est correct**. Exécutez cette tâche sur le site web du bureau d’enregistrement de noms de domaine correspondant au domaine. Azure AD ne peut pas vérifier le nom de domaine si l’entrée DNS est absente du fichier de zone DNS ou qu’elle ne correspond pas exactement à l’entrée DNS qu’Azure AD vous a fournie. Si vous ne disposez pas d’un accès pour mettre à jour les enregistrements DNS du domaine au niveau du bureau d’enregistrement de noms de domaine, partagez l’entrée DNS avec la personne ou l’équipe de votre organisation qui possède cet accès et demandez-lui d’ajouter cette entrée.
3. **Supprimez le nom de domaine d’un autre annuaire dans Azure AD**. Un nom de domaine ne peut être vérifié que dans un seul annuaire. Si un nom de domaine a été précédemment vérifié dans un autre annuaire, vous devez le supprimer avant de pouvoir le vérifier dans votre nouvel annuaire. Pour plus d’informations sur la suppression des noms de domaine, voir [Gérer les noms de domaine personnalisé](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>Ajouter des noms de domaines personnalisés
Si votre organisation utilise plusieurs noms de domaine personnalisés, tels que « contoso.com » et « contosobank.com », vous pouvez les ajouter (le nombre maximal autorisé de noms de domaine est de 900). Suivez la procédure décrite dans cet article pour ajouter chacun de ces noms de domaine.

## <a name="next-steps"></a>Étapes suivantes
* [Affecter des noms d’utilisateur qui incluent votre nom de domaine personnalisé](active-directory-add-domain-add-users.md)
* [Gérer les noms de domaine personnalisés](active-directory-add-manage-domain-names.md)
* [En savoir plus sur les concepts de gestion de domaine dans Azure AD](active-directory-add-domain-concepts.md)
* [Afficher la marque de votre société lorsque vos utilisateurs se connectent](active-directory-add-company-branding.md)
* [Utiliser PowerShell pour gérer les noms de domaine dans Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)




<!--HONumber=Nov16_HO2-->


