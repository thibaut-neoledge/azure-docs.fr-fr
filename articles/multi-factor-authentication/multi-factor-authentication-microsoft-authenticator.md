<properties 
	pageTitle="L’application Microsoft Authenticator pour téléphones mobiles" 
	description="Découvrez comment effectuer une mise à niveau vers la dernière version d’Azure Authenticator." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="femila" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/22/2016" 
	ms.author="billmath"/>

# Microsoft Authenticator

L’application Microsoft Authenticator fournit un niveau supplémentaire de sécurité qui peut être utilisé dans votre compte Azure (par ex. bsimon@contoso.onmicrosoft.com), votre compte professionnel local (par ex. bsimon@contoso.com), ou votre compte Microsoft (par ex bsimon@outlook.com.).

## Téléchargement de l’application Microsoft Authenticator

L'application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Fonctionnement de l’application Microsoft Authenticator
L’application envois une notification à l’application Microsoft Authenticator sur votre smartphone ou votre tablette. L’utilisateur appuie simplement sur « Vérifier » dans l’application pour s’authentifier.

L’application peut également être utilisée avec un code de vérification. Vous entrez simplement le code fourni par l’application dans l’écran de connexion lorsque vous y êtes invité.

Les 2 modes sont les suivants :

**Notification** - Dans ce mode, l’application Microsoft Authenticator empêche l’accès non autorisé à des comptes et arrête les transactions frauduleuses. Cette opération est effectuée à l’aide d’une notification Push sur votre téléphone ou votre appareil inscrit. Affichez simplement la notification et si elle est légitime, sélectionnez Authentifier. Dans le cas contraire, vous pouvez choisir de refuser ou de refuser et signaler la notification frauduleuse. Pour plus d’informations sur le signalement des notifications frauduleuses, consultez la rubrique Utilisation de la fonctionnalité de refus et signalement d’une fraude pour Multi-Factor Authentication.

**Mot de passe à usage unique** - Dans ce mode, l'application Microsoft Authenticator peut être utilisée comme jeton logiciel pour générer un code de vérification OATH. Ce code de vérification peut ensuite être entré avec le nom d’utilisateur et un mot de passe pour fournir la deuxième forme d’authentification.

## Ajouter un compte à l’application Microsoft Authenticator avec le scanneur de code QR

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


## Ajouter manuellement un compte à l’application Microsoft Authenticator
Si vous souhaitez ajouter un compte manuellement, sélectionnez le bouton de saisie manuelle de compte.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)


![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

Si vous avez un compte qui a déjà l'authentification multifacteur Azure, entrez le code et l'URL fournis sur la même page que celle illustrant le code-barres. Ces données sont destinées aux zones de code et d'URL de l'application mobile. Ceci lance l'activation.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

Une fois cette opération terminée, cliquez sur Me contacter. Une notification ou un code de vérification est alors envoyé sur votre téléphone. Cliquez sur vérifier.

## Passage à la nouvelle application Microsoft Authenticator

La publication de l’application Microsoft Authenticator remplace l’ancienne application Azure Authenticator. L'application Azure Authenticator continue de fonctionner, mais cet article peut vous aider si vous décidez d’adopter la nouvelle application Azure Authenticator.


### Déplacement d’une nouvelle application Microsoft Authenticator 

**Étape 1 :** installez Microsoft Authenticator.


**Étape 2 :** activez vos comptes avec la nouvelle application.

Vérifiez tout d’abord que vous disposez du code QR et de l’URL pour saisir manuellement le compte que vous souhaitez ajouter à l’application.

> [AZURE.NOTE] Vous ne savez pas comment obtenir le code QR ? Contactez votre support technique pour obtenir de l’aide.
> 
> Impossible d’activer votre compte avec la nouvelle application ? Contactez votre support technique.
>


Une fois que vous disposez du code QR, lancez l’application. Cliquez sur +.

Ensuite, spécifiez que vous souhaitez ajouter un compte professionnel ou scolaire. Cette action lance la caméra pour analyser le code QR. Si vous ne parvenez pas à analyser le code QR, vous pouvez toujours effectuer une saisie manuelle.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

Pour confirmer que le compte est activé, vérifiez que le nouveau compte s'affiche sur les pages de comptes.


Suivez cette étape pour tous les comptes que vous souhaitez migrer vers la nouvelle application.



**Étape 3 :** désinstallez l’ancienne application Multi-Factor Authentication présente sur votre téléphone.

Une fois que vous avez ajouté tous les comptes à la nouvelle application, désinstallez l’ancienne application de votre téléphone.



## Ajout d'un compte à l'aide du scanneur de codes-barres



- Tout d'abord, accédez à votre page de paramètres de vérification de la sécurité. Pour savoir comment accéder à cette page, consultez [Modification des paramètres de sécurité](multi-factor-authentication-end-user-manage-settings.md).

- Cliquez sur le bouton Configurer.
 
![Ajouter un compte](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- Un écran avec un code-barres s'affiche.
  
![Scanner le code-barres](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- Ouvrez maintenant l'application Microsoft Authenticator. Ceci doit afficher la page Comptes. Ici, vous verrez la liste des comptes que vous avez configurés. Si vous souhaitez ajouter un compte, cliquez sur le signe +, puis indiquez que vous souhaitez ajouter un compte professionnel ou scolaire. Cette action ouvre le scanneur.

- Scannez le code-barres.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- Attendez que le compte soit activé.

- Et c’est terminé ! Le nouveau compte doit désormais s'afficher dans la page Comptes.

![Ajouter un compte](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

![Ajouter un compte](./media/multi-factor-authentication-azure-authenticator/accounts.png)

## Ajout d'un compte Azure manuellement

Si vous souhaitez ajouter un compte manuellement, vous pouvez le faire en procédant comme suit :

- Tout d'abord, accédez à votre page de paramètres de vérification de la sécurité. Pour savoir comment accéder à cette page, consultez [Modification des paramètres de sécurité](multi-factor-authentication-end-user-manage-settings.md).

- Cliquez sur le bouton Configurer.
 
![Ajouter un compte](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- Un écran avec un code-barres s'affiche. Notez le code et l'URL sous le code-barres.
  
![Scanner le code-barres](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- Ouvrez maintenant l'application Microsoft Authenticator. Ceci doit afficher la page Comptes. Ici, vous verrez la liste des comptes que vous avez configurés. Si vous souhaitez ajouter un compte, cliquez sur le signe +, puis indiquez que vous souhaitez ajouter un compte professionnel ou scolaire. Cette action ouvre le scanneur. Cette action ouvre le scanneur.

![Ajouter un compte](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- Cliquez sur Entrer le code manuellement en bas.

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- Entrez le code et l'URL fournis sur la même page que celle illustrant le code-barres. Ces données sont destinées aux zones de code et d'URL de l'application mobile. Ceci lance l'activation.

![Ajouter un compte](./media/multi-factor-authentication-azure-authenticator/manual.png)

![Paramétrage](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

- Attendez que le compte soit activé.

- Et c’est terminé ! Le nouveau compte doit désormais s'afficher dans la page Comptes.

![Ajouter un compte](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

![Ajouter un compte](./media/multi-factor-authentication-azure-authenticator/accounts.png)


## Ajout d'un compte à l'aide de TouchID
L'application mobile Microsoft Authenticator sur iOS prend en charge Touch ID. L'authentification multifacteur Azure permet aux organisations d'exiger un code confidentiel en plus de posséder leur appareil inscrit. Avec cette nouvelle fonctionnalité, les utilisateurs iOS avec les périphériques prenant en charge Touch ID n'ont plus besoin d'entrer le code confidentiel. Une fois configurés, les utilisateurs peuvent simplement scanner leur empreinte digitale au lieu d'entrer le code confidentiel et de cliquer sur Approuver.

La configuration de Touch ID avec Microsoft Authenticator est très simple. Vous effectuez simplement une vérification normale avec le code confidentiel et si votre périphérique prend en charge Touch ID, nous le configurerons automatiquement pour vous.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

À partir de là, lorsque vous êtes invité à vérifier votre connexion, vous appuyez sur la notification Push reçues et vous scannez votre empreinte digitale au lieu d'entrer votre code confidentiel.

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## Suppression d'un compte

Pour supprimer les différents comptes à partir de l'application Microsoft Authenticator, cliquez simplement sur le compte. Vous obtiendrez une option de suppression.

![Supprimer un compte](./media/multi-factor-authentication-azure-authenticator/remove.png)

<!---HONumber=AcomDC_0831_2016-->