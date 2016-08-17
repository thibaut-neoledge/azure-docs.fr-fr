<properties
	pageTitle="Prise en main d’Azure Active Directory Premium"
	description="Une rubrique qui explique comment s’inscrire à l’édition Azure Active Directory Premium par le biais du site web du plan de licences en volume."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila" 
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/25/2016"
	ms.author="markvi"/>

# Prise en main d’Azure Active Directory Premium


Vous disposez de plusieurs options pour vous inscrire à Active Directory Premium :

**Azure ou Office 365** : en tant qu’abonné Azure ou Office 365, vous pouvez acheter Active Directory Premium en ligne. Pour obtenir des instructions détaillées, voir [How to Purchase Azure Active Directory Premium - Existing Customers](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) (Comment acheter Azure Active Directory Premium - clients existants) ou [How to Purchase Azure Active Directory Premium - New Customers](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers) (Comment acheter Azure Active Directory Premium - nouveaux clients).

**Enterprise Mobility + Security** : Enterprise Mobility + Security (anciennement Enterprise Mobility Suite) permet aux organisations de rentabiliser leur utilisation des services suivants réunis dans un seul plan de licences : Active Directory Premium, Azure Rights Management et Microsoft Intune. Pour plus d’informations, consultez le site web de [Enterprise Mobility + Security](https://www.microsoft.com/fr-FR/server-cloud/enterprise-mobility/overview.aspx). Pour obtenir la version d’évaluation gratuite de 30 jours, cliquez [ici](https://portal.office.com/Signup/Signup.aspx?OfferId=2E63A04D-BE0B-4A0F-A8CF-407C1C299221&dl=EMS&ali=1#0).


**Programme de licence en volume Microsoft** : Azure Active Directory Premium est disponible via un [Contrat Entreprise Microsoft](https://www.microsoft.com/fr-FR/licensing/licensing-programs/enterprise.aspx) (au moins 250 licences) ou le programme de [licences en volume ouvert](https://www.microsoft.com/fr-FR/licensing/licensing-programs/open-license.aspx) (5 à 250 licences).


Cette rubrique présente la prise en main d’Azure Active Directory Premium, que vous avez acheté via le programme de licences en volume. Si vous ne connaissez pas encore les différentes éditions d’Azure Active Directory, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

> [AZURE.NOTE]
Les clients vivant en Chine peuvent accéder aux éditions de base et Premium d’Azure Active Directory à l’aide de l’instance mondiale d’Azure Active Directory. Actuellement, les éditions De base et Premium d’Azure Active Directory ne sont pas prises en charge dans le service Microsoft Azure utilisé par 21Vianet en Chine. Pour plus d’informations, contactez-nous via le [Forum Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).




## Étape 1 : inscrivez-vous à Azure Active Directory Premium

Pour vous inscrire, consultez la page [Comment acheter via un programme de licences en volume](http://www.microsoft.com/fr-FR/licensing/how-to-buy/how-to-buy.aspx).



## Étape 2 : activez votre plan de licences

Est-ce votre premier achat d’un plan de licence par le biais du programme de licences en volume Enterprise de Microsoft ? Dans ce cas, vous recevrez un message de confirmation une fois votre achat effectué. Vous avez besoin de cet e-mail pour activer votre premier plan de licence.

Pour tous les achats suivants de cet annuaire, les licences sont automatiquement activées dans ce même annuaire.



**Pour activer votre plan de licences, effectuez l’une des opérations suivantes :**


1. Pour démarrer l’activation, cliquez sur **Se connecter** ou **S’inscrire**.

    ![Connexion][1]



    - Si vous avez un client existant, cliquez sur **Se connecter** pour vous connecter avec votre compte administrateur existant. Vous devez vous connecter avec les informations d’identification d’administrateur général à partir du répertoire dans lequel les licences doivent être activées.

    - Si vous souhaitez créer un nouveau client Azure Active Directory à utiliser avec votre plan de licences, cliquez sur **S’inscrire** pour ouvrir la boîte de dialogue **Créer un profil de compte**.

        ![Créer un profil de compte][2]

Lorsque vous avez terminé, la boîte de dialogue suivante s’affiche en tant que confirmation de l’activation du plan de licence de votre client.

![Confirmation][3]

## Étapes 3 : activation de votre accès Azure Active Directory

Si vous avez utilisé Microsoft Azure, vous pouvez passer à l’[étape 4](#step-4-assign-license-to-user-accounts).

Lorsque les licences sont déployées dans votre annuaire, un **e-mail de Bienvenue** vous est envoyé. L’e-mail confirme que vous pouvez commencer à gérer vos licences Azure Active Directory Premium ou Enterprise Mobility Suite et les fonctionnalités.

Si vous essayez d’activer votre accès à Azure Active Directory avant de recevoir l’e-mail de bienvenue, vous rencontrez le message d’erreur suivant.

![L’accès n’est pas disponible.][9]

Veuillez réessayer dans quelques minutes, une fois que vous avez reçu l’e-mail.

Les nouveaux administrateurs dans votre abonnement peuvent également activer leur accès au portail Azure Classic en suivant ce lien.






**Pour activer votre accès à Azure Active Directory, procédez comme suit :**

1. Dans votre **e-mail de Bienvenue**, cliquez sur **Se connecter**.
    
    ![E-mail de Bienvenue][4]

2. Lorsque vous êtes correctement connecté, vous devez exécuter le deuxième facteur d'authentification sous forme de vérification du mobile :

    ![Vérification du mobile][5]

L’activation peut prendre plusieurs minutes. Une fois l’accès activé, la barre marron disparaît et vous pouvez cliquer sur **Portail**.

![Veuillez patienter pendant la configuration][6]

Dans ce cas, votre accès à Azure sera limité à Azure Active Directory.

![Fonctionnalités Azure][7]

Vous avez peut-être déjà eu accès à Azure lors d’une précédente utilisation. Par ailleurs, vous pouvez mettre à niveau votre accès à Azure Active Directory pour un accès Azure complet en activant des abonnements Azure supplémentaires. Dans ce cas, le portail Azure Classic présente davantage de fonctions.

![Fonctionnalités Azure][8]



## Étape 4 : attribution des licences aux comptes d'utilisateurs

Avant de commencer à utiliser le plan que vous venez d’acheter, vous devez attribuer manuellement des licences aux comptes d'utilisateurs de votre organisation afin qu'ils puissent utiliser les fonctionnalités fournies avec Premium. Procédez comme dans les étapes suivantes pour attribuer des licences aux utilisateurs pour leur permettre d’utiliser les fonctionnalités Azure Active Directory Premium.

**Pour affecter des licences à des utilisateurs, procédez comme suit :**

1. Connectez-vous au portail Azure Classic en tant qu’administrateur général de l’annuaire que vous souhaitez personnaliser.
2. Cliquez sur **Active Directory** puis sélectionnez le répertoire où vous souhaitez attribuer des licences.
3. Sélectionnez l’onglet **Licences**, sélectionnez **Active Directory Premium** ou **Enterprise Mobility Suite**, puis cliquez sur **Attribuer**.

    ![Plans de licence][10]

4. Dans la boîte de dialogue, sélectionnez les utilisateurs auxquels vous souhaitez attribuer des licences, puis cliquez sur l'icône de coche pour enregistrer les modifications.

    ![Attribuer des licences][11]

### Restrictions des licences

Certains plans de licences sont des sous-ensembles ou sur-ensembles d’autres plans de licences. Généralement, un utilisateur ne peut pas être affecté à un plan de licences auquel il a déjà été affecté. Si vous souhaitez attribuer un plan de licences qui est un sur-ensemble, vous devez d'abord supprimer le plan de licences du sous-ensemble.

### Conditions de licence :

Lorsque vous attribuez une licence à un utilisateur, vous pouvez spécifier un emplacement d'utilisation principal dans les propriétés de leur compte. Si un emplacement d'utilisation n'est pas spécifié, l’emplacement du client est automatiquement attribué à l'utilisateur.

![Emplacement de l’utilisateur][12]

La disponibilité des services et des fonctionnalités du service cloud Microsoft varie selon le pays ou la région. Un service tel que le protocole voix sur IP (VoIP) peut être disponible dans un pays ou une région et pas dans d’autres. Les fonctionnalités dans un service peuvent être limitées pour des raisons juridiques dans certains pays ou régions. Pour voir si un service ou une fonctionnalité est disponible avec ou sans restrictions, recherchez votre pays ou région sur le site des restrictions de licence d’un service.

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

<!---HONumber=AcomDC_0810_2016-->