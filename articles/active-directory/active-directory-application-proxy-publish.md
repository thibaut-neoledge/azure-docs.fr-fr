<properties
	pageTitle="Publication d’applications avec le proxy d’application Azure AD | Microsoft Azure"
	description="Explique comment publier des applications locales avec le Proxy d’application Azure AD."
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
	ms.date="01/06/2016"
	ms.author="kgremban"/>


# Publier des applications avec le Proxy d’application Azure AD

> [AZURE.NOTE]Le Proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou Basic d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

Une fois que le Proxy d’application Microsoft Azure Active Directory (AD) est activé, vous pouvez publier des applications pour les rendre accessibles à vos utilisateurs situés en dehors de votre réseau privé.

Cet article vous guide tout au long de la procédure de publication d’applications exécutées sur votre réseau local et pour lesquelles vous souhaitez activer l’accès à distance sécurisé hors de votre réseau.

> [AZURE.NOTE]Pour vérifier que le connecteur fonctionne correctement, la première application que vous publiez doit être un site web accessible depuis votre réseau privé afin de vous assurer que les utilisateurs puissent y accéder à partir d’Internet avant de véritablement publier une application.


## Publier une application à l’aide de l’Assistant

1. Ouvrez le navigateur de votre choix et accédez au Portail Azure Classic.
2. Dans le volet gauche du portail Azure Classic, cliquez sur l’onglet **Active Directory**.
3. Cliquez sur l’annuaire dans lequel vous avez activé le Proxy d’application et pour lequel vous souhaitez publier une application (par exemple, Wingtip Toys).
4. Cliquez sur l’onglet **Applications**, puis cliquez sur le bouton **Ajouter** au bas de l’écran.

	![Ajouter l’application](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Publier une application qui sera accessible hors de votre réseau**.

	![Nouvelle application accessible hors de votre réseau](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

6. Suivez les instructions à l’écran pour fournir les informations suivantes sur votre application :

| **Propriété** | **Détails** |
|---|---|
| URL externe | Il s’agit de l’URL du service cloud utilisé pour accéder à l’application hors de votre réseau privé. L’URL est automatiquement générée en fonction du nom fourni, avec le suffixe msappproxy.net. |
| Méthode de pré-authentification | Définissez le type de méthode de pré-authentification que l’application doit utiliser : <br><br> a. Azure Active Directory : quand un utilisateur tente d’accéder à une application, le Proxy d’application le redirige pour qu’il se connecte avec Azure AD qui authentifie alors l’utilisateur, garantissant ainsi qu’il dispose des autorisations nécessaires pour l’annuaire et l’application. <br><br> b. Passthrough : la pré-authentification n’est pas effectuée. |
| Protocole d’URL externe | Par défaut, les applications sont publiées avec le protocole HTTPS. Le service redirige automatiquement les utilisateurs qui tapent l’URL en utilisant http. <br><br> Pour activer le protocole HTTP pour une application interne, vous devez définir la méthode de pré-authentification Passthrough. Vous pourrez alors remplacer le protocole d’URL externe HTTPS par le protocole HTTP. Notez que la publication d’applications avec le protocole HTTP peut créer des problèmes de sécurité pour votre application et pour vos utilisateurs. <br><br> Vous pouvez insérer un domaine personnalisé au lieu d’utiliser le suffixe par défaut msappproxy.net. Pour plus d’informations, consultez [Utilisation des domaines personnalisés](active-directory-application-proxy-custom-domains.md). |
| URL interne | Il s’agit de l’URL interne utilisée par le connecteur Proxy d’application pour accéder à l’application en interne. Il doit s’agir de l’URL de l’application publiée qui est utilisée pour accéder à l’application depuis votre réseau privé. Il s’agit d’une URL valide sans aucun espace ni symbole. <br><br> Vous pouvez spécifier un chemin spécifique sur le serveur principal à publier, alors que le reste du serveur n’est pas publié. Cela vous permet de publier, par exemple, différents sites situés sur le même serveur SharePoint avec différents noms et règles d'accès. <br><br> Le chemin est spécifié dans le champ URL interne et est visible dans l’URL externe. Les chemins d'accès internes et externes doivent être identiques. |

  ![Propriétés de l’application](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)

  Pour terminer l’Assistant, cliquez sur la coche en bas de l’écran. L’application est maintenant définie dans Azure AD.


## Affecter des utilisateurs et des groupes à l’application

1. Pour les applications qui ont été pré-authentifiées, vous devez affecter les utilisateurs et les groupes qui ont accès à l’application. Dans le cas des applications Passthrough, l’accès est possible pour tous les utilisateurs. Toutefois, pour que l’utilisateur puisse voir l’application dans sa liste d’applications, vous devez affecter l’application à cet utilisateur.

2. Après avoir terminé l’Assistant Ajouter une application, la page Démarrage rapide Proxy d’application s’affiche. Pour assigner des utilisateurs, cliquez sur **Affecter des utilisateurs**.

	![Écran de démarrage rapide du Proxy d’application -- assigner des utilisateurs](./media/active-directory-application-proxy-publish/quickstart.png)

3. Sélectionnez chaque utilisateur ou groupe que vous souhaitez affecter à cette application, puis cliquez sur **Affecter**.

> [AZURE.NOTE]Pour les applications avec authentification Windows intégrée, vous pouvez affecter uniquement les utilisateurs et les groupes qui ont été synchronisés à partir de votre Active Directory local. Il n’est pas possible d’affecter les utilisateurs qui se connectent à l’aide d’un compte Microsoft et les invités aux applications publiées avec le Proxy d’application Azure Active Directory. Vérifiez que les utilisateurs que vous affectez se connectent avec leurs informations d’identification qui doivent faire partie du même domaine que l’application que vous publiez.


## Configuration avancée

1. Vous pouvez modifier les applications publiées ou configurer les options avancées, telles que l’authentification unique pour les applications locales, à partir de la page Configurer.

	![Configuration avancée](./media/active-directory-application-proxy-publish/advancedconfig.png)

2. Sélectionnez l’application, puis cliquez sur **Configurer**. Les options suivantes sont disponibles :

**Paramètre** | **Détails**
---|---
Nom | Fournissez un nom descriptif pour votre application
URL externe | Il s’agit de l’URL du service cloud utilisé pour accéder à l’application hors de votre réseau privé. L’URL est automatiquement générée en fonction du nom fourni, avec le suffixe msappproxy.net.
Méthode de pré-authentification | Définissez le type de méthode de pré-authentification que l’application doit utiliser : <br><br> a. Azure Active Directory : quand un utilisateur tente d’accéder à une application, le Proxy d’application le redirige pour qu’il se connecte avec Azure AD qui authentifie alors l’utilisateur, garantissant ainsi qu’il dispose des autorisations nécessaires pour l’annuaire et l’application. <br><br> b. Passthrough : la pré-authentification n’est pas effectuée.
Protocole d’URL externe | Par défaut, les applications sont publiées avec le protocole HTTPS. Le service redirige automatiquement les utilisateurs qui tapent l’URL en utilisant http. <br><br> Pour activer le protocole HTTP pour une application interne, vous devez définir la méthode de pré-authentification Passthrough. Vous pourrez alors remplacer le protocole d’URL externe HTTPS par le protocole HTTP. Notez que la publication d’applications avec le protocole HTTP peut créer des problèmes de sécurité pour votre application et pour vos utilisateurs. <br><br> Vous pouvez insérer un domaine personnalisé au lieu d’utiliser le suffixe par défaut msappproxy.net. Pour plus d’informations, consultez [Utilisation des domaines personnalisés](active-directory-application-proxy-custom-domains.md).
URL interne | Il s’agit de l’URL interne utilisée par le connecteur Proxy d’application pour accéder à l’application en interne. Il doit s’agir de l’URL de l’application publiée qui est utilisée pour accéder à l’application depuis votre réseau privé. Il s’agit d’une URL valide sans aucun espace ni symbole. <br><br> Vous pouvez spécifier un chemin spécifique sur le serveur principal à publier, alors que le reste du serveur n’est pas publié. Cela vous permet de publier, par exemple, différents sites situés sur le même serveur SharePoint avec différents noms et règles d'accès. <br><br> Le chemin est spécifié dans le champ URL interne et est visible dans l’URL externe. Les chemins d'accès internes et externes doivent être identiques.
Traduire des URL dans les en-têtes | Pour les applications qui nécessitent que les en-têtes d’hôte HTTP ne soient pas traduits (par exemple, certaines configurations de SharePoint), définissez ce paramètre sur **Non**. La traduction d’en-tête est alors désactivée pour les en-têtes de demande et de réponse.
Méthode d’authentification interne | Si vous utilisez le Proxy d’application pour la pré-authentification, vous pouvez définir une méthode d’authentification interne pour permettre à vos utilisateurs de bénéficier de l’authentification unique (SSO) pour cette application. <br><br> Sélectionnez **Authentification Windows intégrée (IWA)** si votre application utilise cette méthode. Vous pouvez configurer la délégation Kerberos contrainte (KCD) pour activer l’authentification unique pour cette application. Les applications qui utilisent l’authentification Windows intégrée doivent être configurées à l’aide de la délégation Kerberos contrainte, sinon le proxy d’application ne sera pas en mesure de les publier. <br><br> Sélectionnez **Aucune** si votre application n’utilise pas l’authentification Windows intégrée. <br><br> Pour plus d’informations, consultez [Authentification unique avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md).
SPN d’application interne | C’est le nom de principal du service (SPN) de l’application, tel qu’il est configuré dans le Proxy d’application local. Le SPN est utilisé par le connecteur du Proxy d’application pour extraire les jetons Kerberos pour l’application à l’aide de la délégation Kerberos KCD. <br><br> Pour plus d’informations, consultez [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md).

Une fois que vous avez publié des applications qui utilisent le Proxy d’application Azure Active Directory, elles apparaissent dans la liste des applications dans Azure AD et vous pouvez les gérer à partir de cet emplacement.

Si vous désactivez les services de Proxy d’application après avoir publié des applications, les applications ne sont pas supprimées, mais elles ne seront plus accessibles hors de votre réseau privé.

Pour afficher une application et vous assurer qu’elle est accessible, double-cliquez sur son nom. Si le service Proxy d’application est désactivé et que l’application n’est pas disponible, un message d’avertissement s’affiche en haut de l’écran.

Pour supprimer une application, sélectionnez-la dans la liste, puis cliquez sur **Supprimer**.

## Voir aussi
Vous pouvez faire bien d’autres choses encore avec le Proxy d’application :

- [Activer le proxy d’application](active-directory-application-proxy-enable.md)
- [Publier des applications avec votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
- [Utiliser des applications utilisant les revendications](active-directory-application-proxy-claims-aware-apps.md)
- [Résoudre les problèmes rencontrés avec le proxy d’application](active-directory-application-proxy-troubleshoot.md)

## En savoir plus sur le Proxy d’application
- [Consultez notre aide en ligne](active-directory-application-proxy-enable.md)
- [Consultez le blog sur le Proxy d’application](http://blogs.technet.com/b/applicationproxyblog/)
- [Regardez nos vidéos sur Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Ressources supplémentaires

* [À propos de la délégation Kerberos contrainte](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=AcomDC_0114_2016-->