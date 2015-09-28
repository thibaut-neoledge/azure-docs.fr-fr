<properties
	pageTitle="Authentification unique pour les applications avec authentification Windows intégrée locales en utilisant la délégation Kerberos contrainte avec le proxy d’application"
	description="Explique comment devenir opérationnel avec le proxy d’application Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="rkarlin"/>



# Authentification unique pour les applications avec authentification Windows intégrée locales en utilisant la délégation Kerberos contrainte avec le proxy d’application


Vous pouvez activer l’authentification unique pour vos applications avec l’authentification Windows intégrée en attribuant aux connecteurs du proxy d’application dans Active Directory l’autorisation d’emprunter l’identité des utilisateurs, et d’envoyer et de recevoir des jetons en leur nom.

> [AZURE.IMPORTANT]Le Proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou Basic d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).


## Diagramme du réseau

![Diagramme de flux de l’authentification Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

Ce diagramme explique le flux quand un utilisateur tente d’accéder à une application locale qui utilise I’authentification Windows intégrée. Le flux général est le suivant :

1. L’utilisateur entre l’URL pour accéder à l’application locale via le proxy d’application.
2. Le proxy d’application redirige la demande vers les services d’authentification d’Azure AD pour effectuer la préauthentification. À ce stade, Azure AD applique les stratégies d’authentification et d’autorisation applicables, comme l’authentification multifacteur. Si l’utilisateur est validé, Azure AD crée un jeton et l’envoie à l’utilisateur.
3. L’utilisateur transmet le jeton au proxy d’application.
4. Le proxy d’application valide le jeton et y récupère le nom d’utilisateur principal (UPN), puis envoie la demande, l’UPN et le nom de principal du service (SPN) au connecteur via un canal sécurisé doublement authentifié.
5. Le connecteur effectue une négociation de délégation Kerberos contrainte avec Active Directory local, en empruntant l’identité de l’utilisateur pour obtenir un jeton Kerberos pour l’application.
6. Active Directory envoie le jeton Kerberos de l’application au connecteur.
7. Le connecteur envoie la demande d’origine au serveur d’applications, en utilisant le jeton Kerberos reçu d’Active Directory.
8. L’application envoie la réponse au connecteur, qui est ensuite retournée au service de proxy d’application et enfin à l’utilisateur.

### Composants requis

1. Assurez-vous que vos applications, comme des applications web SharePoint, sont configurées pour utiliser l’authentification Windows intégrée. Pour plus d’informations, consultez [Activer la prise en charge de l’authentification Kerberos](https://technet.microsoft.com/library/dd759186.aspx) ou, pour SharePoint, consultez [Planifier l’authentification Kerberos dans SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
2. Créez des noms de principaux du service pour vos applications.
3. Assurez-vous que le serveur exécutant le connecteur et que le serveur exécutant l’application que vous publiez sont joints au domaine et font partie du même domaine. Pour plus d’informations sur la jonction à un domaine, consultez [Joindre un ordinateur à un domaine](https://technet.microsoft.com/library/dd807102.aspx).


## Configuration d’Active Directory

La configuration d’Active Directory varie selon que votre connecteur de proxy d’application et le serveur publié sont ou non dans le même domaine.

### Connecteur et serveur publié dans le même domaine



1. Dans Active Directory, accédez à **Outils** > **Utilisateurs et ordinateurs**. 
2. Sélectionnez le serveur exécutant le connecteur. 
3. Cliquez avec le bouton droit et sélectionnez **Propriétés** > **Délégation**. 
4. Sélectionnez **N’approuver cet ordinateur que pour la délégation aux services spécifiés** puis, sous **Services auxquels ce compte peut présenter des informations d’identification déléguées**, ajoutez la valeur de d’identité du nom de principal du service (SPN) du serveur d’applications. 
5. Ceci permet au connecteur de proxy d’application d’emprunter l’identité des utilisateurs dans Active Directory pour les applications définies dans la liste.

![Capture d’écran de la fenêtre Propriétés du connecteur-SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

### Connecteur et serveur publié dans des domaines différents

1. Pour obtenir la liste des conditions préalables à l’utilisation de la délégation Kerberos contrainte entre domaines, consultez [Délégation Kerberos contrainte entre domaines](https://technet.microsoft.com/library/hh831477.aspx).
2. Dans Windows 2012 R2, utilisez la propriété `principalsallowedtodelegateto` sur le serveur Connecteur pour permettre au proxy d’application de déléguer pour le serveur Connecteur, où le serveur publié est `sharepointserviceaccount` et le serveur de délégation est `connectormachineaccount`.

		$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

		Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

		Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE]`sharepointserviceaccount` peut être le compte d’ordinateur SPS ou un compte de service sous lequel s’exécute le pool d’applications SPS.


## Configuration du portail Azure

1. Publiez votre application en suivant les instructions décrites dans [Publier des applications avec le proxy d’application](active-directory-application-proxy-publish.md). Veillez à sélectionner **Azure Active Directory** comme **méthode de préauthentification**.
2. Dès lors que votre application figure dans la liste des applications, sélectionnez-la et cliquez sur **Configurer**.
3. Sous **Propriétés**, définissez **Méthode d’authentification interne** sur **Authentification Windows intégrée**.

![Configuration avancée des applications](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)

4. Entrez le **Nom de principal du service de l’application interne** du serveur d’applications. Dans cet exemple, le nom de principal du service pour notre application publiée est http/lob.contoso.com.

>[AZURE.IMPORTANT]Les UPN dans Azure Active Directory doivent être identiques aux UPN dans votre Active Directory local pour que la préauthentification fonctionne. Assurez-vous que votre Azure Active Directory est synchronisé avec votre Active Directory local.

| | |
| --- | --- |
| Méthode d’authentification interne | Si vous utilisez Azure AD pour la préauthentification, vous pouvez définir une méthode d’authentification interne pour permettre à vos utilisateurs de profiter de l’authentification unique pour cette application. <br><br> Sélectionnez l’**authentification Windows intégrée** (IWA) si votre application l’utilise. Vous pouvez configurer la délégation Kerberos contrainte (KCD) pour activer l’authentification unique pour cette application. Les applications qui utilisent l’authentification Windows intégrée doivent être configurées à l’aide de la délégation Kerberos contrainte, sinon le proxy d’application ne sera pas en mesure de les publier. <br><br> Sélectionnez **Aucune** si votre application n’utilise pas l’authentification Windows intégrée. |
| SPN d’application interne | Il s’agit du nom de principal du service (SPN) de l’application interne tel qu’il est configuré Azure AD local. Le SPN est utilisé par le connecteur du proxy d’application pour extraire les jetons Kerberos pour l’application en utilisant la délégation Kerberos contrainte. |

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg

<!---HONumber=Sept15_HO3-->