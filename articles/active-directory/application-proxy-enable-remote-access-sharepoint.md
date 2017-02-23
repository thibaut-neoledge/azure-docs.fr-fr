---
title: "Activer l’accès distant pour SharePoint avec le proxy d’application Azure AD | Microsoft Docs"
description: "Couvre les bases sur les connecteurs de proxy d’application Azure AD."
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
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 8436238546515b66b58b31673d54586e4a20f50f
ms.openlocfilehash: 86f10c04368d1c382939b418c6909ca807b3bf5a


---

# <a name="enable-remote-access-to-sharepoint-with-azure-ad-app-proxy"></a>Activer l’accès distant pour SharePoint avec le proxy d’application Azure AD

Cet article explique comment intégrer un serveur SharePoint local avec le proxy d’application Azure AD.

> [!NOTE]
> Le proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou De base d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).
> 

##<a name="prerequisites"></a>Composants requis

Cet article suppose que vous avez déjà installé SharePoint 2013 ou une version plus récente et que le programme est en cours d’exécution dans votre environnement. En outre, prenez en compte les conditions préalables suivantes :

* SharePoint comprend la prise en charge native de Kerberos. Par conséquent, les utilisateurs qui accèdent à des sites internes à distance via le proxy d’application Azure AD peuvent s’attendre à une expérience d’authentification unique fluide.

* Vous devrez apporter quelques modifications de configuration sur votre serveur SharePoint. Nous vous recommandons d’utiliser un environnement intermédiaire. De cette façon vous pouvez d’abord mettre à jour votre serveur de test, puis faciliter un cycle de test avant de passer en production.

* Nous partons du principe que vous avez déjà configuré SSL pour SharePoint, car nous exigeons SSL sur l’URL publiée. Vous devrez avoir activé SSL sur votre site interne, pour vous assurer que les liens sont envoyés/mappés correctement. Si vous n’avez pas configuré SSL, consultez [Configuration de SSL pour SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013), qui contient des instructions pour la configuration de SSL. En outre, assurez-vous que l’ordinateur du connecteur approuve le certificat que vous émettez. (Cela ne doit pas nécessairement être un certificat émis publiquement.)

##<a name="steps-to-set-up-sharepoint"></a>Procédure de configuration de SharePoint

Procédez comme suit pour activer l’accès à distance pour SharePoint avec le proxy d’application Azure AD :

**Partie 1 : Configuration de l’authentification unique (SSO)**

  * Étape A. Vérifiez que le serveur SharePoint s’exécute en tant que compte de service.
  * Étape B. Configurez SharePoint pour Kerberos.
  * Étape C. Définissez un nom de principal de service (SPN) pour le compte qui est affecté à SharePoint.
  * Étape D. Vérifiez que le connecteur est défini comme délégué approuvé sur SharePoint.

**Partie 2 : Activer l’accès à distance sécurisé**

 * Publiez la batterie de serveurs SharePoint sur le proxy d’application Azure AD.

**Partie 3 : S’assurer que SharePoint connaît l’URL externe**

 * Définissez les mappages d’accès alternatifs dans SharePoint.

### <a name="part-1-set-up-single-sign-on-sso-to-sharepoint"></a>Partie 1 : Configurer l’authentification unique (SSO) dans SharePoint

Nos clients veulent bénéficier de la meilleure expérience SSO dans leurs applications principales, un serveur SharePoint dans notre cas. Dans ce scénario Azure AD courant, l’utilisateur ne s’authentifie qu’une seule fois, car il n’est pas invité à le faire à nouveau.

Pour les applications locales ou utilisant l’authentification Windows, cela peut être réalisé à l’aide du protocole d’authentification Kerberos et d’une fonctionnalité appelée la délégation Kerberos contrainte (KCD). La KCD, lorsqu’elle est configurée, permet au connecteur du proxy d’application d’obtenir un ticket/jeton Windows pour un utilisateur donné, même si l’utilisateur n’est pas directement connecté à Windows. Pour en savoir plus sur la KCD, consultez [Présentation de la délégation Kerberos contrainte](https://technet.microsoft.com/en-us/library/jj553400.aspx).

Suivez les étapes ci-dessous pour installer cette fonctionnalité sur un serveur SharePoint.

**Étape A : Vérifiez que SharePoint s’exécute sous un compte de service, et non le système local, le service local ou le service réseau**

La première chose à faire est de s’assurer que SharePoint s’exécute sous un compte de service défini. Vous devez faire cela afin de pouvoir rattacher les noms de principal de service (SPN) à un compte valide. Les SPN sont la façon dont le protocole Kerberos identifie les différents services. Et vous en aurez besoin plus tard pour configurer la KCD.

Pour vous assurer que vos sites sont en cours d’exécution sous un compte de service défini, procédez comme suit :

1. Ouvrez le site **Administration centrale de SharePoint 2013**.
2. Accédez à **Sécurité** et choisissez **Configurer des comptes de service**.
3. Sélectionnez **Pool d’applications web – SharePoint – 80**. Les options peuvent être légèrement différentes en fonction du nom de votre pool web, ou selon qu’il utilise ou non SSL par défaut.

  ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-web-application.png)

4. Si **Sélectionner un compte pour ce composant** a la valeur **Service local** ou **Service réseau**, vous devez créer un compte. Si ce n’est pas le cas, vous avez terminé et pouvez passer à l’étape suivante. 
5. Choisissez **Inscrire le nouveau compte géré**. Une fois votre compte créé, vous devez définir le **Pool d’applications web** avant de pouvoir utiliser le compte.

> [!NOTE]
Vous devez disposer d’un compte AD créé au préalable pour le service. Nous vous suggérons d’autoriser une modification de mot de passe automatique. Pour plus d’informations sur l’ensemble des étapes et la résolution des problèmes, consultez [Configurer le changement automatique de mot de passe dans SharePoint 2013](https://technet.microsoft.com/EN-US/library/ff724280.aspx). 

**Étape B : Configurer SharePoint pour Kerberos**

Nous utilisons la KCD pour effectuer l’authentification unique (SSO) sur le serveur SharePoint, et cela fonctionne uniquement avec le protocole Kerberos. 

Pour configurer votre site SharePoint pour l’authentification Kerberos :

1. Ouvrez le site **Administration centrale de SharePoint 2013**.
2. Accédez à **Gestion des applications**, choisissez **Gérer les applications web** et sélectionnez votre site SharePoint. Dans cet exemple, il s’agit de **SharePoint - 80**.

  ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-manage-web-applications.png)
  
3. Cliquez sur **Fournisseurs d’authentification** dans la barre d’outils.
4. Dans la zone **Fournisseurs d’authentification**, cliquez sur **Zone par défaut** pour afficher les paramètres.
5. Dans la zone **Modifier l’authentification**, faites défiler jusqu'à ce que vous voyiez **Types d’authentification par revendication**, et assurez-vous que les deux options **Activer l’authentification Windows** et **Authentification Windows intégrée** sont activées. 
6. Dans la zone de liste déroulante, assurez-vous que **Négocier (Kerberos)** est sélectionné.

  ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-edit-authentication.png)

7. Tout en bas de la zone **Modifier l’authentification**, cliquez sur **Enregistrer**.

**Étape C : Définir un SPN pour le compte de service SharePoint**

Avant de configurer la KCD, nous devons identifier le service SharePoint en cours d’exécution comme le compte de service que vous avez configuré ci-dessus. Nous faisons cela en définissant un nom de principal de service (SPN). Pour plus d'informations, consultez la page [Noms de principal de service](https://technet.microsoft.com/en-us/library/cc961723.aspx).

Le format du nom de principal de service est :

```
<service class>/<host>:<port>
```

_service class_ est un nom unique pour le service. Pour SharePoint, nous utilisons HTTP.

_host_ est le nom de domaine complet ou nom Netbios de l’hôte sur lequel le service est en cours d’exécution. Dans le cas d’un site SharePoint, cela peut-être l’URL du site, en fonction de la version d’IIS que vous utilisez.

_port_ est facultatif. Si le nom de domaine complet du serveur SharePoint est :

```
sharepoint.demo.o365identity.us
```

Le SPN serait : 

```
HTTP/ sharepoint.demo.o365identity.us demo
```

En outre, vous devez également définir des SPN pour des sites spécifiques sur votre serveur. Pour plus d’informations, consultez [Configurer l’authentification Kerberos](https://technet.microsoft.com/en-us/library/cc263449(v=office.12).aspx). Veillez à accorder une attention particulière à la section « Créer des noms de principal de service pour vos applications web à l’aide de l’authentification Kerberos ». 

Le moyen le plus simple pour cela consiste à suivre les formats de SPN qui peuvent déjà être présents pour votre site. Copiez ces SPN pour les inscrire dans le compte de service. Pour ce faire :

1. Accédez au site avec le SPN à partir d’un autre ordinateur. 
 Lorsque vous le faites, l’ensemble des tickets Kerberos pertinents sont mis en cache sur l’ordinateur. Ces tickets contiennent le SPN du site cible auquel vous avez accédé. Nous pouvons extraire le SPN pour ce site à l’aide d’un outil appelé [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html).
 
2. Dans une fenêtre de commande en cours d’exécution dans le même contexte que l’utilisateur qui a accédé au site dans le navigateur, exécutez la commande suivante : 
```
Klist
```
3. Elle renvoie l’ensemble des SPN du service cible. Dans cet exemple, la valeur en surbrillance est le SPN nécessaire :

  ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-target-service.png)
  
4. Maintenant que vous avez le SPN, vous devez vous assurer qu’il est correctement configuré sur le compte de service défini précédemment pour l’application web. Suivez la procédure décrite ci-après.

**Définir le SPN**

Pour définir le SPN, exécutez la commande ci-dessous à partir de l’invite de commandes en tant qu’administrateur du domaine.

```
setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
```

Cette commande définit le SPN pour le compte de service SharePoint en cours d’exécution en tant que _demo\sp_svc_.

N’oubliez pas de remplacer _http/sharepoint.demo.o365identity.us_ par le nom du principal de service pour votre serveur et _demo\sp_svc_ avec le compte de service dans votre environnement. La commande setspn recherchera le SPN avant de l’ajouter. Dans ce cas, vous pouvez voir une erreur **Valeur de SPN dupliquée**. Si vous voyez cette erreur, assurez-vous que la valeur est associée au compte de service.

Vous pouvez vérifier que le SPN a été ajouté en exécutant la commande Setspn avec l’option -l. Pour en savoir plus sur l’outil Setspn, consultez [Setspn](https://technet.microsoft.com/en-us/library/cc731241.aspx).

**Étape D : Vérifier que les connecteurs sont approuvés pour déléguer à SharePoint**

Lors de cette étape, vous allez configurer la délégation KCD afin que le service de proxy d’application Azure AD soit capable de déléguer des identités d’utilisateur pour le service SharePoint. Pour cela, vous activez le connecteur de proxy d’application pour être en mesure de récupérer les tickets Kerberos pour les utilisateurs qui ont été authentifiés dans Azure AD. Puis ce serveur transmet le contexte à l’application cible, ou SharePoint dans ce cas. 

Pour configurer la délégation KCD, vous devrez répéter les étapes suivantes pour chaque machine de connecteur :

1. Connectez-vous en tant qu’administrateur à un centre de données et ouvrez de domaine **Active Directory Users and Computers**.
2. Trouvez l’ordinateur sur lequel le connecteur est en cours d’exécution. Dans cet exemple, il s’agit du même ordinateur que pour le serveur SharePoint.
3. Double-cliquez sur l’ordinateur, puis sur l’onglet **Délégation**.
4. Assurez-vous que les paramètres des délégations sont définis sur **Approuver cet ordinateur uniquement pour la délégation aux services spécifiés**, puis sélectionnez **Utiliser n’importe quel protocole d’authentification**.

  ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-delegation-box.png)
  
5. Vous devez maintenant ajouter le SPN que vous avez créé précédemment pour le compte de service. Cliquez sur le bouton **Ajouter**, puis sur **Utilisateurs ou ordinateurs**, puis recherchez le compte que vous avez créé précédemment.

  ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-users-computers.png)

 Vous devriez voir une liste de SPN parmi lesquels choisir. Vous devez ajouter celui que vous avez précédemment défini. 
6. Sélectionnez cet élément, puis cliquez sur **OK**. Cliquez à nouveau sur **OK** pour enregistrer les modifications.

### <a name="part-2-enable-remote-access-to-sharepoint"></a>Partie 2 : Activer l’accès distant pour SharePoint

Maintenant que vous avez activé SharePoint pour Kerberos et configuré la délégation KCD, vous pouvez configurer l’authentification unique (SSO) pour SharePoint. Puis, à partir du connecteur, vous pouvez publier SharePoint pour l’accès à distance via le proxy d’application Azure AD.

**Publication de SharePoint avec le proxy d’application Azure AD**

Pour effectuer les étapes ci-dessous, vous devez être un membre du rôle Administrateur global dans un compte Azure Active Directory de votre organisation.

1. Connectez-vous au portail de gestion Azure (https://manage.windowsazure.com) et trouvez votre client Azure AD.
2. Cliquez sur **Applications**, puis sur **Ajouter**.
3. Sélectionnez **Publier une application qui sera accessible depuis l’extérieur de votre réseau**. Si vous ne voyez pas cette option, assurez-vous qu’un compte Azure AD De base ou Premium est configuré dans le client.
4. Dans la zone résultante, remplissez chacune des options comme suit : 
 * **Nom** : la valeur de votre choix, par exemple _SharePoint_.
 * **URL interne** : l’URL du site SharePoint en interne, par exemple https://SharePoint/. Dans cet exemple, veillez à utiliser le protocole https.
 * **Méthode de pré-authentification** : sélectionnez _Azure Active Directory_.

  ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-add-application.png)

5. Une fois l’application publiée, cliquez sur l’onglet **Configurer**.
6. Faites défiler jusqu'à l’option **Traduire l'URL dans les en-têtes**. La valeur par défaut est _OUI_, remplacez-la par _NON_. 

 SharePoint utilise la valeur _En-tête d’hôte_ pour rechercher le site, et génère également des liens en fonction de cette valeur. L’effet est que, ce faisant, tout lien que SharePoint génère est une URL publiée correctement définie pour utiliser l’URL externe. Définir la valeur sur _OUI_ permet également au connecteur de transférer la demande à l’application principale. Toutefois, la valeur _NON_ signifie que le connecteur n’envoie pas le nom d’hôte interne, et envoie à la place de l’en-tête d’hôte en tant qu’URL publiée à l’application principale.

 En outre, pour vous assurer que SharePoint accepte cette URL, vous devez effectuer une configuration supplémentaire sur le serveur SharePoint. Vous effectuerez cela dans la section suivante.

7. Modifiez la **Méthode d’authentification interne** sur _Intégrée à Windows_. Si votre client Azure AD utilise un nom UPN dans le cloud différent du nom local, n’oubliez pas d’également mettre à jour **Identité de connexion déléguée**.
8. Définissez le **SPN d’application interne** sur la valeur que nous avons définie ci-dessus. Par exemple, _http/sharepoint.demo.o365identity.us_.
9. Vous pouvez désormais affecter l’application à vos utilisateurs cibles.

Votre application doit ressembler à l’écran suivant :

  ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-internal-application-spn.png)

###<a name="part-3-ensure-sharepoint-knows-about-the-external-url"></a>Partie 3 : Vérifier que SharePoint connaît l’URL externe

La dernière étape que vous devez effectuer consiste à vous assurer que SharePoint peut trouver le site à partir de l’URL externe, afin de pouvoir générer des liens basés sur cette URL externe. Pour cela, configurez des mappages d’accès alternatif pour SharePoint.

**Configurer un nom alternatif pour le site SharePoint**

1. Ouvrez le site **Administration centrale de SharePoint 2013**.
2. Sous **Paramètres système**, sélectionnez **Configurer des mappages d’accès alternatifs**. 

 Cela ouvre la zone **Mappages d’accès alternatif**.

  ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access1.png)

3. Dans la liste déroulante à côté de **Collections de mappages d’accès alternatif**, sélectionnez **Modifier collection de mappages d’accès alternatif**.
4. Sélectionnez votre site, par exemple **SharePoint – 80**.

  ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access2.png)

5. Vous pouvez choisir d’ajouter l’URL publiée comme une URL interne ou une URL publique. Cet exemple utilise une **URL publique** pour l’extranet.
6. Cliquez sur **Modifier les URL publiques** dans le chemin d’accès **Extranet**, puis entrez le chemin d’accès auquel vous publiez l’application, comme dans l’étape précédente. Par exemple, _https://sharepoint-iddemo.msappproxy.net_.

  ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access3.png)

7. Cliquez sur **Save**. 

 Vous pouvez désormais accéder au site SharePoint en externe via le proxy d’application Azure AD.

##<a name="next-steps"></a>Étapes suivantes

[Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md)<br>
[Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md)<br>
[Publication de SharePoint 2016 et du serveur Office en ligne avec le proxy d’application Azure AD](https://blogs.technet.microsoft.com/dawiese/2016/06/09/publishing-sharepoint-2016-and-office-online-server-with-azure-ad-application-proxy/)









<!--HONumber=Feb17_HO1-->


