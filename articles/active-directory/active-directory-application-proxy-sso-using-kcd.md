<properties
	pageTitle="Authentification unique avec le proxy d’application | Microsoft Azure"
	description="Explique comment fournir l’authentification unique à l’aide du proxy d’application Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="kgremban"/>


# Authentification unique avec le proxy d’application

> [AZURE.NOTE]Le Proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou Basic d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

L’authentification unique est un élément clé du proxy d’application Azure AD. Elle procure la meilleure expérience utilisateur : un utilisateur se connecte au cloud, toutes les validations de sécurité sont effectuées dans le cloud (pré-authentification), puis, quand la demande est envoyée à l’application locale, le connecteur de proxy d’application emprunte l’identité de l’utilisateur pour que l’application principale considère ce dernier comme un utilisateur normal provenant d’un appareil joint à un domaine.

![Diagramme d’accès de l’utilisateur final au réseau d’entreprise via le proxy d’application](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Le proxy d’application Azure AD vous permet d’offrir une expérience d’authentification unique à vos utilisateurs. Utilisez les instructions suivantes pour publier vos applications en utilisant l’authentification unique :


## Authentification unique pour les applications avec authentification Windows intégrée locales en utilisant la délégation Kerberos contrainte avec le proxy d’application
Vous pouvez activer l’authentification unique pour vos applications avec l’authentification Windows intégrée en attribuant aux connecteurs du proxy d’application dans Active Directory l’autorisation d’emprunter l’identité des utilisateurs et d’envoyer et recevoir des jetons en leur nom.


### Diagramme du réseau

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


### Configuration d’Active Directory

La configuration d’Active Directory varie selon que votre connecteur de proxy d’application et le serveur publié sont ou non dans le même domaine.

#### Connecteur et serveur publié dans le même domaine

1. Dans Active Directory, accédez à **Outils** > **Utilisateurs et ordinateurs**.
2. Sélectionnez le serveur exécutant le connecteur.
3. Cliquez avec le bouton droit et sélectionnez **Propriétés** > **Délégation**.
4. Sélectionnez **N’approuver cet ordinateur que pour la délégation aux services spécifiés** puis, sous **Services auxquels ce compte peut présenter des informations d’identification déléguées**, ajoutez la valeur de l’identité du nom de principal du service (SPN) du serveur d’applications.
5. Ceci permet au connecteur de proxy d’application d’emprunter l’identité des utilisateurs dans Active Directory pour les applications définies dans la liste.

![Capture d’écran de la fenêtre Propriétés du connecteur-SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### Connecteur et serveur publié dans des domaines différents

1. Pour obtenir la liste des conditions préalables à l’utilisation de la délégation Kerberos contrainte entre domaines, consultez [Délégation Kerberos contrainte entre domaines](https://technet.microsoft.com/library/hh831477.aspx).
2. Dans Windows 2012 R2, utilisez la propriété `principalsallowedtodelegateto` sur le serveur du connecteur pour permettre au proxy d’application de déléguer pour le serveur de connecteur, où le serveur publié est `sharepointserviceaccount` et le serveur de délégation est `connectormachineaccount`.

		$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

		Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

		Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE]`sharepointserviceaccount` peut être le compte d’ordinateur SPS ou un compte de service sous lequel s’exécute le pool d’applications SPS.


### Configuration du portail Azure Classic

1. Publiez votre application en suivant les instructions décrites dans [Publier des applications avec le proxy d’application](active-directory-application-proxy-publish.md). Veillez à sélectionner **Azure Active Directory** comme **méthode de préauthentification**.
2. Dès que votre application figure dans la liste des applications, sélectionnez-la et cliquez sur **Configurer**.
3. Sous **Propriétés**, définissez **Méthode d’authentification interne** sur **Authentification Windows intégrée**. ![Configuration avancée des applications](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. Entrez le **SPN d’application interne** du serveur d’applications. Dans cet exemple, le nom de principal du service pour notre application publiée est http/lob.contoso.com.  

>[AZURE.IMPORTANT]Les UPN dans Azure Active Directory doivent être identiques aux UPN dans votre Active Directory local pour que la préauthentification fonctionne. Vérifiez que votre Azure Active Directory est synchronisé avec votre Active Directory local.

| | |
| --- | --- |
| Méthode d’authentification interne | Si vous utilisez Azure AD pour la préauthentification, vous pouvez définir une méthode d’authentification interne pour permettre à vos utilisateurs de profiter de l’authentification unique pour cette application. <br><br> Sélectionnez l’**authentification Windows intégrée** (IWA) si votre application l’utilise. Vous pouvez configurer la délégation Kerberos contrainte (KCD) pour activer l’authentification unique pour cette application. Les applications qui utilisent l’authentification Windows intégrée doivent être configurées à l’aide de la délégation Kerberos contrainte, sinon le proxy d’application ne sera pas en mesure de les publier. <br><br> Sélectionnez **Aucune** si votre application n’utilise pas l’authentification Windows intégrée. |
| SPN d’application interne | Il s’agit du nom de principal du service (SPN) de l’application interne tel qu’il est configuré Azure AD local. Le SPN est utilisé par le connecteur du proxy d’application pour extraire les jetons Kerberos pour l’application en utilisant la délégation Kerberos contrainte. |


## Authentification unique pour les applications non Windows
Le flux de délégation Kerberos dans le proxy d’application Azure AD démarre quand Azure AD authentifie l’utilisateur dans le cloud. Une fois que la demande est disponible localement, le connecteur du proxy d’application AD Azure émet un ticket Kerberos pour le compte de l’utilisateur en interagissant avec l’annuaire Active Directory local. Ce processus est appelé délégation Kerberos contrainte (KCD). Au cours de la phase suivante, une demande est envoyée à l’application principale avec ce ticket Kerberos. De nombreux protocoles définissent comment envoyer ces demandes. La plupart des serveurs non Windows supposent qu’il s’agit de Negotiate/SPNego, qui est maintenant pris en charge sur le proxy d’application Azure AD.

![Diagramme de l’authentification unique non Windows](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### Identité de délégué partielle
Les applications non Windows obtiennent généralement l’identité de l’utilisateur sous la forme d’un nom d’utilisateur ou d’un nom de compte SAM, pas d’une adresse de messagerie (username@domain). Elles diffèrent de la plupart des systèmes basés sur Windows, qui préfèrent recourir à un UPN, dispositif plus efficace qui garantit l’absence de duplication entre les domaines.

Pour cette raison, le proxy d’application vous permet de sélectionner les identités qui doivent s’afficher dans le ticket Kerberos, par application. Certaines de ces options sont adaptées pour les systèmes qui n’acceptent pas le format d’adresse de messagerie.

![Capture d’écran du paramètre Identité de connexion déléguée](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Si une identité partielle est utilisée et qu’il y a des chances pour qu’elle ne soit pas unique pour tous les domaines ou forêts de votre organisation, vous pouvez publier ces applications deux fois en utilisant deux groupes de connecteurs distincts. Dans la mesure où chaque application possède sa propre audience utilisateur, vous pouvez joindre ses connecteurs à un autre domaine.


## Utilisation de l’authentification unique quand des identités locales et sur le cloud ne sont pas identiques
Sauf s’il est configuré différemment, le proxy d’application suppose que les utilisateurs ont exactement la même identité dans le cloud et localement. Vous pouvez configurer, pour chaque application, l’identité qui doit être utilisée pendant l’exécution de l’authentification unique.

Grâce à cette fonctionnalité, de nombreuses organisations qui ont des identités locales et cloud différentes peuvent mettre en œuvre l’authentification unique des applications cloud aux applications locales sans contraindre les utilisateurs à entrer des noms d’utilisateur et des mots de passe différents. Cela inclut les organisations qui :

- disposent de plusieurs domaines en interne (joe@us.contoso.com, joe@eu.contoso.com) et d’un domaine unique dans le cloud (joe@contoso.com) ;

- disposent d’un nom de domaine non routable en interne (joe@contoso.usa) et d’un nom de domaine légal dans le cloud ;

- n’utilisent pas de noms de domaine en interne (joe) ;

- utilisent différents alias localement et dans le cloud, par exemple, joe-johns@contoso.com et joej@contoso.com.

Cette configuration convient aussi pour les applications qui n’acceptent pas d’adresses sous forme d’adresses de messagerie, scénario très courant pour les serveurs principaux non Windows.


### Configuration de l’authentification unique pour différentes identités sur le cloud et localement

1. Configurez les paramètres Azure AD Connect de manière à ce que l’identité principale soit l’adresse de messagerie (courrier). Cette opération est effectuée dans le cadre du processus de personnalisation, en modifiant le champ **Nom d’utilisateur principal** dans les paramètres de synchronisation. Notez que ces paramètres déterminent aussi la façon dont les utilisateurs se connectent à Office 365, aux appareils Windows 10 et autres applications qui utilisent Azure AD comme magasin d’identités. ![Capture d’écran de l’identification des utilisateurs : liste déroulante Nom d’utilisateur principal](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Dans les paramètres de configuration de l’application à modifier, sélectionnez l’**Identité de connexion déléguée** à utiliser :
  - Nom d’utilisateur principal : joe@contoso.com  
  - Nom d’utilisateur principal alternatif : joed@contoso.local  
  - Partie correspondant au nom d’utilisateur dans le nom d’utilisateur principal : joe  
  - Partie correspondant au nom d’utilisateur dans le nom d’utilisateur principal alternatif : joed  
  - Nom du compte SAM local : suivant la configuration du contrôleur de domaine local

  ![Capture d’écran du menu déroulant Identité de connexion déléguée](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

### Résolution des problèmes liés à l’authentification unique pour différentes identités
Si une erreur se produit dans le processus d’authentification unique, elle apparaît dans le journal des événements d’ordinateur du connecteur, comme expliqué dans [Résoudre les problèmes](active-directory-application-proxy-troubleshoot.md). Toutefois, dans certains cas, la demande est correctement envoyée à l’application principale, mais celle-ci répond dans différentes réponses HTTP. Pour résoudre ces cas, il faut tout d’abord examiner le numéro d’événement 24029 sur l’ordinateur connecteur dans le journal des événements de session du proxy d’application. L’identité de l’utilisateur qui a été utilisée pour la délégation s’affiche dans le champ « utilisateur » dans les détails de l’événement. Pour activer le journal de session, sélectionnez **Afficher les journaux d’analyse et de débogage** dans le menu Affichage de l’Observateur d’événements.


## Voir aussi
Vous pouvez faire bien d’autres choses encore avec le Proxy d’application :


- [Publiez des applications avec le proxy d’application](active-directory-application-proxy-publish.md)
- [Publier des applications avec votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
- [Utiliser des applications utilisant les revendications](active-directory-application-proxy-claims-aware-apps.md)
- [Résoudre les problèmes rencontrés avec le proxy d’application](active-directory-application-proxy-troubleshoot.md)

## En savoir plus sur le Proxy d’application
- [Consultez notre aide en ligne](active-directory-application-proxy-enable.md)
- [Consultez le blog sur le Proxy d’application](http://blogs.technet.com/b/applicationproxyblog/)
- [Regardez nos vidéos sur Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg

<!---HONumber=AcomDC_0114_2016-->