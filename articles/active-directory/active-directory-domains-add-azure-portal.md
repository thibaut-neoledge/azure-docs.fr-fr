<properties
	pageTitle="Ajout de votre nom de domaine personnalisé dans la version préliminaire d’Azure Active Directory | Microsoft Azure"
	description="Comment ajouter les noms de domaine de votre société à Azure Active Directory et comment vérifier le nom de domaine."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="curtand"/>

# Ajouter un nom de domaine personnalisé dans la version préliminaire d’Azure Active Directory

Votre organisation utilise un ou plusieurs noms de domaine pour mener ses activités, et vos utilisateurs se connectent au réseau d’entreprise en utilisant votre nom de domaine d’entreprise. Maintenant que vous utilisez la version préliminaire d’Azure Active Directory (Azure AD), vous pouvez également ajouter votre nom de domaine d’entreprise à ce dernier. [Nouveautés de la version préliminaire](active-directory-preview-explainer.md) Cette tâche vous permet d’attribuer des noms de répertoire qui sont familiers à vos utilisateurs, comme « alice@contoso.com ». Le processus est simple :

1. Ajoutez le nom de domaine personnalisé à votre annuaire.
2. Ajoutez une entrée DNS pour le nom de domaine au niveau du bureau d’enregistrement de noms de domaine.
3. Vérifiez le nom de domaine personnalisé dans Azure AD.

## Comment ajouter un nom de domaine ?

1.  Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.

2.  Sélectionnez **Plus de services**, saisissez **Azure Active Directory** dans la zone de texte, puis sélectionnez **Entrée**.

    ![Ouvrir la gestion des utilisateurs](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Dans le panneau ***nom-répertoire***, sélectionnez **Noms de domaine**.

4. Dans le panneau ***nom-répertoire* - Noms de domaine**, sélectionnez a commande **Ajouter**.

  ![Sélection de la commande Ajouter](./media/active-directory-domains-add-azure-portal/add-command.png)

5. Dans le panneau **Nom de domaine**, saisissez le nom de votre domaine personnalisé dans la zone, par exemple « contoso.com », puis sélectionnez **Ajouter un domaine**. Veillez à inclure .com, .net ou une autre extension de niveau supérieur.

6. Dans le panneau ***NomDomaine*** (autrement dit, le panneau qui s’ouvre avec votre nouveau nom de domaine en titre), obtenez les informations DNS utilisées par Azure AD pour vérifier que votre organisation possède le nom de domaine personnalisé.

  ![obtenir les informations d’entrée DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

Maintenant que vous avez ajouté le nom de domaine, Azure AD doit vérifier que votre organisation possède le nom de domaine. Pour qu’Azure AD puisse effectuer cette vérification, vous devez ajouter une entrée DNS dans le fichier de zone DNS pour le nom de domaine. Cette tâche peut être effectuée sur le site web pour le bureau d’enregistrement de noms de domaine associé au nom de domaine.

## Ajouter l’entrée DNS pour le domaine au niveau du bureau d’enregistrement de noms de domaine

L’étape suivante pour utiliser votre nom de domaine personnalisé avec Azure AD consiste à mettre à jour le fichier de zone DNS pour le domaine. Cette opération permet à Azure AD de vérifier que votre organisation possède le nom de domaine personnalisé.

1.  Connectez-vous au Bureau d’enregistrement des noms de domaine pour le domaine. Si vous n’avez pas accès à ce bureau pour mettre à jour l’entrée DNS, demandez à la personne ou à l’équipe disposant de cet accès d’exécuter l’étape 2 et de vous avertir une fois l’opération effectuée.

2.  Mettez à jour le fichier de zone DNS pour le domaine en ajoutant l’entrée DNS fournie par Azure AD. Cette entrée DNS permet à Azure AD de vérifier que vous êtes le propriétaire du domaine. Elle ne modifiera aucun comportement comme le routage du courrier ou l’hébergement web.

Pour plus d’informations sur cette procédure d’ajout de l’entrée DNS, voir [Instructions pour ajouter une entrée DNS à des Bureaux d’enregistrement DNS courants](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Vérifier le nom de domaine avec Azure AD.

Une fois que vous avez ajouté l’entrée DNS, vous êtes prêt à vérifier le nom de domaine avec Azure AD.

Le nom de domaine peut être vérifié uniquement une fois les enregistrements DNS propagés. Cette propagation ne prend généralement que quelques secondes, mais peut parfois nécessiter une heure ou davantage. Si la vérification ne fonctionne pas la première fois, réessayez ultérieurement.

1.  Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.

2.  Sélectionnez **Parcourir**, saisissez Gestion des utilisateurs dans la zone de texte, puis sélectionnez **Entrée**.

    ![Ouvrir la gestion des utilisateurs](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Dans le panneau **Gestion des utilisateurs - Noms de domaine**, sélectionnez le nom de domaine non vérifié que vous souhaitez vérifier.

4. Dans le panneau ***NomDomaine*** (autrement dit, le panneau qui s’ouvre avec votre nouveau nom de domaine en titre), sélectionnez **Vérifier** pour terminer la vérification.

Vous pouvez désormais [affecter des noms d’utilisateur incluant votre nom de domaine personnalisé](active-directory-create-users-azure-portal.md).

## Résolution de problèmes

Si vous ne parvenez pas à vérifier un nom de domaine personnalisé, essayez d’effectuer les opérations ci-après. Nous irons de la plus courante à la moins courante.

1.	**Attendez une heure**. Les enregistrements DNS doivent être propagés avant qu’Azure AD puisse vérifier le domaine. Cette opération peut prendre une heure ou davantage.

2.	**Vérifiez que l’enregistrement DNS a été entré et qu’il est correct**. Exécutez cette tâche sur le site web du bureau d’enregistrement de noms de domaine correspondant au domaine. Azure AD ne peut pas vérifier le nom de domaine si l’entrée DNS est absente du fichier de zone DNS ou qu’elle ne correspond pas exactement à l’entrée DNS qu’Azure AD vous a fournie. Si vous ne disposez pas d’un accès pour mettre à jour les enregistrements DNS du domaine au niveau du bureau d’enregistrement de noms de domaine, partagez l’entrée DNS avec la personne ou l’équipe de votre organisation qui possède cet accès et demandez-lui d’ajouter cette entrée.

3.	**Supprimez le nom de domaine d’un autre annuaire dans Azure AD**. Un nom de domaine ne peut être vérifié que dans un seul annuaire. Si un nom de domaine a été précédemment vérifié dans un autre annuaire, vous devez le supprimer avant de pouvoir le vérifier dans votre nouvel annuaire. Pour plus d’informations sur la suppression des noms de domaine, voir [Gérer les noms de domaine personnalisé](active-directory-domains-manage-azure-portal.md).

## Ajouter des noms de domaines personnalisés

Si votre organisation utilise plusieurs noms de domaine personnalisés, tels que « contoso.com » et « contosobank.com », vous pouvez les ajouter (le nombre maximal autorisé de noms de domaine est de 900). Suivez la procédure décrite dans cet article pour ajouter chacun de ces noms de domaine.

## Étapes suivantes

[Gérer les noms de domaine personnalisés](active-directory-domains-manage-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->