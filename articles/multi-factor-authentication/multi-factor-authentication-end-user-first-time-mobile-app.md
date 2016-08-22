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
	ms.date="08/04/2016" 
	ms.author="billmath"/>

# Utilisation de l’application mobile comme méthode de contact avec Azure Multi-Factor Authentication

Si vous souhaitez utiliser l’application mobile comme méthode de contact principale, vous pouvez utiliser cet article. Il vous guide dans la configuration de l’authentification multifacteur pour utiliser votre application mobile comme méthode de contact principale.

L’application Azure Authenticator pour [Windows Phone](http://www.windowsphone.com/fr-FR/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) et [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).

## Pour utiliser une application mobile comme méthode de contact


- Dans la liste déroulante, sélectionnez Application mobile.


![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

- Sélectionnez Notification ou Mot de passe à usage unique et cliquez sur Configurer.
- Sur le téléphone où l’application Azure Authenticator est installée, lancez l’application, puis cliquez sur Scanner le code-barres. Pour ajouter un compte qui a déjà l'authentification multifacteur Azure ou un compte tiers, consultez <a href="#compte">Ajout d'un compte manuellement</a>.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- Numérisez l’image de code-barres fournie avec l'écran de configuration de l’application mobile. Cliquez sur Terminer pour fermer l’écran de code-barres. Si vous ne parvenez pas à scanner le code-barres, vous pouvez entrer manuellement les informations.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- Sur le téléphone, l’activation commence et une fois cette opération terminée, cliquez sur Contact. Une notification ou un code de vérification est alors envoyé sur votre téléphone. Cliquez sur vérifier.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- Cliquez sur Fermer. À ce stade, la vérification doit réussir.
- À présent, il est recommandé d'entrer votre numéro de téléphone mobile en cas de perte d'accès à votre application mobile.
- Spécifiez votre pays dans la liste déroulante et entrez votre numéro de téléphone portable dans la zone située en regard du pays. Cliquez sur Suivant.
- À ce stade, vous avez configuré votre méthode de contact et il est temps de définir les mots de passe d'application pour les applications sans navigateur comme Outlook 2010 ou antérieure. Si vous n'utilisez pas ces applications, cliquez sur **Terminé**. Sinon, passez à l'étape suivante.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- Si vous utilisez ces applications, copiez le mot de passe d’application fourni et collez-le dans votre application sans navigateur. Pour connaître les étapes relatives aux applications individuelles, comme Outlook et Lync, consultez la rubrique Modification du mot de passe dans votre messagerie électronique par le mot de passe d’application et Modification du mot de passe dans votre application par le mot de passe d’application.
- Cliquez sur Done.


<h2 id="compte">Ajout d'un compte manuellement</h2>
Si vous souhaitez ajouter un compte manuellement, sélectionnez le bouton de saisie manuelle de compte.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)

Si vous avez un compte qui a déjà l'authentification multifacteur Azure, entrez le code et l'URL fournis sur la même page que celle illustrant le code-barres. Ces données sont destinées aux zones de code et d'URL de l'application mobile. Ceci lance l'activation.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

Une fois cette opération terminée, cliquez sur Me contacter. Une notification ou un code de vérification est alors envoyé sur votre téléphone. Cliquez sur vérifier. Pour terminer, suivez les étapes ci-dessus en commençant au numéro 6.

Si vous utilisez un compte tiers avec l'application mobile, entrez le nom du compte et la clé de sécurité dans les zones fournies, puis activez le compte. Une fois que vous avez fait cela et vérifié le compte, suivez les étapes ci-dessus en commençant au numéro 6.


![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/add3rdparty.png)

>[AZURE.NOTE]Si la mention « Ajouter un compte professionnel » s'affiche, elle s'applique à la jonction d'espace de travail et non à l'authentification multifacteur. Vous pouvez l'ignorer.
 

<!---HONumber=AcomDC_0810_2016-->