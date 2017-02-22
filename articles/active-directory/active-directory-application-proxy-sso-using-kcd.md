---
title: "Authentification unique avec le proxy d’application | Microsoft Docs"
description: "Explique comment fournir l’authentification unique à l’aide du proxy d’application Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: ded0d9c9-45f6-47d7-bd0f-3f7fd99ab621
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1eada96773b1d9c6adb9326c9100da7cde8abf77
ms.openlocfilehash: 8df936a03868412adf34672108f40829c41f33ab

---

# <a name="single-sign-on-with-application-proxy"></a>Authentification unique avec le proxy d’application
L’authentification unique est un élément clé du proxy d’application Azure AD. Elle procure une expérience utilisateur optimale grâce aux étapes suivantes :

1. L’utilisateur se connecte au cloud.  
2. Toutes les validations de sécurité sont effectuées dans le cloud (pré-authentification)  
3. Lorsque la demande est envoyée à l’application locale, le connecteur proxy d’application emprunte l’identité de l’utilisateur. L’application principale pense qu’il s’agit d’un utilisateur standard qui se connecte depuis un appareil faisant partie du domaine.

![Diagramme d’accès de l’utilisateur final au réseau d’entreprise via le proxy d’application](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Le proxy d’application Azure AD vous permet d’offrir une expérience d’authentification unique à vos utilisateurs. Utilisez les instructions suivantes pour publier vos applications en utilisant l’authentification unique :

## <a name="sso-for-on-prem-iwa-apps-using-kcd-with-application-proxy"></a>Authentification unique pour les applications avec authentification Windows intégrée locales en utilisant la délégation Kerberos contrainte avec le proxy d’application
Vous pouvez activer l’authentification unique pour vos applications avec l’authentification Windows intégrée en attribuant aux connecteurs du proxy d’application dans Active Directory l’autorisation d’emprunter l’identité des utilisateurs et d’envoyer et recevoir des jetons en leur nom.

### <a name="network-diagram"></a>Diagramme du réseau
Ce diagramme explique le flux quand un utilisateur tente d’accéder à une application locale qui utilise I’authentification Windows intégrée.

![Diagramme de flux de l’authentification Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. L’utilisateur entre l’URL pour accéder à l’application locale via le proxy d’application.
2. Le proxy d’application redirige la demande vers les services d’authentification d’Azure AD pour effectuer la préauthentification. À ce stade, Azure AD applique les stratégies d’authentification et d’autorisation applicables, comme l’authentification multifacteur. Si l’utilisateur est validé, Azure AD crée un jeton et l’envoie à l’utilisateur.
3. L’utilisateur transmet le jeton au proxy d’application.
4. Le proxy d’application valide le jeton et y récupère le nom d’utilisateur principal (UPN), puis envoie la demande, l’UPN et le nom de principal du service (SPN) au connecteur via un canal sécurisé doublement authentifié.
5. Le connecteur effectue une négociation de délégation Kerberos contrainte avec Active Directory local, en empruntant l’identité de l’utilisateur pour obtenir un jeton Kerberos pour l’application.
6. Active Directory envoie le jeton Kerberos de l’application au connecteur.
7. Le connecteur envoie la demande d’origine au serveur d’applications, en utilisant le jeton Kerberos reçu d’Active Directory.
8. L’application envoie la réponse au connecteur, qui est ensuite retournée au service de proxy d’application et enfin à l’utilisateur.

### <a name="prerequisites"></a>Composants requis
Avant de commencer avec l’authentification unique pour le proxy d’application, vérifiez que votre environnement est prêt avec les paramètres et configurations suivants :

* Vos applications, comme les applications web SharePoint, sont configurées pour utiliser l’authentification Windows intégrée. Pour plus d’informations, consultez [Activer la prise en charge de l’authentification Kerberos](https://technet.microsoft.com/library/dd759186.aspx) ou, pour SharePoint, consultez [Planifier l’authentification Kerberos dans SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Toutes vos applications disposent de noms de principal de service.
* Le serveur exécutant le connecteur et le serveur exécutant l’application sont joints au domaine et font partie du même domaine ou de domaines sécurisés. Pour plus d’informations sur la jonction à un domaine, consultez [Joindre un ordinateur à un domaine](https://technet.microsoft.com/library/dd807102.aspx).
* Le serveur exécutant le connecteur est autorisé à lire le TokenGroupsGlobalAndUniversal des utilisateurs. Il s’agit d’un paramètre par défaut qui peut avoir été affecté par la sécurisation renforcée de l’environnement. Consultez l’article [KB2009157](https://support.microsoft.com/en-us/kb/2009157)pour en savoir plus sur ce paramètre.

### <a name="active-directory-configuration"></a>Configuration d’Active Directory
La configuration d’Active Directory varie selon que votre connecteur de proxy d’application et le serveur publié sont ou non dans le même domaine.

#### <a name="connector-and-published-server-in-the-same-domain"></a>Connecteur et serveur publié dans le même domaine
1. Dans Active Directory, accédez à **Outils** > **Utilisateurs et ordinateurs**.
2. Sélectionnez le serveur exécutant le connecteur.
3. Cliquez avec le bouton droit, puis sélectionnez **Properties** > **Délégation**.
4. Sélectionnez **N’approuver cet ordinateur que pour la délégation aux services spécifiés**. Sous **Services auxquels ce compte peut présenter des informations d’identification déléguées**, ajoutez la valeur de l’identité du nom de principal du service (SPN) du serveur d’applications.
5. Ceci permet au connecteur de proxy d’application d’emprunter l’identité des utilisateurs dans Active Directory pour les applications définies dans la liste.

![Capture d’écran de la fenêtre Propriétés du connecteur-SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-published-server-in-different-domains"></a>Connecteur et serveur publié dans des domaines différents
1. Pour obtenir la liste des conditions préalables à l’utilisation de la délégation Kerberos contrainte entre domaines, consultez [Délégation Kerberos contrainte entre domaines](https://technet.microsoft.com/library/hh831477.aspx).
2. Dans Windows 2012 R2, utilisez la propriété `principalsallowedtodelegateto` sur le serveur du connecteur pour permettre au proxy d’application de déléguer pour le serveur de connecteur, où le serveur publié est `sharepointserviceaccount` et le serveur de délégation est `connectormachineaccount`.

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

> [!NOTE]
> `sharepointserviceaccount` peut être le compte d’ordinateur SPS ou un compte de service sous lequel s’exécute le pool d’applications SPS.
>
>

### <a name="azure-classic-portal-configuration"></a>Configuration du portail Azure Classic
1. Publiez votre application en suivant les instructions décrites dans [Publier des applications avec le proxy d’application](active-directory-application-proxy-publish.md). Veillez à sélectionner **Azure Active Directory** comme **méthode de préauthentification**.
2. Dès que votre application figure dans la liste des applications, sélectionnez-la et cliquez sur **Configurer**.
3. Sous **Propriétés**, définissez **Méthode d’authentification interne** sur **Authentification Windows intégrée**.  
   ![Configuration avancée des applications](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. Entrez le **SPN d’application interne** du serveur d’applications. Dans cet exemple, le nom de principal du service pour notre application publiée est http/lob.contoso.com.  

> [!IMPORTANT]
> Si votre UPN local et l’UPN dans Azure Active Directory ne sont pas identiques, vous devez configurer [l’identité de connexion déléguée](#delegated-login-identity) pour que la pré-authentification fonctionne.
>
>

|  |  |
| --- | --- |
| Méthode d’authentification interne |Si vous utilisez Azure AD pour la préauthentification, vous pouvez définir une méthode d’authentification interne pour permettre à vos utilisateurs de profiter de l’authentification unique pour cette application. <br><br> Sélectionnez **l’authentification Windows intégrée** (IWA) si votre application l’utilise. Vous pouvez configurer la délégation Kerberos contrainte (KCD) pour activer l’authentification unique pour cette application. Les applications qui utilisent l’authentification Windows intégrée doivent être configurées à l’aide de la délégation Kerberos contrainte, sinon le proxy d’application ne pourra pas les publier. <br><br> Sélectionnez **Aucune** si votre application n’utilise pas l’authentification Windows intégrée. |
| SPN d’application interne |Il s’agit du nom de principal du service (SPN) de l’application interne tel qu’il est configuré Azure AD local. Le SPN est utilisé par le connecteur du proxy d’application pour extraire les jetons Kerberos pour l’application en utilisant la délégation Kerberos contrainte. |

## <a name="sso-for-non-windows-apps"></a>Authentification unique pour les applications non Windows
Le flux de délégation Kerberos dans le proxy d’application Azure AD démarre quand Azure AD authentifie l’utilisateur dans le cloud. Une fois que la demande est disponible localement, le connecteur du proxy d’application AD Azure émet un ticket Kerberos pour le compte de l’utilisateur en interagissant avec l’annuaire Active Directory local. Ce processus est appelé délégation Kerberos contrainte (KCD). Au cours de la phase suivante, une demande est envoyée à l’application principale avec ce ticket Kerberos. Plusieurs protocoles définissent la manière d’envoyer ces demandes. La plupart des serveurs non Windows supposent qu’il s’agit de Negotiate/SPNego, qui est maintenant pris en charge sur le proxy d’application Azure AD.

![Diagramme de l’authentification unique non Windows](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### <a name="delegated-login-identity"></a>identité de connexion déléguée
L’identité de connexion déléguée vous permet de gérer deux scénarios de connexion différents :

* Les applications non Windows qui obtiennent généralement l’identité de l’utilisateur sous la forme d’un nom d’utilisateur ou d’un nom de compte SAM, pas d’une adresse de messagerie (username@domain).
* Les configurations de connexion alternatives où l’UPN dans Azure AD et l’UPN dans votre Active Directory local sont différents.

Avec le proxy d’application, vous pouvez sélectionner l’identité à utiliser pour obtenir le ticket Kerberos. Ce paramètre est à configurer application par application. Certaines de ces options sont adaptées pour les systèmes qui n’acceptent pas le format d’adresse de messagerie, tandis que d’autres sont conçues pour les connexions alternatives.

![Capture d’écran du paramètre Identité de connexion déléguée](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Si l’identité de connexion déléguée est utilisée, il se peut que la valeur ne soit pas la même pour l’ensemble des domaines ou forêts de votre organisation. Vous pouvez éviter ce problème en publiant ces applications deux fois à l’aide de deux groupes de connecteurs différents. Dans la mesure où chaque application possède sa propre audience utilisateur, vous pouvez joindre ses connecteurs à un autre domaine.

## <a name="working-with-sso-when-on-premises-and-cloud-identities-are-not-identical"></a>Utilisation de l’authentification unique quand des identités locales et sur le cloud ne sont pas identiques
Sauf s’il est configuré différemment, le proxy d’application suppose que les utilisateurs ont exactement la même identité dans le cloud et localement. Vous pouvez configurer, pour chaque application, l’identité qui doit être utilisée pendant l’exécution de l’authentification unique.  

Grâce à cette fonctionnalité, de nombreuses organisations qui ont des identités locales et cloud différentes peuvent mettre en œuvre l’authentification unique des applications cloud aux applications locales sans contraindre les utilisateurs à entrer des noms d’utilisateur et des mots de passe différents. Cela inclut les organisations qui :

* disposent de plusieurs domaines en interne (joe@us.contoso.com, joe@eu.contoso.com) et d’un domaine unique dans le cloud (joe@contoso.com).
* disposent d’un nom de domaine non routable en interne (joe@contoso.usa) et d’un nom de domaine légal dans le cloud.
* n’utilisent pas de noms de domaine en interne (joe) ;
* utilisent différents alias localement et dans le cloud, Par exemple, joe-johns@contoso.com vs. joej@contoso.com  

Cette configuration convient aussi pour les applications qui n’acceptent pas d’adresses sous forme d’adresses de messagerie, scénario très courant pour les serveurs principaux non Windows.

### <a name="setting-sso-for-different-cloud-and-on-prem-identities"></a>Configuration de l’authentification unique pour différentes identités sur le cloud et localement
1. Configurez les paramètres Azure AD Connect de manière à ce que l’identité principale soit l’adresse de messagerie (courrier). Cette opération est effectuée dans le cadre du processus de personnalisation, en modifiant le champ **Nom d’utilisateur principal** dans les paramètres de synchronisation. Ces paramètres déterminent également comment les utilisateurs se connectent à Office 365, aux appareils Windows 10 et autres applications qui utilisent Azure AD comme magasin d’identités.  
   ![Capture d’écran de l’identification des utilisateurs : liste déroulante Nom d’utilisateur principal](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Dans les paramètres de configuration de l’application à modifier, sélectionnez l’ **Identité de connexion déléguée** à utiliser :

   * Nom d’utilisateur principal : joe@contoso.com  
   * Nom d’utilisateur principal alternatif : joed@contoso.local  
   * Partie correspondant au nom d’utilisateur dans le nom d’utilisateur principal : joe  
   * Partie correspondant au nom d’utilisateur dans le nom d’utilisateur principal alternatif : joed  
   * Nom du compte SAM local : suivant la configuration du contrôleur de domaine local

   ![Capture d’écran du menu déroulant Identité de connexion déléguée](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)  

### <a name="troubleshooting-sso-for-different-identities"></a>Résolution des problèmes liés à l’authentification unique pour différentes identités
Si une erreur se produit dans le processus d’authentification unique, elle apparaît dans le journal des événements d’ordinateur du connecteur, comme expliqué dans la section [Résoudre les problèmes](active-directory-application-proxy-troubleshoot.md).
Toutefois, dans certains cas, la demande est correctement envoyée à l’application principale, mais celle-ci répond dans plusieurs réponses HTTP. Pour résoudre ces cas, il faut tout d’abord examiner le numéro d’événement 24029 sur l’ordinateur connecteur dans le journal des événements de session du proxy d’application. L’identité de l’utilisateur qui a été utilisée pour la délégation s’affiche dans le champ « utilisateur » dans les détails de l’événement. Pour activer le journal de session, sélectionnez **Afficher les journaux d’analyse et de débogage** dans le menu Affichage de l’Observateur d’événements.

## <a name="see-also"></a>Voir aussi
* [Publiez des applications avec le proxy d’application](active-directory-application-proxy-publish.md)
* [Résoudre les problèmes rencontrés avec le proxy d’application](active-directory-application-proxy-troubleshoot.md)
* [Utiliser des applications utilisant les revendications](active-directory-application-proxy-claims-aware-apps.md)
* [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)

Pour les dernières nouvelles et mises à jour, consultez le site [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg



<!--HONumber=Dec16_HO4-->


