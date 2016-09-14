<properties 
	pageTitle="Utilisation de l’application mobile comme méthode de contact avec Azure MFA" 
	description="Cette page montre aux utilisateurs comment utiliser l’application mobile comme méthode de contact principale pour Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenp" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="billmath"/>

# Utilisation de l’application mobile comme méthode de contact avec Azure Multi-Factor Authentication

Si vous souhaitez utiliser l’application Microsoft Authenticator comme méthode de contact principale, vous pouvez vous référer à cet article. Il vous guide dans la configuration de l’authentification multifacteur pour utiliser votre application mobile comme méthode de contact principale.

L'application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Pour utiliser Microsoft Authenticator comme méthode de contact


- Sur l’écran de vérification de sécurité supplémentaire, sélectionnez Application mobile dans la liste déroulante.


![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

- Sélectionnez Notification ou Mot de passe à usage unique et cliquez sur Configurer.
- Sur le téléphone où est installée l’application Microsoft Authenticator, lancez l’application, puis cliquez sur « + » pour ajouter un compte. Ensuite, spécifiez que vous souhaitez ajouter un compte professionnel ou scolaire, ce qui lance le scanneur de code QR. Si votre appareil photo ne fonctionne pas correctement, vous pouvez choisir d’entrer manuellement les informations de votre société. [Ajout manuel d’un compte](#adding-an-account-manually).

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan4.png)

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- Numérisez l’image de code QR fournie avec l’écran de configuration de l’application mobile. Cliquez sur Terminer pour fermer l’écran de code QR.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- Si vous ne parvenez pas à scanner le code QR, vous pouvez entrer les informations manuellement.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- Sur le téléphone, l’activation commence et une fois cette opération terminée, cliquez sur Contact. Une notification ou un code de vérification est alors envoyé sur votre téléphone. Cliquez sur vérifier.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- Certaines entreprises peuvent exiger un code PIN lors de la vérification.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)


- Une fois cette opération terminée, vous pouvez cliquer sur Fermer. À ce stade, la vérification doit réussir.
- À présent, il est recommandé d'entrer votre numéro de téléphone mobile en cas de perte d'accès à votre application mobile.
- Spécifiez votre pays dans la liste déroulante et entrez votre numéro de téléphone portable dans la zone située en regard du pays. Cliquez sur Suivant.
- À ce stade, vous avez configuré votre méthode de contact et il est temps de définir les mots de passe d'application pour les applications sans navigateur comme Outlook 2010 ou antérieure. Si vous n'utilisez pas ces applications, cliquez sur **Terminé**. Sinon, passez à l'étape suivante.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- Si vous utilisez ces applications, copiez le mot de passe d’application fourni et collez-le dans votre application sans navigateur. Pour connaître les étapes relatives aux applications individuelles, comme Outlook et Lync, consultez la rubrique Modification du mot de passe dans votre messagerie électronique par le mot de passe d’application et Modification du mot de passe dans votre application par le mot de passe d’application.
- Cliquez sur Done.


## Ajout d'un compte manuellement
Si vous souhaitez ajouter un compte manuellement, sélectionnez le bouton de saisie manuelle de compte.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)


![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

Si vous avez un compte qui a déjà l'authentification multifacteur Azure, entrez le code et l'URL fournis sur la même page que celle illustrant le code-barres. Ces données sont destinées aux zones de code et d'URL de l'application mobile. Ceci lance l'activation.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

Une fois cette opération terminée, cliquez sur Me contacter. Une notification ou un code de vérification est alors envoyé sur votre téléphone. Cliquez sur vérifier.



 

<!---HONumber=AcomDC_0831_2016-->