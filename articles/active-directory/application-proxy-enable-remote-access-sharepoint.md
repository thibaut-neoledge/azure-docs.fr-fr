---
title: "Activer l’accès distant à SharePoint avec le proxy d’application Azure AD | Microsoft Docs"
description: "Aborde les notions de base relatives à l’intégration d’un serveur SharePoint local au proxy d’application Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 93b36891c960582563a4ff9c622cd5ac3198dfeb
ms.lasthandoff: 04/17/2017


---

# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Activer l’accès distant à SharePoint avec le proxy d’application Azure AD

Cet article explique comment intégrer un serveur SharePoint local au proxy d’application Azure Active Directory (Azure AD).

Pour activer l’accès à distance à SharePoint avec le proxy d’application Azure AD, suivez les sections de cet article étape par étape.

## <a name="prerequisites"></a>Composants requis

Cet article suppose que SharePoint 2013 ou une version plus récente est déjà configuré et en cours d’exécution dans votre environnement. En outre, prenez en compte les conditions préalables suivantes :

* La fonctionnalité Proxy d’application est disponible uniquement si vous effectuez une mise à niveau vers l’édition Premium ou Standard d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

* SharePoint comprend la prise en charge native de Kerberos. Par conséquent, les utilisateurs qui accèdent à des sites internes à distance à l’aide du proxy d’application Azure AD peuvent s’attendre à une expérience d’authentification unique (SSO) sans interruption.

* Vous devrez apporter quelques modifications de configuration sur votre serveur SharePoint. Nous vous recommandons d’utiliser un environnement intermédiaire. Ainsi, vous pouvez d’abord mettre à jour votre serveur de test, puis faciliter un cycle de test avant de passer en production.

* Nous partons du principe que vous avez déjà configuré SSL pour SharePoint, car nous exigeons SSL sur l’URL publiée. SSL doit être activé sur votre site interne, afin de garantir que les liens sont envoyés/mappés correctement. Si vous n’avez pas configuré SSL, consultez le blog [Configure SSL for SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) pour obtenir des instructions. En outre, assurez-vous que l’ordinateur du connecteur approuve le certificat que vous émettez. (Ce certificat doit pas nécessairement être émis publiquement.)

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>Étape 1 : Configurer l’authentification unique auprès de SharePoint

Nos clients veulent bénéficier de la meilleure expérience SSO pour leurs applications principales, un serveur SharePoint dans le cas présent. Dans ce scénario Azure AD courant, l’utilisateur est authentifié une seule fois, car il ne sera pas invité à le faire à nouveau.

Pour les applications locales qui exigent ou utilisent l’authentification Windows, vous pouvez obtenir l’authentification unique (SSO) à l’aide du protocole d’authentification Kerberos et d’une fonctionnalité appelée « délégation Kerberos contrainte (KCD) ». Quand elle est configurée, la délégation KCD permet au connecteur de proxy d’application d’obtenir un ticket/jeton Windows pour un utilisateur, même si ce dernier ne s’est pas connecté directement à Windows. Pour en savoir plus sur la KCD, consultez [Présentation de la délégation Kerberos contrainte](https://technet.microsoft.com/library/jj553400.aspx).

Pour configurer la délégation KCD pour un serveur SharePoint, utilisez les procédures décrites dans les sections séquentielles suivantes.

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>Vérifier que SharePoint s’exécute sous un compte de service

Tout d’abord, vérifiez que SharePoint s’exécute sous un compte de service défini, et non sous le système local, le service local ou le service réseau. Vous devez effectuer cette opération afin de pouvoir attacher les noms de principal de service (SPN) à un compte valide. Les SPN sont la façon dont le protocole Kerberos identifie les différents services. De plus, vous aurez besoin de ce compte plus tard pour configurer la délégation KCD.

Pour vous assurer que vos sites sont en cours d’exécution sous un compte de service défini, procédez comme suit :

1. Ouvrez le site **Administration centrale de SharePoint 2013**.
2. Accédez à **Sécurité**, puis sélectionnez **Configurer les comptes de service**.
3. Sélectionnez **Pool d’applications web - SharePoint - 80**. Les options peuvent être légèrement différentes en fonction du nom de votre pool web, ou si le pool web utilise SSL par défaut.

  ![Choix disponibles pour configurer un compte de service](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-web-application.png)

4. Si **Sélectionnez un compte pour ce composant** a la valeur **Service local** ou **Service réseau**, vous devez créer un compte. Si ce n’est pas le cas, vous avez terminé et pouvez passer à la section suivante.
5. Choisissez **Enregistrer le nouveau compte géré**. Une fois votre compte créé, vous devez définir l’option **Pool d’applications web** pour pouvoir utiliser le compte.

> [!NOTE]
Vous devez disposer d’un compte Azure AD précédemment créé pour le service. Nous vous suggérons d’autoriser une modification de mot de passe automatique. Pour plus d’informations sur l’ensemble des étapes et la résolution des problèmes, consultez [Configurer la modification automatique de mot de passe dans SharePoint 2013](https://technet.microsoft.com/library/ff724280.aspx).

### <a name="configure-sharepoint-for-kerberos"></a>Configurer SharePoint pour Kerberos

Vous utilisez la délégation KCD pour effectuer l’authentification unique auprès du serveur SharePoint, et cela fonctionne uniquement avec le protocole Kerberos.

Pour configurer votre site SharePoint pour l’authentification Kerberos :

1. Ouvrez le site **Administration centrale de SharePoint 2013**.
2. Accédez à **Gestion des applications**, sélectionnez **Gérer les applications web**, puis sélectionnez votre site SharePoint. Dans cet exemple, il s’agit de **SharePoint - 80**.

  ![Sélection du site SharePoint](./media/application-proxy-remote-sharepoint/remote-sharepoint-manage-web-applications.png)

3. Cliquez sur **Fournisseurs d’authentification** dans la barre d’outils.
4. Dans la zone **Fournisseurs d’authentification**, cliquez sur **Zone par défaut** pour afficher les paramètres.
5. Dans la boîte de dialogue **Modifier l’authentification**, faites défiler l’affichage vers le bas jusqu’à ce que vous voyiez **Types d’authentification basée sur les revendications**, puis vérifiez que les deux options **Activer l’authentification Windows** et **Authentification Windows intégrée** sont sélectionnées.
6. Dans la zone de liste déroulante, vérifiez que l’option **Négocier (Kerberos)** est sélectionnée.

  ![Boîte de dialogue Modifier l’authentification](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-edit-authentication.png)

7. En bas de la boîte de dialogue **Modifier l’authentification**, cliquez sur **Enregistrer**.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Définir un nom de principal de service pour le compte de service SharePoint

Avant de configurer la délégation KCD, vous devez identifier le service SharePoint en cours d’exécution comme compte de service que vous avez configuré. Pour cela, définissez un nom de principal de service. Pour plus d'informations, consultez la page [Noms de principal de service](https://technet.microsoft.com/library/cc961723.aspx).

Le format du nom de principal de service est :

```
<service class>/<host>:<port>
```

Dans le format du nom de principal de service :

* _service class_ est un nom unique pour le service. Pour SharePoint, vous utilisez **HTTP**.

* _host_ est le nom de domaine complet ou le nom NetBIOS de l’hôte sur lequel le service est en cours d’exécution. Dans le cas d’un site SharePoint, ce nom peut devoir être l’URL du site, en fonction de la version d’IIS que vous utilisez.

* _port_ est facultatif.

Si le nom de domaine complet du serveur SharePoint est :

```
sharepoint.demo.o365identity.us
```

Le nom de principal de service est alors :

```
HTTP/ sharepoint.demo.o365identity.us demo
```

Vous devrez peut-être également définir des noms de principal de service pour des sites spécifiques sur votre serveur. Pour plus d’informations, consultez [Configurer l’authentification Kerberos](https://technet.microsoft.com/library/cc263449(v=office.12).aspx). Prêtez une attention particulière à la section « Créer des noms de principal de service pour vos applications web à l’aide de l’authentification Kerberos ».

Le moyen le plus simple pour définir des noms de principal de service (SPN) consiste à suivre les formats de SPN qui peuvent déjà être présents pour votre site. Copiez ces SPN pour les inscrire dans le compte de service. Pour ce faire :

1. Accédez au site avec le SPN à partir d’un autre ordinateur.
 L’ensemble des tickets Kerberos pertinents sont alors mis en cache sur l’ordinateur. Ces tickets contiennent le SPN du site cible auquel vous avez accédé.

2. Vous pouvons extraire le SPN pour ce site à l’aide d’un outil appelé [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html). Dans une fenêtre de commande en cours d’exécution dans le même contexte que l’utilisateur qui a accédé au site dans le navigateur, exécutez la commande suivante :
```
Klist
```
Klist renvoie alors l’ensemble des SPN cibles. Dans cet exemple, la valeur en surbrillance est le SPN nécessaire :

  ![Exemples de résultats de l’outil Klist](./media/application-proxy-remote-sharepoint/remote-sharepoint-target-service.png)

4. Maintenant que vous avez le SPN, vous devez vérifier qu’il est correctement configuré sur le compte de service que vous avez précédemment défini pour l’application web. Exécutez la commande suivante à partir de l’invite de commandes en tant qu’administrateur du domaine :

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 Cette commande définit le SPN pour le compte de service SharePoint en cours d’exécution en tant que _demo\sp_svc_.

 Remplacez _http/sharepoint.demo.o365identity.us_ par le nom de principal de service pour votre serveur et _demo\sp_svc_ par le compte de service dans votre environnement. La commande Setspn recherche le SPN avant de l’ajouter. Dans le cas présent, l’erreur **Valeur de SPN dupliquée** peut s’afficher. Si vous voyez cette erreur, assurez-vous que la valeur est associée au compte de service.

Vous pouvez vérifier que le SPN a été ajouté en exécutant la commande Setspn avec l’option -l. Pour en savoir plus sur cette commande, consultez [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>Vérifier que le connecteur est défini comme délégué approuvé sur SharePoint

Configurez la délégation KCD afin que le service de proxy d’application Azure AD puisse déléguer des identités d’utilisateur au service SharePoint. Pour cela, vous activez le connecteur de proxy d’application pour récupérer les tickets Kerberos pour les utilisateurs qui ont été authentifiés dans Azure AD. Puis ce serveur transmet le contexte à l’application cible, ou SharePoint dans ce cas.

Pour configurer la délégation KCD, répétez les étapes suivantes pour chaque ordinateur connecteur :

1. Connectez-vous en tant qu’administrateur de domaine à un centre de données, puis ouvrez **Utilisateurs et ordinateurs Active Directory**.
2. Trouvez l’ordinateur sur lequel le connecteur est en cours d’exécution. Dans cet exemple, il s’agit du même ordinateur que le serveur SharePoint.
3. Double-cliquez sur l’ordinateur, puis cliquez sur l’onglet **Délégation**.
4. Vérifiez que les paramètres des délégation sont définis sur **N’approuver cet ordinateur que pour la délégation aux services spécifiés**, puis sélectionnez **Utiliser tout protocole d’authentification**.

  ![Paramètres de délégation](./media/application-proxy-remote-sharepoint/remote-sharepoint-delegation-box.png)

5. Cliquez sur le bouton **Ajouter**, cliquez sur **Utilisateurs et ordinateurs**, puis recherchez le compte de service.

  ![Ajout du SPN pour le compte de service](./media/application-proxy-remote-sharepoint/remote-sharepoint-users-computers.png)

6. Dans la liste des noms de principal de service, sélectionnez celui que vous avez créé précédemment pour le compte de service.
7. Cliquez sur **OK**. Cliquez à nouveau sur **OK** pour enregistrer les modifications apportées.

## <a name="step-2-enable-remote-access-to-sharepoint"></a>Étape 2 : Activer l’accès à distance à SharePoint

Maintenant que vous avez activé SharePoint pour Kerberos et configuré la délégation KCD, vous pouvez configurer l’authentification unique auprès de SharePoint. Ensuite, à partir du connecteur, vous pouvez publier la batterie de serveurs SharePoint pour l’accès à distance à l’aide du proxy d’application Azure AD.

Pour effectuer les étapes suivantes, vous devez être membre du rôle Administrateur global dans un compte Azure Active Directory de votre organisation.

1. Connectez-vous au [portail Azure](https://manage.windowsazure.com), puis recherchez votre client Azure AD.
2. Cliquez sur **Applications**, puis sur **Ajouter**.
3. Sélectionnez **Publier une application qui sera accessible depuis l’extérieur de votre réseau**. Si vous ne voyez pas cette option, assurez-vous qu’un compte Azure AD De base ou Premium est configuré dans le client.
4. Renseignez chacune des options comme suit :
 * **Nom** : utilisez la valeur de votre choix, par exemple **SharePoint**.
 * **URL interne** : il s’agit de l’URL du site SharePoint en interne, par exemple **https://SharePoint/**. Dans cet exemple, veillez à utiliser le protocole **https**.
 * **Méthode de préauthentification** : sélectionnez **Azure Active Directory**.

  ![Options d’ajout d’une application](./media/application-proxy-remote-sharepoint/remote-sharepoint-add-application.png)

5. Une fois l’application publiée, cliquez sur l’onglet **Configurer**.
6. Faites défiler jusqu'à l’option **Traduire l'URL dans les en-têtes**. La valeur par défaut est **OUI**. Remplacez-la par **NON**.

 SharePoint utilise la valeur _En-tête d’hôte_ pour rechercher le site. Il génère également des liens en fonction de cette valeur. L’effet immédiat est de vérifier que tout lien généré par SharePoint est une URL publiée correctement définie pour utiliser l’URL externe. Définir la valeur sur **OUI** permet également au connecteur de transférer la demande à l’application principale. Toutefois, la valeur **NON** signifie que le connecteur n’envoie pas le nom d’hôte interne. Au lieu de cela, il envoie l’en-tête d’hôte comme URL publiée à l’application principale.

 De plus, pour garantir que SharePoint accepte cette URL, vous devez effectuer des configurations supplémentaires sur le serveur SharePoint. Vous le ferez dans la section suivante.

7. Définissez **Méthode d’authentification interne** sur **Authentification Windows intégrée**. Si votre client Azure AD utilise un nom d’utilisateur principal (UPN) dans le cloud différent de l’UPN local, n’oubliez pas de mettre également à jour **Identité de connexion déléguée**.
8. Définissez l’option **SPN d’application interne** sur la valeur que vous avez définie précédemment. Par exemple, utilisez **http/sharepoint.demo.o365identity.us**.
9. Affectez l’application à vos utilisateurs cibles.

Votre application doit ressembler à l’écran suivant :

  ![Application achevée](./media/application-proxy-remote-sharepoint/remote-sharepoint-internal-application-spn.png)

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>Étape 3 : Garantir que SharePoint connaît l’URL externe

La dernière étape à effectuer consiste à garantir que SharePoint peut trouver le site basé sur l’URL externe, afin qu’il génère des liens basés sur cette URL externe. Pour cela, configurez des mappages des accès de substitution pour le site SharePoint.

1. Ouvrez le site **Administration centrale de SharePoint 2013**.
2. Sous **Paramètres système**, sélectionnez **Configurer des mappages d’accès alternatifs**.

 Cela ouvre la zone **Mappages d’accès alternatif**.

  ![Zone Mappages des accès de substitution](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access1.png)

3. Dans la liste déroulante à côté de **Collection de mappages des accès de substitution**, sélectionnez **Modifier la collection de mappages des accès de substitution**.
4. Sélectionnez votre site, par exemple **SharePoint - 80**.

  ![Sélection d’un site](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access2.png)

5. Vous pouvez choisir d’ajouter l’URL publiée comme URL interne ou URL publique. Cet exemple utilise une URL publique comme extranet.
6. Cliquez sur **Modifier les URL publiques** dans le chemin **Extranet**, puis entrez le chemin de l’application publiée, comme dans l’étape précédente. Par exemple, entrez **https://sharepoint-iddemo.msappproxy.net**.

  ![Saisie du chemin](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access3.png)

7. Cliquez sur **Save**.

 Vous pouvez maintenant accéder au site SharePoint en externe par l’intermédiaire du proxy d’application Azure AD.

## <a name="next-steps"></a>Étapes suivantes

- [Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md)
- [Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md)
- [Publication de SharePoint 2016 et d’Office Online Server avec le proxy d’application Azure AD](https://blogs.technet.microsoft.com/dawiese/2016/06/09/publishing-sharepoint-2016-and-office-online-server-with-azure-ad-application-proxy/)

