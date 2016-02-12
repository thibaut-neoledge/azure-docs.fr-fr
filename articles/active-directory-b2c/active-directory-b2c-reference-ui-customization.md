<properties
	pageTitle="Version préliminaire d’Azure Active Directory B2C : outil d’assistance de personnalisation d’interface utilisateur | Microsoft Azure"
	description="Une rubrique sur les fonctionnalités de personnalisation de l’interface utilisateur (UI) dans Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/28/2016"
	ms.author="swkrish"/>

# Version préliminaire d’Azure Active Directory B2C : comment personnaliser l’interface utilisateur B2C Interface Azure AD (IU)

L’expérience utilisateur est primordiale dans une application utilisée par les consommateurs. Elle fait la différence entre une bonne application et une excellente, et entre les consommateurs simplement actifs et ceux qui sont réellement engagés. Azure Active Directory (AD) B2C vous permet de personnaliser les pages d’inscription, de connexion (*voir la remarque ci-dessous*) et d’édition de profil de l’utilisateur avec le contrôle pixel-perfect.

> [AZURE.NOTE]
Actuellement, les pages d’inscription au compte local, les courriers électroniques de vérification et les pages de réinitialisation de mot de passe en libre-service ne sont personnalisables qu’avec la [fonction de personnalisation de la société](../active-directory/active-directory-add-company-branding.md) et non par le biais des mécanismes décrits dans cet article.

Cet article portera sur les éléments suivants :

- La présentation de la fonction de personnalisation d’interface utilisateur (UI) de page.
- Un outil d’assistance qui vous permettra de tester la fonctionnalité de personnalisation d’interface utilisateur de page à l’aide de notre exemple de contenu.
- Les éléments d’interface utilisateur de base dans chaque type de page.
- Meilleures pratiques lors de l’exercice de cette fonctionnalité.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## La fonctionnalité de personnalisation de l’interface utilisateur (UI) de page

La fonctionnalité de personnalisation de l’interface utilisateur de page vous permet de personnaliser l’apparence des pages d’inscription, de connexion et de modification de profil du consommateur (en configurant des [stratégies](active-directory-b2c-reference-policies.md)). Vos clients auront des expériences cohérentes lors de la navigation entre votre application et les pages fournies par le service Azure AD B2C.

Contrairement aux autres services où les options sont limitées ou ne sont disponibles que via les API, Azure AD B2C utilise une approche moderne (et plus simple) de l’interface utilisateur de personnalisation de page. Voici comment cela fonctionne : Azure AD B2C exécute le code dans l’explorateur client et utilise une approche moderne appelée [partage de ressources cross-origin (CORS)](http://www.w3.org/TR/cors/) pour charger le contenu depuis une URL spécifiée au préalable dans une stratégie. Vous pouvez spécifier des URL différentes pour différentes pages. Le code fusionne le contenu de B2C Azure AD (appelés des éléments d’interface utilisateur) et le contenu chargé depuis votre URL et affiche la page pour le consommateur. Il vous suffit de faire est créer un contenu HTML5 correct avec un élément `<div id="api"></div>` situé quelque part dans `<body>`. C’est l’endroit où le contenu Azure AD B2C est fusionné. Et héberger ce contenu sur un point de terminaison HTTPS (avec CORS activé). Vous pouvez aussi totalement personnaliser les éléments d’interface utilisateur d’Azure AD B2C.

## Test de la fonctionnalité de personnalisation de l'interface

Si vous souhaitez tester la fonctionnalité de personnalisation de l'expérience utilisateur à l'aide de notre exemple de contenu HTML et CSS hébergé sur le stockage d'objets Blob Azure, nous fournissons [un outil simple d'assistance](active-directory-b2c-reference-ui-customization-helper-tool.md) pour télécharger et configurer votre contenu statique.

## Éléments d’interface utilisateur de base dans chaque type de page.

Dans cette section, vous trouverez des fragments HTML5 (pour chaque type de page) qu'Azure AD B2C fusionne dans l'élément <div id="api"></div> situé dans votre contenu. Vous pouvez utiliser vos propres feuilles de style pour personnaliser ces éléments d’interface utilisateur. Ces feuilles de style peuvent remplacer des feuilles de style par défaut que nous ajoutons dans ces pages dans les fragments <head>.

> [AZURE.IMPORTANT]
	Pendant la présentation, prévoyez les éléments d’interface utilisateur à modifier, car nous prenons en compte vos retours et nous nous adaptons en conséquence. Consultez le code source des pages par défaut pour les dernières mises à jour. En fait, la première modification envisagée est la suppression de nos feuilles de style par défaut ; cela signifie que vous devrez toujours fournir vos propres feuilles de style pour ces éléments d'interface utilisateur dans votre contenu.

## Page de sélection Fournisseur d’identité

Cette page contient une liste de fournisseurs d’identité dans laquelle l’utilisateur peut choisir à l’inscription ou à la connexion. Il s’agit de fournisseurs d’identité sociale tels que Facebook et Google + ou de comptes locaux (basés sur une adresse de messagerie ou un nom d’utilisateur).

```HTML

<div id="api" data-name="IdpSelections">
	<div class="intro">
         <p>Sign up</p>
	</div>
	
	<div>
		<ul>
			<li>
				<button class="accountButton" id="FacebookExchange">Facebook</button>
			</li>
			<li>
				<button class="accountButton" id="GoogleExchange">Google+</button>
			</li>
			<li>
				<button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
			</li>
		</ul>
	</div>
</div>

```

## Page d’inscription à un compte local

Cette page contient un formulaire d’inscription que l’utilisateur doit renseigner lors de l’inscription à l’aide d’un compte local basé sur un courrier électronique ou un nom d’utilisateur. Le formulaire peut contenir différentes commandes de saisie telles que la zone de saisie de texte, celle du mot de passe, le bouton radio, le menu déroulant à sélection unique et sélectionner plusieurs cases à cocher.

```HTML

<div id="api" data-name="SelfAsserted"> 
	<div class="intro"> 
		<p>Create your account by providing the following details</p>
	</div>
	
	<div id="attributeVerification"> 
		<div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
		<div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
		<div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
		<div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
		<div class="attr" id="attributeList"> 
			<ul>
				<li> 
					<div class="attrEntry validate">
						<div> 
							<div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
							<div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
							<div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
							<div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
							<div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
							<div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
							<div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
						</div>

					<div class="helpText show">This information is required</div>
						<label>Email</label>
						<input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

					<div class="buttons verify" claim_id="email"> 
						<div id="email_ver_wait" class="working" style="display: none;"></div>
							<label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label> 
							<input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
							<button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
							<button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
							<button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
							<button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
							<button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button> 
						</div>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
						<label>Enter password</label>
						<input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ "; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"> This information is required</div>
						<label>Reenter password</label>
						<input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*-_+=[]{}|\\:',?/`~";();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText">This information is required</div>
						<label>Name</label>
						<input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>Gender</label>
						<input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
						<label for="extension_Gender_F">Female</label>
						<input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
						<label for="extension_Gender_M">Male</label>
						<a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>Loyalty number</label>
						<input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText"></div>
						<label>State</label>
						<select class="dropdown_single" id="state">
							<option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
							<option value="WA">Washington</option>
							<option value="NY">New York</option>
							<option value="CA">California</option>
						</select>
						<a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
					</div>
				</li>
				<li> 
					<div class="attrEntry">
						<div class="helpText">This information is required</div>
						<label>Zip code</label>
						<input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
					</div>
				</li>
			</ul> 
		</div>
		<div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
	</div>
	<div class="verifying-modal"> 
		<div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
		<div id="verifying_blurb"></div>
	</div>
</div>

```

## Page d’inscription à un compte social

Cette page contient un formulaire d’inscription que le consommateur doit remplir lors de l’inscription à l’aide d’un compte existant à partir d’un fournisseur d’identité sociale tel que Facebook ou Google +. Cette page est similaire à la page d’inscription à un compte local (affiché dans la section précédente) à l’exception des champs de saisie de mot de passe.

## Page d’authentification multifacteur Azure

Cette page permet aux utilisateurs de vérifier leurs numéros de téléphone (par voie textuelle ou vocale) au cours de l’inscription ou de la connexion.

```HTML

<div id="api" data-name="Phonefactor">
	<div id="phonefactor_initial">
		<div class="intro">
			<p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
		</div>
		<div class="errorText" id="errorMessage" style="display:none"></div>
		<div class="phoneEntry" id="phoneEntry">
			<div class="phoneNumber">
				<select id="countryCode" style="display:inline-block">
					<option value="+93">Afghanistan (+93)</option>
					<!-- Not all country codes listed -->
					<option value="+44">United Kingdom (+44)</option>
					<option value="+1" selected="">United States (+1)</option>
					<!-- Not all country codes listed -->
				</select>
			</div>
			<div class="phoneNumber">
				<input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
			</div>
		</div>
		<div id="codeVerification" style="display:none">
			<div>
				<p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
				<input type="text" id="verificationCode" placeholder="Verification code">
			</div>
		</div>
		<div class="buttons">
			<button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
			<button id="verifyPhone" style="display:inline-block">Call Me</button>
			<button id="cancel" style="display:inline-block">Cancel</button>
		</div>
	</div>
	<div class="dialing-modal">
		<div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
		<div id="dialing_blurb"></div><div id="dialing_number"></div>
	</div>
</div>

```

## Page d’erreur


```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
	<h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
	<div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
	<div class="error-page-messagedetails">
		<div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
		<div>Timestamp:2015-09-14 23:22:35Z</div>
		<div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
	</div>
</div>

```

## Points à retenir lors de la création de votre propre contenu.

Si vous envisagez d’utiliser la fonctionnalité de personnalisation d’interface utilisateur de page, veuillez consulter les meilleures pratiques suivantes :

- Ne copiez pas le modèle par défaut de B2C Azure AD et ne le modifiez pas. Il est préférable de créer votre contenu HTML5 à partir de zéro et d’utiliser le modèle par défaut comme référence.
- Pour des raisons de sécurité, nous ne vous permettons pas d’inclure du langage JavaScript dans votre contenu. La plupart des éléments dont vous avez besoin sont disponibles et prêts à l’emploi. Dans le cas contraire, utilisez [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) pour demander de nouvelles fonctionnalités.
- Versions de navigateur prises en charge :
	- Internet Explorer 11
	- Internet Explorer 10
	- Internet Explorer 9 (limité)
	- Internet Explorer 8 (limité)
	- Google Chrome 43.0
	- Google Chrome 42.0
	- Mozilla Firefox 38.0
	- Mozilla Firefox 37.0

<!---HONumber=AcomDC_0204_2016-->