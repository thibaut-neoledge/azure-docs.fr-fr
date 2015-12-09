<properties
	pageTitle="Administration de votre annuaire Azure AD | Microsoft Azure"
	description="Cette rubrique explique ce qu’est un client Azure AD et comment gérer un annuaire Azure AD"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	writer="markvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="12/01/2015"
	ms.author="markvi"/>

# Administration de votre annuaire Azure AD

## Qu'est-ce qu'un locataire Azure AD ?

Dans le monde réel, le terme « client » peut être défini comme un groupe ou une société qui occupe un immeuble. Par exemple, votre organisation possède probablement des bureaux dans un immeuble. Cet immeuble se trouve peut-être dans une rue, entouré de nombreuses autres organisations. Votre organisation est dans ce cas considérée comme l’occupant des lieux. Cet immeuble est un actif de votre organisation qui vous permet d’exercer votre activité en toute sécurité. Il définit les limites entre votre entreprise et les autres entreprises de votre rue. Autrement dit, il vous permet d’isoler votre organisation et ses actifs des autres organisations.

Dans le contexte du cloud, un client peut être défini comme un client ou une organisation qui possède et gère une instance spécifique de ce service cloud. Avec la plateforme d’identité fournie par Microsoft Azure, un client consiste simplement en une instance dédiée d’Azure Active Directory (Azure AD) que votre organisation reçoit et possède quand elle s’inscrit à un service cloud Microsoft tel qu’Azure ou Office 365.

Chaque annuaire Azure AD est distinct et indépendant des autres annuaires Azure AD. De la même manière qu’un immeuble de bureaux d’entreprise est un élément d’actif sûr propre à votre organisation, un annuaire Azure AD est également conçu pour être un élément d’actif sûr pour les seuls besoins de votre organisation. Par son architecture, Azure AD isole les données et les informations d’identité des clients, ce qui évite tout risque de brassage. Autrement dit, les utilisateurs et les administrateurs d’un annuaire Azure AD ne peuvent pas accéder aux données d’un autre annuaire par accident ou malveillance.

![][1]

## Comment puis-je obtenir un annuaire Azure AD ?

Azure AD fournit l’annuaire principal et les fonctionnalités de gestion d’identité qui existent derrière la plupart des services cloud Microsoft, y compris :

- Les tables Azure
- Microsoft Office 365
- Microsoft Dynamics CRM Online
- Microsoft Intune

Quand vous vous inscrivez à ces services cloud Microsoft, vous obtenez un annuaire Azure AD. Vous pouvez créer des annuaires supplémentaires en fonction des besoins. Par exemple, vous pouvez mettre à jour votre premier annuaire en tant qu’annuaire de production, puis créer un autre annuaire pour les tests ou l’environnement intermédiaire.

> [AZURE.NOTE]Une fois inscrit au premier service, nous vous recommandons d’utiliser le compte d’administrateur associé à votre organisation au moment de vous inscrire à d’autres services cloud Microsoft.

Quand vous vous inscrivez à un service cloud Microsoft, vous êtes invité à fournir des détails sur votre organisation et sur l’inscription de son nom de domaine Internet. C’est sur la base de ces informations qu’est créée une nouvelle instance d’annuaire Azure AD pour votre organisation. Ce même annuaire sert à authentifier les tentatives de connexion quand vous vous abonnez à plusieurs services cloud Microsoft.

Les services supplémentaires tirent pleinement parti des comptes d’utilisateurs, des stratégies, des paramètres ou de l’intégration d’annuaire locale existants, que vous configurez pour améliorer l’efficacité entre l’infrastructure d’identité locale de votre organisation et Azure AD.

Par exemple, si vous avez initialement souscrit un abonnement Microsoft Intune et exécuté les étapes nécessaires pour mieux intégrer votre service Active Directory local à votre annuaire Azure AD en déployant la synchronisation d’annuaires et/ou des serveurs d’authentification unique, vous pouvez vous inscrire à un autre service cloud Microsoft comme Office 365, qui peut également bénéficier des mêmes avantages liés à l’intégration d’annuaire que ceux dont vous bénéficiez actuellement avec Microsoft Intune.

Pour plus d’informations sur l’intégration de votre annuaire local à Azure AD, consultez la rubrique [Intégration d’annuaire](active-directory-aadconnect.md).

### Association d’un annuaire Azure AD à un abonnement Azure

Vous pouvez associer un nouvel abonnement Azure au même annuaire qui authentifie les connexions pour un abonnement Office 365 ou Microsoft Intune existant. Connectez-vous au portail de gestion Azure à l’aide de votre compte professionnel ou scolaire. Le portail de gestion Azure vous renvoie un message pour indiquer qu’il n’a trouvé aucun abonnement pour ce compte. Sélectionnez **S’inscrire à Azure**, pour permettre l’administration de votre annuaire dans le portail de gestion. Pour plus d’informations, consultez la rubrique [Gestion de l’annuaire associé à votre abonnement Office 365 dans Azure](active-directory-how-subscriptions-associated-directory.md#manage-the-directory-for-your-office-365-subscription-in-azure).

Pour visualiser une vidéo qui traite de questions courantes sur l’utilisation d’Azure AD, consultez la rubrique [ Azure Active Directory - Questions courantes sur l’inscription, la connexion et l’utilisation.](http://channel9.msdn.com/Series/Windows-Azure-Active-Directory/WAADCommonSignupsigninquestions).

### Création d’un annuaire Azure AD en vous inscrivant à un service cloud Microsoft en tant qu’organisation

Si vous n’êtes pas encore abonné à un service cloud Microsoft, cliquez sur l’un des liens ci-dessous pour vous inscrire. Un annuaire Azure AD est créé automatiquement dès votre inscription à un premier service.

- [Microsoft Azure](https://account.windowsazure.com/organization)
- [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
- [Microsoft Intune](https://account.manage.microsoft.com/Signup/MainSignUp.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&ali=1)

### Gestion d’un annuaire par défaut fourni par Azure

Aujourd’hui, un annuaire est créé automatiquement quand vous vous inscrivez à Azure et que votre abonnement est associé à cet annuaire. Mais, si vous vous êtes inscrit à Azure avant octobre 2013, un annuaire n’a pas été créé automatiquement. Dans ce cas, Azure peut avoir « remblayé » votre compte en configurant un annuaire par défaut pour ce dernier. Dans ce cas, votre abonnement a été associé à cet annuaire par défaut.

Ce processus a été instauré en octobre 2013 dans le cadre d’une amélioration globale du modèle de sécurité pour Azure. Il permet d’offrir des fonctionnalités d’identité professionnelle à tous les clients Azure, et garantit que toutes les ressources Azure sont accessibles uniquement aux utilisateurs figurant dans l’annuaire. Vous ne pouvez pas utiliser Azure sans annuaire. Pour cela, tout utilisateur s’étant inscrit avant le 7 juillet 2013 mais ne disposant pas d’annuaire devait en créer un. Si vous aviez déjà créé un annuaire, votre abonnement était associé à celui-ci.

L’utilisation d’Azure AD est gratuite. L’annuaire est une ressource gratuite. Il existe un niveau Azure Active Directory Premium supplémentaire qui fait l’objet d’une licence séparée, et fournit des fonctionnalités supplémentaires telles que la marque de société et la réinitialisation de mot de passe libre-service.

Pour modifier le nom complet de votre annuaire, cliquez sur celui-ci dans le portail de gestion, puis cliquez sur **Configurer**. Comme expliqué plus loin dans cette rubrique, vous pouvez ajouter ou supprimer un annuaire. Pour associer votre abonnement à un autre annuaire, cliquez sur l’extension **Paramètres** dans le volet gauche de navigation, et au bas de la page **Abonnements**, cliquez sur **Modifier l’annuaire**. Vous pouvez également créer un domaine personnalisé à l’aide d’un nom DNS que vous avez enregistré à la place du domaine par défaut *.onmicrosoft.com, ce qui peut être préférable avec un service tel que SharePoint Online.

## Comment puis-je gérer les données d’un annuaire ?

En tant qu’administrateur d’un ou de plusieurs abonnements de service cloud Microsoft, vous pouvez utiliser le portail de gestion Azure, le portail de compte Microsoft Intune ou le centre d’administration Office 365 pour gérer les données d’annuaire de votre entreprise. Vous pouvez également télécharger et exécuter les applets de commande du [Module Microsoft Azure Active Directory pour Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) pour vous aider à gérer les données stockées dans Azure AD.

À partir de ces portails (ou applets de commande), vous pouvez :

- créer et gérer des comptes d’utilisateurs et de groupe ;
- gérer le ou les services cloud apparentés auxquels votre organisation s’est abonnée ;
- configurer une intégration locale à votre service d’annuaire.

Le portail de gestion Azure, le Centre d’administration Office 365, le portail de compte Microsoft Intune et les applets de commande Azure AD peuvent tous lire et écrire dans une instance partagée unique d’Azure AD associée à l’annuaire de votre organisation, comme le montre l’illustration suivante. Les portails (ou applets de commande) agissent ainsi comme une interface frontale qui extrait et/ou modifie vos données d’annuaire.

![][2]

Les portails de compte cités ci-dessus et les applets de commande Azure AD PowerShell associées utilisés pour gérer les utilisateurs et les groupes s’appuient sur la plateforme Azure AD.

Quand vous modifiez les données de votre organisation via l’un de ces portails (ou applets de commande) tout en étant connecté dans le cadre de l’un de ces services, la modification est aussi visible dans les autres portails lors de votre prochaine connexion dans le cadre de ce service, car ces données sont partagées entre les services cloud Microsoft auxquels vous êtes abonné. Par exemple, si vous avez utilisé le Centre d’administration Office 365 pour bloquer un utilisateur, cette action l’empêche de se connecter aux autres services auxquels votre organisation est actuellement abonnée. Si vous consultez ensuite le compte de cet utilisateur dans le contexte du portail de compte Microsoft Intune, vous verrez qu’il est bloqué.

## Comment puis-je ajouter et gérer plusieurs annuaires ?

Vous pouvez ajouter un annuaire Azure AD dans le portail de gestion Azure. Sélectionnez l’extension **Active Directory** à gauche et cliquez sur **Ajouter**.

Vous pouvez gérer chaque annuaire comme une ressource entièrement indépendante : chaque annuaire est un homologue complet et logiquement indépendant des autres annuaires que vous gérez ; il n’existe aucune relation parent-enfant entre les annuaires. Cette indépendance entre les annuaires vaut pour les ressources, l’administration et la synchronisation.

- **Indépendance des ressources**. Si vous créez ou supprimez une ressource dans un annuaire, cela n’a aucun effet sur les ressources d’un autre annuaire, si l’on excepte le cas des utilisateurs externes, comme décrit ci-dessous. Si vous utilisez un domaine personnalisé (par exemple, « contoso.com ») pour un annuaire, il ne peut être utilisé avec aucun autre annuaire.
- **Indépendance de l’administration**. Si un utilisateur non administrateur de l’annuaire « Contoso » crée l’annuaire de test « Test », alors :
    - ◦l’outil de synchronisation d’annuaires, pour synchroniser les données avec une seule forêt Active Directory ;
    - ◦les administrateurs de l’annuaire « Contoso » n’ont pas de privilèges d’administration directs sur l’annuaire « Test », à moins qu’un administrateur de « Test » leur ait spécifiquement accordé ces privilèges. Les administrateurs de « Contoso » peuvent contrôler l’accès à l’annuaire « Test » en vertu du contrôle qu’ils exercent sur le compte d’utilisateur qui a créé « Test ».

    Et si vous modifiez le rôle d’administrateur d’un utilisateur dans un annuaire (ou que vous l’ajoutez ou le modifiez), la modification n’affecte pas le rôle d’administrateur que l’utilisateur peut avoir dans un autre annuaire.


- **Indépendance de la synchronisation**. Vous pouvez configurer chaque annuaire Azure AD de manière indépendante pour obtenir la synchronisation des données depuis une même instance de l’un des éléments suivants :
    - l’outil de synchronisation d’annuaires, pour synchroniser les données avec une seule forêt Active Directory ;
    - le connecteur Azure Active Directory pour Forefront Identity Manager, pour synchroniser les données avec une ou plusieurs forêts locales et/ou des sources de données non AD.

De même, sachez qu’à la différence des autres ressources Azure, vos annuaires ne sont pas des ressources enfants d’un abonnement Azure. Ainsi, si vous annulez votre abonnement ou si vous le laissez expirer, vous pouvez toujours accéder à vos données d’annuaire à l’aide d’Azure AD PowerShell, de l’API Graph Azure ou d’autres interfaces, telles que le Centre d’administration Office 365. Vous pouvez également associer un autre abonnement à l’annuaire.

## Comment puis-je supprimer un annuaire Azure AD ?
Un administrateur général peut supprimer un annuaire Azure AD à partir du portail. Quand un annuaire est supprimé, toutes les ressources qu’il contient sont également supprimées ; vous devez donc vous assurer que vous n’avez pas besoin de l’annuaire avant de le supprimer.

> [AZURE.NOTE]Si l’utilisateur s’est connecté avec un compte professionnel ou scolaire, il ne doit pas essayer de supprimer son annuaire de base. Par exemple, si l’utilisateur s’est connecté avec le compte joe@contoso.onmicrosoft.com, il ne peut pas supprimer l’annuaire dont le domaine par défaut est contoso.onmicrosoft.com.

### Conditions qui doivent être remplies pour supprimer un annuaire Azure AD

Azure AD nécessite que certaines conditions soient remplies pour supprimer un annuaire. Cela limite le risque que la suppression d’un annuaire n’affecte les utilisateurs ou les applications, avec par exemple l’incapacité pour les utilisateurs de se connecter à Office 365 ou d’accéder à des ressources dans Azure. Par exemple, en cas de suppression involontaire d’un annuaire associé à un abonnement, les utilisateurs ne pourraient plus accéder aux ressources Azure pour cet abonnement.

Les conditions à remplir sont les suivantes :

- Il ne reste qu’un seul utilisateur dans l’annuaire : l’administrateur général chargé de supprimer l’annuaire. Tous les autres utilisateurs doivent être supprimés avant de pouvoir supprimer le répertoire. Si les utilisateurs sont synchronisés en local, la synchronisation doit être désactivée et les utilisateurs supprimés dans l’annuaire cloud via le portail de gestion ou le module Azure pour Windows PowerShell. Il n’est pas nécessaire de supprimer des groupes ou des contacts, tels que les contacts ajoutés à partir du centre d’administration Office 365.
- Aucune application ne doit se trouver dans l’annuaire. Elles doivent toutes être supprimées avant de pouvoir supprimer l’annuaire.
- L’annuaire ne doit être associé à aucun abonnement Microsoft Online Services, tel que Microsoft Azure, Office 365 ou Azure AD. Par exemple, si un annuaire par défaut a été créé pour vous dans Azure, vous ne pouvez pas le supprimer si votre abonnement Azure utilise toujours cet annuaire à des fins d’authentification. De même, vous ne pouvez pas supprimer un annuaire auquel un autre utilisateur a associé un abonnement. Pour associer votre abonnement à un autre annuaire, connectez-vous au portail de gestion Azure et cliquez sur **Paramètres** dans le volet de navigation gauche. Puis au bas de la page **Abonnements**, cliquez sur **Modifier l’annuaire**. Pour plus d’informations sur les abonnements Azure, consultez la page [Comment sont associés les abonnements Azure et Azure AD ?](active-directory-how-subscriptions-associated-directory.md).

    > [AZURE.NOTE]Si l’utilisateur s’est connecté avec un compte professionnel ou scolaire, il ne doit pas essayer de supprimer son annuaire de base. Par exemple, si l’utilisateur s’est connecté avec le compte joe@contoso.onmicrosoft.com, il ne peut pas supprimer l’annuaire dont le domaine par défaut est contoso.onmicrosoft.com.

- L’annuaire ne doit être lié à aucun fournisseur d’authentification Multi-Factor Authentication.


## Ressources supplémentaires

- [Forum Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
- [Forum Azure Multi-Factor Authentication](https://social.msdn.microsoft.com/Forums/home?forum=windowsazureactiveauthentication)
- [Stackoverflow](http://stackoverflow.com/questions/tagged/azure)
- [Inscription à Azure en tant qu’organisation](sign-up-organization.md)
- [Gestion d’Azure AD à l’aide de Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
- [Attribution de rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-administer/aad_portals.png
[2]: ./media/active-directory-administer/azure_tenants.png

<!---HONumber=Nov15_HO4-->