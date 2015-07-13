<properties 
	pageTitle="Prise en main d’Azure Active Directory Premium" 
	description="Une rubrique qui explique comment s'inscrire à l’édition Azure Active Directory Premium." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>

# Prise en main d’Azure Active Directory Premium

Azure Active Directory est disponible en trois éditions : gratuit, de base et Premium. L'édition gratuite est comprise dans un abonnement Azure ou Office 365. Les éditions de base et Premium sont disponibles via un [Accord Entreprise de Microsoft](https://www.microsoft.com/fr-fr/licensing/licensing-programs/enterprise.aspx) ou programme de[licences en volume ouvert](https://www.microsoft.com/fr-fr/licensing/licensing-programs/open-license.aspx). Les abonnés Azure et Office 365 peuvent également acheter Active Directory Premium en ligne. [Connectez-vous ici](https://portal.office.com/Commerce/Catalog.aspx) pour l'acheter.

> [AZURE.NOTE]Les clients vivant en Chine peuvent accéder aux éditions de base et Premium d’Azure Active Directory à l’aide de l’instance mondiale d’Azure Active Directory. Actuellement, les éditions De base et Premium d’Azure Active Directory ne sont pas prises en charge dans le service Microsoft Azure utilisé par 21Vianet en Chine. Pour plus d’informations, contactez-nous sur le [forum Azure Active Directory](http://feedback.azure.com/forums/169401-azure-active-directory).

Azure Active Directory Premium est également inclus dans le **Enterprise Mobility Suite**. Enterprise Mobility Suite permet aux organisations de rentabiliser leur utilisation des services Microsoft Intune, Azure Rights Management et Active Directory Premium réunis dans un seul plan de licences. Pour plus d'informations, consultez le site web de [Enterprise Mobility Suite](https://www.microsoft.com/fr-fr/server-cloud/enterprise-mobility/overview.aspx).

Pour commencer à utiliser les fonctionnalités d'Azure Active Directory Premium dès aujourd'hui, suivez les étapes ci-dessous. Ces mêmes étapes s'appliquent à l'édition de base Azure Active Directory.

## Étape 1 : inscrivez-vous à Azure Active Directory Premium

Pour vous inscrire, consultez le site web des [licences en volume](http://www.microsoft.com/fr-fr/licensing/how-to-buy/how-to-buy.aspx).

## Étape 2 : activez votre plan de licences

S'il s'agit de la première fois que vous achetez un plan de licences via le programme de licences en volume Enterprise de Microsoft, vous devez tout d'abord l'activer avant de pouvoir attribuer des licences depuis votre répertoire Azure Active Directory. Pour cela, vous devez cliquer sur le lien de connexion ou d’inscription qui se trouve dans l'e-mail de confirmation (voir ci-dessous) que vous recevrez une fois que vous aurez acheté votre premier plan de licences. Pour tous les achats suivants de ce répertoire, les licences seront automatiquement activées dans ce même répertoire.

![][1]

Si vous avez un client existant, sélectionnez le lien de **connexion** lien pour vous connecter avec votre compte d'administrateur existant. Il est important que vous vous connectiez avec les informations d'identification d'administrateur général à partir du répertoire dans lequel les licences doivent être activées.

Si vous souhaitez créer un nouveau client Azure Active Directory à utiliser avec votre plan de licences, vous devez sélectionner le lien d’**inscription** qui vous permettra d'accéder à l'écran suivant.

![][2]

Une fois que vous avez terminé le processus d'inscription ou de connexion initié à partir de l’e-mail, l’écran suivant s’affichera pour confirmer que le plan de licences est activé pour votre client.

![][3]

## Étapes 3 : activation de votre accès Azure Active Directory

Une fois les licences approvisionnées dans votre répertoire, vous recevrez un e-mail de bienvenue (voir ci-dessous) qui vous confirme que vous pouvez commencer à gérer vos licences et fonctionnalités Azure Active Directory Premium ou Enterprise Mobility Suite. Si vous avez déjà utilisé Microsoft Azure, vous pouvez passer à http://manage.windowsazure.com pour attribuer les nouvelles licences (voir l'étape 4 ci-dessous pour plus d'informations). Si vous débutez avec Microsoft Azure, sélectionnez le lien de connexion dans l’e-mail ou rendez-vous sur la [page d’activation d’accès à Azure Active Directory](https://account.windowsazure.com/signup?offer=MS-AZR-0110P) pour suivre les étapes qui vous permettront d’accéder à votre répertoire via le Portail de gestion Azure.

![][4]

Une fois que vous êtes bien connecté, vous devrez remplir un deuxième écran d’authentification de facteur (ci-dessous) en indiquant un numéro de téléphone mobile et en le validant. Après cela, vous pourrez activer votre accès à Azure Active Directory en sélectionnant **S'inscrire**.

![][5]

L'activation peut prendre quelques minutes, comme illustré ci-dessous. Une fois votre accès activé, la barre marron disparaît et vous pourrez cliquer sur le lien en haut à droite ou accédez au [Portail de gestion Azure](http://manage.windowsazure.com).

![][6]

Dans ce cas, votre accès à Azure sera limité à Azure Active Directory.

![][7]

Vous avez peut-être déjà eu accès à Azure lors d’une précédente utilisation. Par ailleurs, vous pouvez mettre à niveau votre accès à Azure Active Directory pour un accès Azure complet en activant des abonnements Azure supplémentaires. Le cas échéant, le Portail de gestion aura plus de fonctionnalités, comme suit.

![][8]

Si vous essayez d'activer votre accès à Azure Active Directory avant de recevoir l’e-mail de bienvenue ci-dessus, vous pouvez rencontrer le message d'erreur suivant. Veuillez réessayer dans quelques minutes, une fois que vous avez reçu l’e-mail.

![][9]

Les nouveaux administrateurs dans votre abonnement peuvent également activer leur accès au Portail de gestion via ce lien.

## Étape 4 : attribution des licences aux comptes d'utilisateurs

Avant de commencer à utiliser le plan que vous venez d’acheter, vous devrez attribuer manuellement des licences aux comptes d'utilisateurs de votre organisation afin qu'ils puissent utiliser les fonctionnalités fournies avec Premium. Procédez comme dans les étapes suivantes pour attribuer des licences aux utilisateurs pour leur permettre d’utiliser les fonctionnalités Azure Active Directory Premium.

Pour attribuer des licences aux utilisateurs :

1. Connectez-vous au Portail de gestion en tant qu’administrateur général du répertoire que vous souhaitez personnaliser.
2. Cliquez sur **Active Directory** puis sélectionnez le répertoire où vous souhaitez attribuer des licences.
3. Sélectionnez l’onglet **Licences**, sélectionnez **Active Directory Premium** ou **Enterprise Mobility Suite**, puis cliquez sur **Attribuer**.

    ![][10]

4. Dans la boîte de dialogue, sélectionnez les utilisateurs auxquels vous souhaitez attribuer des licences, puis cliquez sur l'icône de coche pour enregistrer les modifications.

    ![][11]

## Restrictions des licences

Certains plans de licences sont des sous-ensembles ou sur-ensembles d’autres plans de licences. Dans la plupart des cas, un utilisateur ne peut pas être affecté à un plan de licences auquel il a déjà été affecté. Si vous souhaitez attribuer un plan de licences qui est un sur-ensemble, vous devez d'abord supprimer le plan de licences de sous-ensemble.

## Conditions de licence :

Lorsque vous attribuez une licence à un utilisateur, vous pouvez spécifier un emplacement d'utilisation principal dans les propriétés de leur compte comme indiqué ci-dessous. Si un emplacement d'utilisation n'est pas spécifié, l’emplacement du client est automatiquement attribué à l'utilisateur.

![][12]

La disponibilité des services et des fonctionnalités du service cloud Microsoft varie selon le pays ou la région. Un service tel que le protocole voix sur IP (VoIP) peut être disponible dans un pays ou une région et pas dans d’autres. Les fonctionnalités dans un service peuvent être limitées pour des raisons juridiques dans certains pays ou régions. Pour voir si un service ou une fonctionnalité est disponible avec ou sans restrictions, recherchez votre pays ou région sur le site des restrictions de licence du service.

## Étapes suivantes

- [Ajout de votre société à vos pages de connexion et du panneau d’accès](active-directory-add-company-branding.md)
- [Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png


 

<!---HONumber=62-->