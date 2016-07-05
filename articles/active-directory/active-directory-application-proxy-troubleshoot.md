<properties
	pageTitle="Résoudre les problèmes du proxy d’application | Microsoft Azure"
	description="Explique comment résoudre les erreurs dans le proxy d’application Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/22/2016"
	ms.author="kgremban"/>



# Résoudre les problèmes du proxy d’application

Si des erreurs se produisent dans l’accès à une application publiée ou dans la publication d’applications, vérifiez les options suivantes pour voir si le proxy d’application Microsoft Azure Active Directory fonctionne correctement :

- Ouvrez la console Services Windows et vérifiez que le service **Connecteur de proxy d’application Microsoft AAD** est activé et en cours d’exécution. Vous pouvez également consulter la page de propriétés du service Proxy d’application, comme le montre l’image suivante : ![Capture d’écran de la fenêtre Propriétés du connecteur de proxy d’application Microsoft AAD](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)

- Ouvrez l’Observateur d’événements et recherchez les événements liés au connecteur de proxy d’application sous **Applications and Services Logs** > **Microsoft** > **AadApplicationProxy** > **Connector** > **Admin**.
- Si nécessaire, des journaux plus détaillés sont disponibles en activant les journaux d’analyse et de débogage, et en activant le journal de session du connecteur de proxy d’application.


## Erreurs générales.

Erreur | Description | Résolution :
--- | --- | ---
Cette application d’entreprise n’est pas accessible. Vous n’êtes pas autorisé à accéder à cette application. Échec de l’autorisation. Veillez à affecter à l’utilisateur un accès à cette application. | Vous n’avez peut-être pas affecté cette application à l’utilisateur. | Accédez à l’onglet **Application** et, sous **Utilisateurs et groupes**, affectez cet utilisateur ou ce groupe d’utilisateurs à cette application.
Cette application d’entreprise n’est pas accessible. Vous n’êtes pas autorisé à accéder à cette application. Échec de l’autorisation. Assurez-vous que l’utilisateur a une licence pour Azure Active Directory Premium ou Basic. | Votre utilisateur peut recevoir cette erreur lors d’une tentative d’accès à l’application que vous avez publiée si l’administrateur de l’abonné n’a pas affecté explicitement une licence De base/Premium à l’utilisateur qui a tenté de se connecter. | Accédez à l’onglet **Licences** Active Directory de l’abonné et assurez-vous qu’une licence De base ou Premium est affectée à cet utilisateur ou à ce groupe.


## Résolution des problèmes du connecteur
Si l’inscription échoue pendant l’installation de l’Assistant Connecteur, vous pouvez voir la raison de l’échec en consultant le journal des événements sous **Journaux Windows** > **Application**, ou en exécutant la commande Windows PowerShell suivante.

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

| Erreur | Description | Résolution : |
| --- | --- | --- |
| Échec de l’inscription du connecteur : assurez-vous que vous avez activé le proxy d’application dans le portail de gestion Azure, et que vous avez entré correctement votre nom d’utilisateur et votre mot de passe Active Directory. Erreur : « Une ou plusieurs erreurs se sont produites. » | Vous avez fermé la fenêtre d’inscription sans effectuer de connexion à Azure AD. | Réexécutez l’Assistant Connecteur et inscrivez le connecteur. |
| Échec de l’inscription du connecteur : assurez-vous que vous avez activé le proxy d’application dans le portail de gestion Azure, et que vous avez entré correctement votre nom d’utilisateur et votre mot de passe Active Directory. Erreur : « AADSTS50001 : La ressource `https://proxy.cloudwebappproxy.net/registerapp` est désactivée. » | Le proxy d’application est désactivé. | Veillez à activer le proxy d’application dans le portail Azure Classic avant d’essayer d’inscrire le connecteur. Pour plus d’informations sur l’activation du proxy d’application, consultez [Activer le services du proxy d’application](active-directory-application-proxy-enable.md). |
| Échec de l’inscription du connecteur : assurez-vous que vous avez activé le proxy d’application dans le portail de gestion Azure, et que vous avez entré correctement votre nom d’utilisateur et votre mot de passe Active Directory. Erreur : « Une ou plusieurs erreurs se sont produites. » | Si la fenêtre d’inscription s’ouvre puis ferme immédiatement sans vous permettre de vous connecter, vous recevrez probablement cette erreur. Cette erreur se produit quand il existe une erreur réseau sur votre système. | Assurez-vous qu’il est possible de se connecter à partir d’un navigateur à un site web public et que les ports sont ouverts comme spécifié dans les [conditions préalables du proxy d’application](active-directory-application-proxy-enable.md). |
| Échec de l’inscription du connecteur : assurez-vous que votre ordinateur est connecté à Internet. Erreur : « Il n’existait pas de point de terminaison à l’écoute sur `https://connector.msappproxy.net:9090/register/RegisterConnector` pouvant accepter le message. Ceci est souvent dû à une adresse ou une action SOAP incorrecte. S’il est présent, voir l’élément InnerException pour plus d’informations. » | Si vous vous connectez en utilisant votre nom d’utilisateur et votre mot de passe Azure AD, mais que vous recevez ensuite cette erreur, la raison peut en être que tous les ports au-dessus de 8081 sont bloqués. | Assurez-vous que les ports nécessaires sont ouverts. Pour plus d’informations, consultez [Conditions préalables pour le proxy d’application](active-directory-application-proxy-enable.md). |
| L’erreur est présentée en clair dans la fenêtre d’inscription. Impossible de continuer. Seule la fermeture de la fenêtre est possible. | Vous avez entré un nom d’utilisateur ou un mot de passe incorrect. | Réessayez. |
| Échec de l’inscription du connecteur : assurez-vous que vous avez activé le proxy d’application dans le portail de gestion Azure, et que vous avez entré correctement votre nom d’utilisateur et votre mot de passe Active Directory. Erreur : « AADSTS50059 : pas d’informations d’identification de locataire trouvées dans la demande ou déduite des informations d’identification fournies. Échec de la recherche selon l’URI du principal du service. | Vous essayez de vous connecter en utilisant un compte Microsoft mais pas un domaine qui fait partie de l’ID d’organisation de l’annuaire auquel vous tentez d’accéder. | Assurez-vous que l’administrateur fait partie du même nom de domaine que le domaine du locataire. Par exemple, si le domaine Azure AD est contoso.com, l’administrateur doit être admin@contoso.com. |
| Impossible de récupérer la stratégie d’exécution actuelle pour l’exécution de scripts PowerShell. | En cas d’échec de l’installation du connecteur, vérifiez que la stratégie d’exécution de PowerShell n’est pas désactivée. | Ouvrez l’Éditeur de stratégie de groupe. Accédez à **Configuration ordinateur** > **Modèles d’administration** > **Composants Windows** > **Windows PowerShell** et double-cliquez sur **Activer l’exécution des scripts**. Cette option peut être définie sur **Non configuré** ou **Activé**. Si elle est définie sur **Activé**, vérifiez que sous Options, la stratégie d’exécution est définie sur **Autoriser les scripts locaux et les scripts signés distants** ou sur **Autoriser tous les scripts**. |
| Échec du téléchargement de la configuration par le connecteur. | Le certificat client du connecteur, qui est utilisé pour l’authentification, est expiré. Ceci peut également se produire si le connecteur est installé derrière un proxy. Dans ce cas, le connecteur ne peut pas accéder à Internet et ne sera pas en mesure de fournir des applications aux utilisateurs distants. | Renouvelez l’approbation manuellement à l’aide de l’applet de commande `Register-AppProxyConnector` dans Windows PowerShell. Si votre connecteur est derrière un proxy, il est nécessaire d’octroyer l’accès à Internet aux comptes du connecteur « services réseau » et « système local ». Ceci peut être effectué en leur accordant l’accès au proxy ou en les configurant pour qu’ils ignorent le proxy. |
| Échec de l’inscription du connecteur : vous devez être un administrateur global de votre Active Directory pour inscrire le connecteur. Erreur : « La demande d’inscription a été refusée. » | L’alias avec lequel vous essayez de vous connecter n’est pas un administrateur sur ce domaine. Votre connecteur est toujours installé pour l’annuaire qui détient le domaine de l’utilisateur. | Assurez-vous que l’administrateur que vous voulez utiliser pour vous connecter dispose des autorisations globales sur le locataire Azure AD.|


## Erreurs Kerberos

| Erreur | Description | Résolution : |
| --- | --- | --- |
| Impossible de récupérer la stratégie d’exécution actuelle pour l’exécution de scripts PowerShell. | En cas d’échec de l’installation du connecteur, vérifiez que la stratégie d’exécution de PowerShell n’est pas désactivée. | Ouvrez l’Éditeur de stratégie de groupe. Accédez à **Configuration ordinateur** > **Modèles d’administration** > **Composants Windows** > **Windows PowerShell** et double-cliquez sur **Activer l’exécution des scripts**. Cette option peut être définie sur **Non configuré** ou **Activé**. Si elle est définie sur **Activé**, vérifiez que sous Options, la stratégie d’exécution est définie sur **Autoriser les scripts locaux et les scripts signés distants** ou sur **Autoriser tous les scripts**. |
| 12008 - Azure AD a dépassé le nombre maximal de tentatives d’authentification Kerberos autorisé auprès du serveur principal. | Cet événement peut indiquer une configuration incorrecte entre Azure AD et le serveur d’applications principal, ou un problème de configuration de la date/heure sur les deux ordinateurs. | Le serveur principal a refusé le ticket Kerberos créé par Azure AD. Vérifiez que les configurations d’Azure AD et du serveur d’applications principal sont correctes. Assurez-vous que les configurations de la date/heure sur Azure AD et sur le serveur d’applications principal sont synchronisées. |
| 13016 - Azure AD ne peut pas récupérer un ticket Kerberos pour l’utilisateur, car il n’existe pas d’UPN dans le jeton de périphérie ou dans le cookie d’accès. | Il existe un problème avec la configuration de STS (SharePoint Team Services). | Corrigez la configuration de la revendication UPN dans STS. |
| 13019 - Azure AD ne peut pas récupérer un ticket Kerberos pour l’utilisateur en raison de l’erreur API générale suivante. | Cet événement peut indiquer une configuration incorrecte entre Azure AD et le serveur contrôleur de domaine, ou un problème de configuration de la date/heure sur les deux ordinateurs. | Le contrôleur de domaine a refusé le ticket Kerberos créé par Azure AD. Vérifiez que les configurations d’Azure AD et du serveur d’applications principal sont correctes, en particulier les configurations du nom de principal du service (SPN). Assurez-vous qu’Azure AD est joint au même domaine que le contrôleur de domaine pour que le contrôleur de domaine établisse la relation d’approbation avec Azure AD. Assurez-vous que les configurations de la date/heure sur Azure AD et sur le contrôleur de domaine sont synchronisées. |
| 13020 - Azure AD ne peut pas récupérer un ticket Kerberos pour l’utilisateur, car le SPN du serveur principal n’est pas défini. | Cet événement peut indiquer une configuration incorrecte entre Azure AD et le serveur contrôleur de domaine, ou un problème de configuration de la date/heure sur les deux ordinateurs. | Le contrôleur de domaine a refusé le ticket Kerberos créé par Azure AD. Vérifiez que les configurations d’Azure AD et du serveur d’applications principal sont correctes, en particulier les configurations du nom de principal du service (SPN). Assurez-vous qu’Azure AD est joint au même domaine que le contrôleur de domaine pour que le contrôleur de domaine établisse la relation d’approbation avec Azure AD. Assurez-vous que les configurations de la date/heure sur Azure AD et sur le contrôleur de domaine sont synchronisées. |
| 13022 - Azure AD ne peut pas authentifier l’utilisateur, car le serveur principal répond aux tentatives d’authentification Kerberos avec une erreur HTTP 401. | Cet événement peut indiquer une configuration incorrecte entre Azure AD et le serveur d’applications principal, ou un problème de configuration de la date/heure sur les deux ordinateurs. | Le serveur principal a refusé le ticket Kerberos créé par Azure AD. Vérifiez que les configurations d’Azure AD et du serveur d’applications principal sont correctes. Assurez-vous que les configurations de la date/heure sur Azure AD et sur le serveur d’applications principal sont synchronisées. |
| Le site web ne peut pas afficher la page. | Votre utilisateur peut recevoir cette erreur en tentant d’accéder à l’application que vous avez publiée si l’application est une application avec l’authentification Windows intégrée. Le SPN défini pour cette application est peut-être incorrect. | Pour les applications avec l’authentification Windows intégrée : assurez-vous que le SPN configuré pour cette application est correct. |
| Le site web ne peut pas afficher la page. | Votre utilisateur peut obtenir cette erreur en tentant d’accéder à l’application que vous avez publiée si l’application est une application OWA. La raison peut en être l’une des suivantes : <br> - Le SPN défini pour cette application est incorrect. <br> - L’utilisateur qui a tenté d’accéder à l’application utilise un compte Microsoft au lieu du compte d’entreprise approprié pour se connecter, ou bien l’utilisateur est un utilisateur invité. <br> - L’utilisateur qui a tenté d’accéder à l’application n’est pas défini correctement pour cette application du côté local. | Voici les mesures d’atténuation à prendre dans ce cas : <br> - Assurez-vous que le SPN configuré pour cette application est correct. <br> - Vérifiez que l’utilisateur se connecte en utilisant son compte d’entreprise qui correspond au domaine de l’application publiée. Les utilisateurs d’un compte Microsoft et les invités ne peuvent pas accéder aux applications avec l’authentification Windows intégrée. <br> - Assurez-vous que cet utilisateur a les autorisations appropriées, telles qu’elles sont définies pour cette application principale sur l’ordinateur local. |
| Cette application d’entreprise n’est pas accessible. Vous n’êtes pas autorisé à accéder à cette application. Échec de l’autorisation. Veillez à affecter à l’utilisateur un accès à cette application. | Votre utilisateur peut recevoir cette erreur en tentant d’accéder à l’application que vous avez publiée s’il utilise un compte Microsoft au lieu du compte d’entreprise approprié pour se connecter, ou bien si l’utilisateur est un utilisateur invité. | Les utilisateurs d’un compte Microsoft et les invités ne peuvent pas accéder aux applications avec l’authentification Windows intégrée. Assurez-vous que l’utilisateur se connecte en utilisant son compte d’entreprise qui correspond au domaine de l’application publiée. |
| Cette application d’entreprise n’est pas accessible pour l’instant. Réessayez ultérieurement... Le connecteur a dépassé le délai d’expiration. | Votre utilisateur peut recevoir cette erreur en tentant d’accéder à l’application que vous avez publiée s’il n’est pas correctement défini pour cette application du côté local. | Assurez-vous que cet utilisateur a les autorisations appropriées, telles qu’elles sont définies pour cette application principale sur l’ordinateur local. |


## Voir aussi

- [Activation du proxy d’application Azure AD](active-directory-application-proxy-enable.md)
- [Publiez des applications avec le proxy d’application](active-directory-application-proxy-publish.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)

Pour les dernières nouvelles et mises à jour, visitez [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/) (blog sur les proxys d’application)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png

<!---HONumber=AcomDC_0622_2016-->