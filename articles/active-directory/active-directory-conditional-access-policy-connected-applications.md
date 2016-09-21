<properties
	pageTitle="Comment définir la stratégie d’accès conditionnel en fonction de l’appareil Azure Active Directory pour contrôler l’accès aux applications connectées Azure Active Directory"
	description="Explique comment les administrateurs informatiques peuvent définir des stratégies d’accès conditionnel en fonction de l’appareil pour les applications connectées à Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/09/2016"
	ms.author="markvi"/>


# Comment définir la stratégie d’accès conditionnel en fonction de l’appareil Azure Active Directory pour contrôler l’accès aux applications connectées Azure Active Directory 


L’accès conditionnel en fonction de l’appareil à Azure Active Directory vous permet de protéger les ressources organisationnelles des risques suivants :

- Accès à partir d’appareils inconnus/non gérés.
- Appareils non conformes aux stratégies de sécurité définies par votre organisation.

Pour une vue d’ensemble de l’accès conditionnel, consultez [Accès conditionnel Azure Active Directory](active-directory-conditional-access.md).

Vous pouvez définir les stratégies d’accès conditionnel en fonction de l’appareil pour protéger les éléments suivants :

- Office 365 SharePoint Online pour protéger les documents et les sites organisationnels.

- Office 365 Exchange Online pour protéger la messagerie organisationnelle.

- Applications SaaS connectées à Azure AD pour l’authentification.

- Applications locales publiées via le proxy d’application Azure AD.


Dans le portail de gestion Azure, vous pouvez définir cette stratégie en accédant à l’application spécifique dans le répertoire.

 
  ![Applications](./media/active-directory-conditional-access-policy-connected-applications/01.png "Applications")


Après avoir sélectionné l’application, cliquez sur l’onglet **Configurer** pour définir la stratégie d’accès conditionnel.


  ![Règles d’accès basées sur les appareils](./media/active-directory-conditional-access-policy-connected-applications/02.png "Règles d’accès basées sur les appareils")


 

Pour activer une stratégie d’accès conditionnel en fonction de l’appareil, dans la section **Règles d’accès basé sur l’appareil**, pour **Activer les règles d’accès**, sélectionnez **Activé**.

Cette stratégie est constituée de trois composants :

1. **S’applique à** - utilisateurs auxquels cette stratégie s’applique lors de l’accès à l’application.

2. **Règles d’appareil** - conditions requises auxquelles les appareils doivent répondre avant d’accéder à l’application.

3. **Mise en œuvre de l’application** - applications clientes (natives et de navigateur) pour lesquelles la stratégie doit être évaluée.

  ![Règles d’accès basées sur les appareils](./media/active-directory-conditional-access-policy-connected-applications/03.png "Règles d’accès basées sur les appareils")
 

## Définir les utilisateurs auxquels la stratégie s’applique 

Dans la section **S’applique à**, vous pouvez sélectionner les utilisateurs auxquels s’applique cette stratégie.

Vous disposez de deux options :

- **Tous les utilisateurs** - tout le monde peut accéder à l’application

- **Groupes** - pour limiter l’accès aux groupes et aux utilisateurs membres d’un groupe.

![S’applique à](./media/active-directory-conditional-access-policy-connected-applications/11.png "S’applique à")


En sélectionnant **Sauf**, vous pouvez exclure des utilisateurs de cette stratégie lors de l’accès à l’application. Cela est utile lorsque vous avez besoin d’activer des utilisateurs spécifiques devant accéder temporairement à l’application. Sélectionnez cette option, si par exemple certains de vos utilisateurs possèdent des appareils qui ne sont pas prêts pour l’accès conditionnel (pas encore inscrits, récemment déclarés conformes, etc..).
 

## Sélectionner les conditions que les appareils doivent satisfaire 

Avec **Règles d’appareil**, vous définissez les conditions pour que les appareils puissent accéder à l’application.

Pour l’accès conditionnel en fonction de l’appareil, les appareils suivants sont pris en charge :

- Mise à jour anniversaire Windows 10, Windows 7 et Windows 8.1.

- Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 et Windows Server 2008 R2.

- Appareils iOS (iPad, iPhone).

- Appareils Android

La prise en charge pour Mac sera bientôt disponible.

  ![Appareils](./media/active-directory-conditional-access-policy-connected-applications/04.png "Applications")



 >[AZURE.NOTE] Pour plus d’informations sur les différences entre la jonction de domaine et la jonction Azure AD, consultez [Utilisation d’appareils Windows 10 sur votre lieu de travail](active-directory-azureadjoin-windows10-devices.md).


Vous disposez de deux options pour les règles d’appareil :

- **Tous les appareils doivent être conformes** - cela nécessite que toutes les plateformes d’appareil aient accès à l’application pour être conformes. Pour les plates-formes qui ne prennent pas en charge l’accès conditionnel en fonction de l’appareil, l’accès est interdit aux appareils.

- **Seuls les appareils sélectionnés doivent être conformes** - cela nécessite que seules les plateformes d’appareil soient conformes. Les plates-formes non sélectionnées ou les autres plateformes accédant à l’application sont autorisées.

  ![Applications](./media/active-directory-conditional-access-policy-connected-applications/05.png "Applications")



Les appareils joints à Azure AD sont compatibles s’ils sont désignés comme **compatible** dans le répertoire par Microsoft Intune ou un système MDM tiers qui s’intègre à Azure AD.

Les appareils joints à un domaine sont compatibles de deux manières :

- S’ils sont inscrits auprès d’Azure AD, le fait qu’ils soient joints à un domaine les rend alors fiables aux yeux de nombreuses organisations.

- S’ils sont marqués comme conformes dans Azure AD par System Center Configuration Manager 2016.

 ![Règles d’appareil](./media/active-directory-conditional-access-policy-connected-applications/06.png "Règles d’appareil")
 

Les appareils personnels Windows sont compatibles s’ils sont désignés comme **compatible** dans le répertoire par Microsoft Intune ou un système MDM tiers qui s’intègre à Azure AD.

Les appareils non Windows sont compatibles s’ils sont désignés comme **compatible** dans le répertoire par Microsoft Intune.

 >[AZURE.NOTE] Pour plus d’informations sur la configuration d’Azure AD pour la compatibilité des appareils par le système de gestion, consultez [Accès conditionnel Azure Active Directory](active-directory-conditional-access.md).


Lorsque vous sélectionnez des plateformes d’appareil spécifiques, vous pouvez sélectionner une ou plusieurs options, y compris Android, iOS, Windows Mobile (téléphones et tablettes Windows 8.1) et Windows (tous les autres appareils Windows, y compris les appareils Windows 10). Avec cette option, l’évaluation de stratégie a lieu uniquement sur les plateformes sélectionnées. En cas de tentatives d’accès à partir d’un appareil qui ne fait pas partie de la sélection, aucune stratégie d’appareil n’est évaluée et l’appareil est autorisé si l’utilisateur l’est.

![Règles d’appareil](./media/active-directory-conditional-access-policy-connected-applications/07.png "Règles d’appareil")
  

## Sélectionner quel type d’application client est évalué sous quelle stratégie 

Dans la section **Mise en œuvre de l’application**, vous définissez le type d’application pour lequel la stratégie doit être évaluée.


Vous disposez de deux options pour les applications :

- Pour les applications de navigateur ou natives.
- Pour les applications natives uniquement.


![Applications](./media/active-directory-conditional-access-policy-connected-applications/08.png "Applications")


En sélectionnant **Pour les applications de navigateur ou natives**, vous appliquez la stratégie d’accès aux applications via :

- Les navigateurs (par exemple, Edge dans Windows 10, Safari dans iOS, etc.).
- Les applications utilisant la bibliothèque d’authentification Active Directory (ADAL) sur n’importe quelle plate-forme ou l’API de gestionnaire de compte web (WAM) sous Windows 10.

>[AZURE.NOTE] Pour plus d’informations sur la prise en charge du navigateur et autres considérations pour l’utilisateur final accédant à des applications protégées par l’accès conditionnel en fonction de l’appareil, consultez [Accès conditionnel Azure Active Directory](active-directory-conditional-access.md).

 

## Protection de l’accès à la messagerie depuis les applications Exchange Active Sync 

Dans les applications Office 365 Exchange Online, vous disposez d’une section supplémentaire appelée **Exchange Activesync**. Cette section vous permet de bloquer l’accès à la messagerie pour les applications de messagerie Exchange Active Sync.

![Applications](./media/active-directory-conditional-access-policy-connected-applications/09.png "Applications")
 
![Applications](./media/active-directory-conditional-access-policy-connected-applications/10.png "Applications")

 
## Rubriques supplémentaires

- [Accès conditionnel Azure Active Directory](active-directory-conditional-access.md)

<!---HONumber=AcomDC_0907_2016-->