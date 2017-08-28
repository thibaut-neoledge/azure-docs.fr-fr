---
title: "Ajouter un domaine personnalisé à Azure AD | Microsoft Docs"
description: "Explique comment ajouter un domaine personnalisé dans Azure Active Directory."
services: active-directory
author: jeffgilb
manager: femila
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 4848130601ffa18ed1565e79cb0f0db3274e950f
ms.contentlocale: fr-fr
ms.lasthandoff: 08/11/2017

---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>Démarrage rapide : Ajouter un nom de domaine personnalisé dans Azure Active Directory

Chaque annuaire Azure AD est fourni avec un nom de domaine initial au format suivant : *nom_de_domaine*.onmicrosoft.com. Le nom de domaine initial ne peut pas être modifié ni supprimé. Toutefois, vous pouvez ajouter votre nom de domaine d’entreprise à Azure AD. Par exemple, il se peut que votre organisation dispose d’autres noms de domaine dans le cadre de ses activités et que les utilisateurs se connectent à l’aide de votre nom de domaine d’entreprise. L’ajout de noms de domaine personnalisés à Azure AD vous permet d’attribuer des noms d’utilisateurs avec lesquels vos utilisateurs sont familiarisés, comme « alice@contoso.com ». au lieu d’« alice@*<domain name>*.onmicrosoft.com ». Le processus est simple :

1. Ajoutez le nom de domaine personnalisé à votre annuaire.
2. Ajoutez une entrée DNS pour le nom de domaine au niveau du bureau d’enregistrement de noms de domaine.
3. Vérifiez le nom de domaine personnalisé dans Azure AD.

## <a name="add-your-custom-domain"></a>Ajouter un domaine personnalisé
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour l’annuaire.
2. Sélectionnez **Autres services**, saisissez **Azure Active Directory** dans la zone de texte, puis sélectionnez **Entrée**.
   
   ![Ouvrir la gestion des utilisateurs](./media/active-directory-domains-add-azure-portal/user-management.png)
3. Dans le panneau ***nom-annuaire***, sélectionnez **Noms de domaine**.
4. Dans le panneau ***nom-annuaire* - Noms de domaine**, sélectionnez la commande **Ajouter**.
   
   ![Sélection de la commande Ajouter](./media/active-directory-domains-add-azure-portal/add-command.png)
5. Dans le panneau **Nom de domaine**, saisissez le nom de votre domaine personnalisé dans la zone, par exemple « contoso.com », puis sélectionnez **Ajouter un domaine**. Veillez à inclure .com, .net ou une autre extension de niveau supérieur.
6. Dans le panneau ***NomDomaine*** (dont le titre comprend le nom du domaine personnalisé), obtenez les informations DNS à utiliser pour vérifier que votre organisation est propriétaire du nom de domaine personnalisé.
   
   ![obtenir les informations d’entrée DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> Si vous envisagez de fédérer votre instance locale de Windows Server Active Directory avec Azure AD, vous devez cocher la case **Je prévois de configurer ce domaine pour l’authentification unique avec mon annuaire Active Directory local** lorsque vous exécutez l’outil Azure AD Connect pour synchroniser vos annuaires. Vous devez également inscrire le même nom de domaine que celui que vous comptez fédérer avec votre annuaire local lors de l’étape **Domaine Azure AD** de l’Assistant. Vous pouvez voir à quoi cette étape de l’assistant ressemble [dans ces instructions](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Si vous ne disposez pas de l’outil Azure AD Connect, vous pouvez [le télécharger ici](http://go.microsoft.com/fwlink/?LinkId=615771).

Maintenant que vous avez ajouté le nom de domaine, Azure AD doit vérifier que votre organisation possède le nom de domaine. Pour qu’Azure AD puisse effectuer cette vérification, vous devez ajouter une entrée DNS dans le fichier de zone DNS pour le nom de domaine. Cette tâche peut être effectuée sur le site web pour le bureau d’enregistrement de noms de domaine associé au nom de domaine.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Ajouter l’entrée DNS pour le domaine au niveau du bureau d’enregistrement de noms de domaine
L’étape suivante pour utiliser votre nom de domaine personnalisé avec Azure AD consiste à mettre à jour le fichier de zone DNS pour le domaine. Azure AD peut ainsi vérifier que votre organisation est propriétaire du nom de domaine personnalisé.

1. Connectez-vous au Bureau d’enregistrement des noms de domaine pour le domaine. Si vous n’avez pas accès à ce bureau pour mettre à jour l’entrée DNS, demandez à la personne ou à l’équipe disposant de cet accès d’exécuter l’étape 2 et de vous avertir une fois l’opération effectuée.
2. Mettez à jour le fichier de zone DNS pour le domaine en ajoutant l’entrée DNS fournie par Azure AD. Cette entrée DNS permet à Azure AD de vérifier que vous êtes le propriétaire du domaine. Elle ne modifiera aucun comportement comme le routage du courrier ou l’hébergement web.

Pour plus d’informations sur cette procédure d’ajout de l’entrée DNS, voir [Instructions pour ajouter une entrée DNS à des Bureaux d’enregistrement DNS courants](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Vérifier le nom de domaine avec Azure AD.
Une fois que vous avez ajouté l’entrée DNS, vous êtes prêt à vérifier le nom de domaine avec Azure AD.

Le nom de domaine peut être vérifié uniquement une fois les enregistrements DNS propagés. Cette propagation ne prend généralement que quelques secondes, mais peut parfois nécessiter une heure ou davantage. Si la vérification ne fonctionne pas la première fois, réessayez ultérieurement.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour l’annuaire.
2. Sélectionnez **Parcourir**, saisissez Gestion des utilisateurs dans la zone de texte, puis sélectionnez **Entrée**.
   
   ![Ouvrir la gestion des utilisateurs](./media/active-directory-domains-add-azure-portal/user-management.png)
3. Dans le panneau **Gestion des utilisateurs - Noms de domaine** , sélectionnez le nom de domaine non vérifié que vous souhaitez vérifier.
4. Dans le panneau ***NomDomaine*** (autrement dit, le panneau qui s’ouvre avec votre nouveau nom de domaine en titre), sélectionnez **Vérifier** pour terminer la vérification.

> [!TIP]
> Vous pouvez ajouter jusqu’à 900 noms de domaine personnalisés. Toutefois, seul un nom de domaine peut être [défini en tant que nom de domaine principal de votre annuaire Azure AD](active-directory-domains-manage-azure-portal.md#set-the-primary-domain-name-for-your-azure-ad-directory) et utilisé par défaut lorsque vous créez des comptes.

Vous pouvez désormais créer des comptes d’utilisateur basés sur le cloud et mettre à jour des informations de compte d’utilisateur local précédemment synchronisées, à l’aide de votre nom de domaine personnalisé. Vous pouvez également modifier les informations de suffixe de domaine de compte d’utilisateur précédemment synchronisées à l’aide de [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) ou de [l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## <a name="troubleshooting"></a>Résolution des problèmes
Si vous ne parvenez pas à vérifier un nom de domaine personnalisé, essayez les étapes de dépannage suivantes :

1. **Attendez une heure**. Les enregistrements DNS doivent être propagés avant qu’Azure AD puisse vérifier le domaine. Cette opération peut prendre une heure ou davantage.
2. **Vérifiez que l’enregistrement DNS a été entré et qu’il est correct**. Exécutez cette tâche sur le site web du bureau d’enregistrement de noms de domaine correspondant au domaine. Azure AD ne peut pas vérifier le nom de domaine si l’entrée DNS est absente du fichier de zone DNS ou qu’elle ne correspond pas exactement à l’entrée DNS qu’Azure AD vous a fournie. Si vous ne disposez pas d’un accès pour mettre à jour les enregistrements DNS du domaine au niveau du bureau d’enregistrement de noms de domaine, partagez l’entrée DNS avec la personne ou l’équipe de votre organisation qui possède cet accès et demandez-lui d’ajouter cette entrée.
3. **Supprimez le nom de domaine d’un autre annuaire dans Azure AD**. Un nom de domaine ne peut être vérifié que dans un seul annuaire. Si un nom de domaine a été précédemment vérifié dans un autre annuaire, vous devez le supprimer avant de pouvoir le vérifier dans votre nouvel annuaire. Pour plus d’informations sur la suppression des noms de domaine, voir [Gérer les noms de domaine personnalisé](active-directory-domains-manage-azure-portal.md).    

## <a name="add-more-custom-domain-names"></a>Ajouter des noms de domaines personnalisés
Si votre organisation utilise plusieurs noms de domaine personnalisés, tels que « contoso.com » ou « contosobank.com », vous pouvez ajouter jusqu’à 900 noms de domaine en répétant les étapes décrites dans cet article.

### <a name="learn-more"></a>En savoir plus
[Présentation conceptuelle des noms de domaine personnalisés dans Azure AD](active-directory-add-domain-concepts.md)

[Gérer les noms de domaine personnalisés](active-directory-domains-manage-azure-portal.md)


## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez appris comment ajouter un domaine personnalisé à Azure AD. 

Vous pouvez utiliser le lien suivant pour ajouter un nouveau domaine personnalisé dans Azure AD à partir du portail Azure.

> [!div class="nextstepaction"]
> [Ajouter un domaine personnalisé](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 
