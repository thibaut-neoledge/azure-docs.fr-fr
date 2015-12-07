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
	ms.date="10/19/2015"
	ms.author="kgremban"/>


# Publier des applications avec le Proxy d’application Azure AD

> [AZURE.NOTE]Le Proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou Basic d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

Une fois que le Proxy d’application Microsoft Azure AD est activé, vous pouvez publier des applications pour les rendre accessibles à vos utilisateurs hors de votre réseau privé.

Cette section vous guide dans les étapes nécessaires à la publication d’applications exécutées sur votre réseau local et pour lesquelles vous souhaitez activer l’accès à distance sécurisé hors de votre réseau.

> [AZURE.NOTE]Pour vérifier que le connecteur fonctionne correctement, la première application que vous publiez doit être un site web accessible depuis votre réseau privé afin de vous assurer que les utilisateurs puissent y accéder à partir d’Internet avant de véritablement publier une application.

## Publier une application à l’aide de l’Assistant

1. Ouvrez le navigateur de votre choix et accédez au portail de gestion Azure.
2. Dans le volet gauche du portail de gestion Azure, cliquez sur l’onglet Active Directory.
3. Cliquez sur l’annuaire dans lequel vous avez activé le Proxy d’application et pour lequel vous souhaitez publier une application (par exemple, Wingtip Toys).
4. Cliquez sur l’onglet **Applications**, puis cliquez sur le bouton **Ajouter** en bas de l’écran ![Ajouter l’application](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)
5. Dans la boîte de dialogue Que voulez-vous faire ?, cliquez sur **Publier une application qui sera accessible hors de votre réseau**. ![Nouvelle application accessible hors de votre réseau](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

6. Suivez les instructions à l’écran pour fournir les informations suivantes sur votre application : ![Propriétés de l’application](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)

**Paramètre** | **Détails**
---|---
URL externe | Il s’agit de l’URL du service cloud utilisé pour accéder à l’application hors de votre réseau privé. L’URL est automatiquement générée en fonction du nom fourni, avec le suffixe msappproxy.net.
Méthode de pré-authentification | <p>Définissez le type de méthode de pré-authentification que l’application doit utiliser :</p><p>a. Microsoft Azure Active Directory (Microsoft Azure AD) : définit la méthode de pré-authentification Microsoft Azure Active Directory (Microsoft Azure AD). Lorsqu’un utilisateur tente d’accéder à une application, le Proxy d’application le redirige pour qu’il se connecte avec Azure AD qui authentifie alors l’utilisateur, garantissant ainsi qu’il dispose des autorisations nécessaires pour l’annuaire et l’application.</p> <p>b. Passthrough : la pré-authentification n’est pas effectuée.</p>
Protocole d’URL externe | <p>Par défaut, les applications sont publiées avec le protocole HTTPS.</p> <p>Pour activer le protocole HTTP pour une application interne, vous devez définir la pré-authentification Passthrough. Vous serez alors en mesure de modifier le protocole de l’URL externe, de HTTPS en HTTP.</p> <p>Notez que la publication d’applications avec le protocole HTTP peut créer des problèmes de sécurité pour votre application et pour vos utilisateurs.</p> <p>Vous pouvez insérer un domaine personnalisé plutôt que d’utiliser la valeur par défaut msappproxy.net. Pour plus d’informations, voir plus bas.</p>
URL interne | Il s’agit de l’URL interne utilisée par le connecteur Proxy d’application pour accéder à l’application en interne. Il doit s’agir de l’URL de l’application publiée qui est utilisée pour accéder à l’application depuis votre réseau privé. Il s’agit d’une URL valide sans aucun espace ni symbole. Vous pouvez définir un chemin d'accès spécifique sur le serveur principal à publier, tandis que le reste du serveur n'est pas publié. Cela vous permet de publier, par exemple, différents sites situés sur le même serveur SharePoint avec différents noms et règles d'accès.
Le chemin d'accès est spécifié dans le champ URL interne et sera visible dans l'URL externe. Les chemins d'accès internes et externes doivent être identiques.


Pour terminer l’Assistant, cliquez sur la coche en bas de l’écran. L’application est maintenant définie dans Azure AD.



## Affecter des utilisateurs et des groupes à l’application

1. Pour les applications qui ont été pré-authentifiées, vous devez affecter les utilisateurs et les groupes qui ont accès à l’application. <p>Pour les applications Passthrough, l’accès est possible pour tous les utilisateurs. Toutefois, pour que l’utilisateur puisse voir l’application dans sa liste d’applications, vous devez affecter l’application à cet utilisateur.
2. Après avoir terminé l’Assistant Ajouter une application, la page Démarrage rapide Proxy d’application s’affiche. Pour affecter des utilisateurs, cliquez sur **Affecter des utilisateurs**. ![Écran de démarrage rapide du Proxy d’application](./media/active-directory-application-proxy-publish/quickstart.png)
3. Sélectionnez chaque utilisateur ou groupe que vous souhaitez affecter à cette application et cliquez sur **Affecter**.

> [AZURE.NOTE]Pour les applications avec authentification Windows intégrée, vous pouvez affecter uniquement les utilisateurs et les groupes qui ont été synchronisés à partir de votre Active Directory local. Il n’est pas possible d’affecter les utilisateurs qui se connectent à l’aide d’un compte Microsoft et les invités aux applications publiées avec le Proxy d’application Azure Active Directory. Vérifiez que les utilisateurs que vous affectez se connectent avec leurs informations d’identification qui doivent faire partie du même domaine que l’application que vous publiez.

## Configuration avancée

1. Vous pouvez modifier les applications publiées ou configurer les options avancées, par exemple l’authentification unique des applications locales, à partir de la page **Configurer**.![Configuration avancée](./media/active-directory-application-proxy-publish/advancedconfig.png)

2. Sélectionnez l’application et cliquez sur **Configurer**. Les options suivantes sont disponibles :

**Paramètre** | **Détails**
---|---
Nom | Fournissez un nom descriptif pour votre application
URL externe | Il s’agit de l’URL du service cloud utilisé pour accéder à l’application hors de votre réseau privé. L’URL est générée automatiquement en fonction du nom fourni, avec le suffixe **msappproxy.net**.
Méthode de pré-authentification | Définissez le type de méthode de pré-authentification que l’application doit utiliser :<p>Active Directory : définit la méthode de pré-authentification sur Active Directory. Lorsqu’un utilisateur tente d’accéder à une application, le Proxy d’application le redirige pour qu’il se connecte avec le Proxy d’application qui authentifie alors l’utilisateur, garantissant ainsi qu’il dispose des autorisations nécessaires pour l’annuaire et l’application.</p><p> Passthrough : la pré-authentification n’est pas effectuée.</p>
URL externe | Par défaut, les applications sont publiées avec le protocole HTTPS. Pour activer le protocole HTTP pour une application interne, vous devez définir la pré-authentification Passthrough. Vous serez alors en mesure de modifier le protocole de l’URL externe, de HTTPS en HTTP. Notez que la publication d’applications avec le protocole HTTP peut créer des problèmes de sécurité pour votre application et pour vos utilisateurs. Vous pouvez insérer un domaine personnalisé plutôt que d’utiliser la valeur par défaut (msappproxy.net). Pour plus d’informations, consultez la rubrique Utilisation des domaines personnalisés.
URL interne | Il s’agit de l’URL interne utilisée par le connecteur Proxy d’application pour accéder à l’application en interne. Il doit s’agir de l’URL de l’application publiée qui est utilisée pour accéder à l’application depuis votre réseau privé. Il s’agit d’une URL valide sans aucun espace ni symbole. Vous pouvez définir un chemin d'accès spécifique sur le serveur principal à publier, tandis que le reste du serveur n'est pas publié. Cela vous permet de publier, par exemple, différents sites situés sur le même serveur SharePoint avec différents noms et règles d'accès.
Le chemin d'accès est spécifié dans le champ URL interne et sera visible dans l'URL externe. Les chemins d'accès internes et externes doivent être identiques. Traduire des URL dans les en-têtes | Pour les applications qui nécessitent que les en-têtes d’hôte HTTP ne soient pas traduits (par exemple, certaines configurations de SharePoint), définissez ce paramètre sur Non. La traduction d’en-tête est alors désactivée pour les en-têtes de demande et de réponse. Méthode d’authentification interne | Si vous utilisez le Proxy d’application pour la pré-authentification, vous pouvez définir une méthode d’authentification interne pour permettre à vos utilisateurs de profiter de l’authentification unique (SSO) pour cette application. <p> Sélectionnez l’**authentification Windows intégrée (IWA)** si votre application l’utilise. Vous pouvez configurer la délégation Kerberos contrainte (KCD) pour activer l’authentification unique pour cette application. <p> Sélectionnez **Aucune** si votre application n’utilise pas l’authentification Windows intégrée. <p> Les applications qui utilisent l’authentification Windows intégrée doivent être configurées à l’aide de la délégation Kerberos contrainte, sinon le proxy d’application ne sera pas en mesure de les publier. <p> Pour plus d’informations, consultez la rubrique Authentification unique pour les applications avec authentification Windows intégrée locales en utilisant la délégation Kerberos contrainte avec le SPN d’application interne du proxy d’application | C’est le nom de principal du service (SPN) de l’application interne, tel qu’il est configuré dans le proxy d’application local. Le SPN est utilisé par le connecteur du Proxy d’application pour extraire les jetons Kerberos pour l’application à l’aide de la délégation Kerberos KCD. <p> Pour plus d’informations, consultez [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md).

Une fois que vous avez publié des applications qui utilisent le Proxy d’application Azure Active Directory, elles apparaissent dans la liste des applications dans Azure AD et vous pouvez les gérer à partir de cet emplacement. Si vous désactivez les services de Proxy d’application après avoir publié des applications, les applications ne sont pas supprimées, mais elles ne seront plus accessibles hors de votre réseau privé. Pour afficher une application et vous assurer qu’elle est accessible, double-cliquez sur son nom. Si le service Proxy d’application est désactivé et que l’application n’est pas disponible, un message d’avertissement s’affiche en haut de l’écran. Pour supprimer une application, sélectionnez-la dans la liste, puis cliquez sur **Supprimer**.

## Voir aussi
Vous pouvez faire bien d’autres choses encore avec le Proxy d’application :

- [Activer le proxy d’application](active-directory-application-proxy-enable.md)
- [Publier des applications avec votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
- [Utiliser des applications utilisant les revendications](active-directory-application-proxy-claims-aware-apps.md)
- [Résoudre les problèmes rencontrés avec le proxy d'application](active-directory-application-proxy-troubleshoot.md)

## En savoir plus sur le Proxy d’application
- [Consultez notre aide en ligne](active-directory-application-proxy-enable.md)
- [Consultez le blog sur le Proxy d’application](http://blogs.technet.com/b/applicationproxyblog/)
- [Regardez nos vidéos sur Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Ressources supplémentaires

* [À propos de la délégation Kerberos contrainte](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=AcomDC_1125_2015-->