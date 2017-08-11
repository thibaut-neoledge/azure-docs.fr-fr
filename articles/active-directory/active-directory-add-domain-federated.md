---
title: "Ajouter votre nom de domaine personnalisé et configurer la connexion fédérée sur Azure Active Directory | Microsoft Docs"
description: "Découvrez comment ajouter les noms de domaine de votre entreprise à Azure Active Directory pour configurer la connexion fédérée entre Azure Active Directory et votre solution de fédération locale."
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 27126c7e-e6d6-4ef3-a4fb-f5f0706e749d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 7f257dff6fdd423b89d1c52a84b64951dcf5915f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/17/2017

---
# <a name="add-your-custom-domain-name-to-azure-active-directory"></a>Ajout de votre nom de domaine personnalisé à Azure Active Directory
Vous pouvez configurer un nom de domaine personnalisé, par exemple « contoso.com », pour que les utilisateurs de contoso.com puissent disposer d’une expérience d’authentification unique fédérée à partir de votre réseau d’entreprise. Si vous possédez déjà les services de fédération Active Directory (AD FS) ou un autre serveur de fédération qui s’exécutent sur votre réseau d’entreprise, vous pouvez configurer Azure AD pour utiliser votre nom de domaine personnalisé à l’aide de l’outil Azure AD Connect. Vous pouvez également utiliser Azure AD Connect pour déployer un nouvel environnement AD FS, et le configurer pour l’authentification unique fédérée sur Azure AD.

Si vous n’avez pas déployé AD FS ou un autre serveur de fédération et que vous n’envisagez pas de le faire, suivez les instructions contenues dans l’article : [Ajout de votre nom de domaine personnalisé à Azure Active Directory](active-directory-add-domain.md).

## <a name="add-a-custom-domain-name-to-your-directory"></a>Ajouter un nom de domaine personnalisé à votre annuaire
1. Connectez-vous au [Portail Azure Classic](https://manage.windowsazure.com/) en utilisant le compte d’un administrateur général de votre annuaire Azure AD.
2. Dans **Active Directory**, ouvrez votre annuaire et sélectionnez l’onglet **Domaines**.
3. Dans la barre de commandes, sélectionnez **Ajouter**, puis indiquez le nom de votre domaine personnalisé, par exemple « contoso.com ». Veillez à inclure .com, .net ou une autre extension de niveau supérieur.
4. Cochez la case **Je prévois de configurer ce domaine pour la connexion unique à mon annuaire Active Directory local** .
5. Sélectionnez **Ajouter**.

Exécutez l’outil Azure AD Connect pour obtenir l’entrée DNS qui sera utilisée par Azure AD pour vérifier le domaine. L’entrée DNS apparaît à l’étape **Domaine Azure AD** de l’Assistant. Vous pouvez voir à quoi cette étape de l’assistant ressemble [dans ces instructions](connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Si vous ne disposez pas de l’outil Azure AD Connect, vous pouvez [le télécharger ici](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Ajouter l’entrée DNS pour le domaine au niveau du bureau d’enregistrement de noms de domaine
L’étape suivante pour utiliser votre nom de domaine personnalisé avec Azure AD consiste à mettre à jour le fichier de zone DNS pour le domaine. Cette opération permet à Azure AD de vérifier que votre organisation possède le nom de domaine personnalisé.

1. Connectez-vous au site web du bureau d’enregistrement de noms de domaine associé à votre nom de domaine. Si vous n’avez pas accès à ce bureau pour effectuer cette opération, demandez à la personne ou à l’équipe au sein de votre organisation qui dispose de cet accès d’exécuter l’étape 2 et de vous avertir une fois l’opération effectuée.
2. Mettez à jour le fichier de zone DNS pour le domaine en ajoutant l’entrée DNS fournie par Azure AD. Cette entrée DNS permet à Azure AD de vérifier que vous êtes le propriétaire du domaine. Elle ne modifiera aucun comportement comme le routage du courrier ou l’hébergement web.

Pour plus d’informations sur cette étape, voir [Instructions pour ajouter une entrée DNS à des Bureaux d’enregistrement DNS courants](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Vérifier le nom de domaine avec Azure AD.
Une fois que vous avez ajouté l’entrée DNS, vous êtes prêt à vérifier le nom de domaine avec Azure AD.

Pour vérifier le domaine, sélectionnez **Suivant** à l’étape **domaine Azure AD** de l’Assistant Azure AD Connect. Azure AD recherche l’entrée DNS dans le fichier de zone DNS pour le domaine. Azure AD ne vérifie le nom de domaine qu’une fois les enregistrements DNS propagés. La propagation ne prend généralement que quelques secondes, mais peut parfois nécessiter une heure ou davantage. Si la vérification ne fonctionne pas la première fois, réessayez ultérieurement.

Passez ensuite aux étapes restantes de l’Assistant Azure AD Connect. Ce faisant, vous synchroniserez les utilisateurs de votre Windows Server AD avec Azure AD. Les utilisateurs synchronisés dans le domaine que vous avez configuré pour la fédération seront en mesure de profiter d’une expérience d’authentification unique fédérée à partir de votre réseau d’entreprise sur Azure AD.

## <a name="troubleshooting"></a>Résolution des problèmes
Si vous ne parvenez pas à vérifier un nom de domaine personnalisé, essayez d’effectuer les opérations ci-après. Nous irons de la plus courante à la moins courante.

1. **Attendez une heure**. Les enregistrements DNS doivent être propagés avant qu’Azure AD puisse vérifier le domaine. Cette opération peut prendre une heure ou davantage.
2. **Vérifiez que l’enregistrement DNS a été entré et qu’il est correct**. Exécutez cette tâche sur le site web du bureau d’enregistrement de noms de domaine correspondant au domaine. Azure AD ne peut pas vérifier le nom de domaine si l’entrée DNS est absente du fichier de zone DNS ou qu’elle ne correspond pas exactement à l’entrée DNS qu’Azure AD vous a fournie. Si vous ne disposez pas d’un accès pour mettre à jour les enregistrements DNS du domaine au niveau du bureau d’enregistrement de noms de domaine, partagez l’entrée DNS avec la personne ou l’équipe de votre organisation qui possède cet accès et demandez-lui d’ajouter cette entrée.
3. **Supprimez le nom de domaine d’un autre annuaire dans Azure AD**. Un nom de domaine ne peut être vérifié que dans un seul annuaire. Si un nom de domaine a été précédemment vérifié dans un autre annuaire, vous devez le supprimer avant de pouvoir le vérifier dans votre nouvel annuaire. Pour plus d’informations sur la suppression des noms de domaine, voir [Gérer les noms de domaine personnalisé](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>Ajouter des noms de domaines personnalisés
Si votre organisation utilise plusieurs noms de domaine personnalisés, tels que « contoso.com » et « contosobank.com », vous pouvez les ajouter (le nombre maximal autorisé de noms de domaine est de 900). Suivez la procédure décrite dans cet article pour ajouter chacun de ces noms de domaine.

## <a name="next-steps"></a>Étapes suivantes
* [Gérer les noms de domaine personnalisés](active-directory-add-manage-domain-names.md)
* [En savoir plus sur les concepts de gestion de domaine dans Azure AD](active-directory-add-domain-concepts.md)
* [Afficher la marque de votre société lorsque vos utilisateurs se connectent](active-directory-add-company-branding.md)
* [Utiliser PowerShell pour gérer les noms de domaine dans Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)


