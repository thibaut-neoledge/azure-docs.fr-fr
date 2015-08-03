<properties 
	pageTitle="Sécurisation des ressources de cloud avec le serveur Azure Multi-Factor Authentication et AD FS" 
	description="Voici la page d'authentification multifacteur Azure qui explique la prise en main de l'authentification multifacteur Azure et d’AD FS 2.0 dans le cloud." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Sécurisation des ressources de cloud avec le serveur Azure Multi-Factor Authentication et AD FS

Si votre organisation est fédérée avec Azure Active Directory et que vous disposez de ressources accessibles à Azure AD, vous pouvez utiliser l'authentification multifacteur d’Azure ou les services de fédération d’Active Directory pour sécuriser ces ressources. Utilisez les procédures ci-dessous pour sécuriser les ressources Azure Active Directory avec l’authentification multifacteur d’Azure ou les services de fédération d’Active Directory

## Pour sécuriser les ressources Azure AD à l'aide d'AD FS, procédez comme suit : 



1. Utilisez la procédure décrite dans [Activation de l'authentification multifacteur](multi-factor-authentication-get-started-cloud/#turn-on-multi-factor-authentication-for-users) pour permettre aux utilisateurs d’activer un compte.
2. Utilisez la procédure suivante pour configurer une règle de revendication :

![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)

- 	Démarrez la console de gestion AD FS.
- 	Accédez aux Parties de confiance et avec le bouton droit de la souris cliquez sur Partie de confiance. Sélectionnez Modifier les règles de revendication...
- 	Cliquez sur Ajouter une règle...
- 	Dans la liste déroulante, sélectionnez Envoyer les revendications à l’aide d’une règle personnalisée, puis cliquez sur Suivant.
- 	Donnez un nom à cette règle.
- 	Sous règle personnalisée : ajoutez le code suivant :


		=> issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

	Revendication correspondante :

		<saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
		<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
		</saml:Attribute>
- Cliquez sur OK. Cliquez sur Finish. Fermez la console de gestion AD FS.

Les utilisateurs peuvent ensuite procéder à la connexion à l'aide de la méthode locale (par exemple, carte à puce).

## Adresses IP de confiance pour les utilisateurs fédérés
Les adresses IP de confiance permettent aux administrateurs de contourner l'authentification multifacteur pour une adresse IP spécifique ou pour les utilisateurs fédérés qui ont des requêtes provenant de leur propre intranet. Les sections suivantes décrivent comment configurer des adresses IP de confiance Azure Multi-Factor Authentication avec des utilisateurs fédérés et comment contourner l'authentification multifacteur, lorsqu'une requête provient d'un intranet d'utilisateurs fédérés. Pour cela, vous devez configurer AD FS pour utiliser un passthrough ou filtrer un modèle de revendication entrante avec le type de revendication Inside Corporate Network (Dans un réseau d'entreprise). Cet exemple utilise Office 365 pour nos approbations de la partie de confiance.

### Configuration des règles de revendications AD FS

La première chose à faire consiste à configurer les revendications AD FS. Nous allons créer deux règles de revendications : une pour le type de revendication Inside Corporate Network (Dans un réseau d'entreprise) et l'autre pour maintenir les utilisateurs connectés.<ol>

<li>Ouvrez Gestion AD FS.</li>
<li>Sur la gauche, sélectionnez Relying Party Trusts (Approbations de la partie de confiance).</li>
<li>Au milieu, cliquez avec le bouton droit sur la plateforme d'identité Microsoft Office 365 et sélectionnez **Edit Claim Rules…** (Modifier les règles de revendication).</li>

![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)

<li>Sous Issuance Transform Rules (Émission de règles de transformation), cliquez sur **Add Rule** (Ajouter une règle).</li>

![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)

<li>Dans l'Assistant d'ajout d'une règle de revendication de transformation, sélectionnez Passthrough ou Filter an Incoming Claim (Filtrer une revendication entrante) dans la liste déroulante et cliquez sur Next (Suivant).</li>

![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)

<li>Dans la zone en regard du nom de la règle de revendication, nommez votre règle. Par exemple : InsideCorpNet.</li>
<li>Dans la liste déroulante, en regard du type de revendication entrante, sélectionnez Inside Corporate Network (Dans un réseau d'entreprise).</li>

![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)

<li>Cliquez sur Finish.</li>
<li>Sous Issuance Transform Rules (Émission de règles de transformation), cliquez sur **Add Rule** (Ajouter une règle).</li>
<li>Dans l'Assistant d'ajout d'une règle de revendication de transformation, sélectionnez Send Claims Using a Custom Rule (Envoyer les revendications à l'aide d'une règle personnalisée) dans la liste déroulante et cliquez sur Next (Suivant).</li>
<li>Dans la zone sous le nom de la règle de revendication, entrez Keep Users Signed In (Maintenir les utilisateurs connectés).</li>
<li>Dans la zone de la règle personnalisée, entrez&#160;: c:[Type == "http://schemas.microsoft.com/2014/03/psso"] => issue(claim = c);
</li>

![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)

<li>Cliquez sur **Terminer**.</li>
<li>Cliquez sur **Appliquer**.</li>
<li>Cliquez sur **Ok**.</li>

<li>Fermez Gestion AD FS.</li>

### Configuration d'adresses IP de confiance Azure Multi-Factor Authentication avec des utilisateurs fédérés
Maintenant que les revendications sont en place, nous pouvons configurer des adresses IP de confiance. <ol>

<li>Connectez-vous au portail de gestion Azure.</li>
<li>Cliquez à gauche sur Active&#160;Directory.</li>
<li>Sous Directory, cliquez sur le répertoire sur lequel vous souhaitez configurer les adresses IP de confiance.</li>
<li>Dans le répertoire que vous avez sélectionné, cliquez sur Configurer.</li>
<li>Dans la section Authentification multifacteur, cliquez sur Gérer les paramètres de service.</li>
<li>Dans la page Service Settings (Paramètres du service), sous Trusted IPs (Adresses IP de confiance), sélectionnez **For requests from federated users originating from my intranet** (Pour les requêtes d'utilisateurs fédérés provenant de mon intranet).</li>

![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)

<li>Cliquez sur Enregistrer.</li>
<li>Une fois les mises à jour appliquées, cliquez sur Fermer.</li>

Et voilà ! À ce stade, les utilisateurs fédérés d'Office 365 doivent pouvoir utiliser uniquement MFA lorsqu'une revendication provient de l'extérieur de l'intranet de l'entreprise.

<!---HONumber=July15_HO4-->