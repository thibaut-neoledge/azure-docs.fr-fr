---
title: "Configurations et stratégies de sécurité recommandées | Microsoft Docs"
description: "Décrit les recommandations et les concepts de base de Microsoft pour le déploiement de stratégies et de configurations liées aux e-mails, aux documents et aux applications."
author: jeffgilb
manager: femila
editor: jsnow
ms.service: guidance
ms.topic: article
ms.date: 07/12/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
pnp.series.title: Best Practices
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 3c8e636921ab269a5314f74789c8144602685412
ms.contentlocale: fr-fr
ms.lasthandoff: 07/15/2017

---

# <a name="recommended-security-policies-and-configurations"></a>Configurations et stratégies de sécurité recommandées
 
## <a name="introduction"></a>Introduction

Les recommandations fournies décrivent les directives générales de Microsoft pour l’application de stratégies et de configurations de sécurité dans toute l’entreprise. Ces recommandations vous assurent que vos employés sont en sécurité et productifs. 

### <a name="intended-audience"></a>Public concerné

Le public concerné par ces recommandations se compose d’architectes d’infrastructure d’entreprise et de professionnels de l’informatique qui connaissent déjà les produits [Exchange Online](https://technet.microsoft.com/library/jj200580.aspx) (Office 365) et [Microsoft Enterprise Mobility + Security](http://microsoft.com/ems). Ces produits incluent, sans s’y limiter, Azure Active Directory (identité), Microsoft Intune (gestion des appareils) et Azure Information Protection (protection des données). 

### <a name="customer-environment"></a>Environnement client

Les stratégies recommandées sont applicables aux entreprises fonctionnant entièrement dans le cloud Microsoft et pour les clients dont l’infrastructure est déployée en local et sur le cloud Microsoft. 

### <a name="assumptions"></a>Hypothèses

La plupart des recommandations fournies reposent sur des services disponibles uniquement avec les abonnements E5 Enterprise Mobility + Security (EMS). Les recommandations présentées supposent des fonctionnalités d’abonnement E5 EMS complètes. 

### <a name="caveats"></a>Mises en garde

Votre organisation peut être soumise à des exigences réglementaires ou autres exigences de conformité, y compris des recommandations spécifiques qui peuvent vous amener à appliquer des stratégies divergeant de ces configurations recommandées.  Ces configurations recommandent des contrôles d’utilisation qui n’étaient pas disponibles jusqu’ici.  Nous vous recommandons ces contrôles, car nous pensons qu’ils représentent un parfait équilibre entre sécurité et productivité.  

Nous avons fait tout notre possible pour prendre en compte un large éventail d’exigences de protection de l’organisation. Toutefois, nous ne pouvons pas envisager toutes les exigences possibles ni tous les aspects uniques de votre organisation. Utilisez ce document comme un guide sur la façon dont Microsoft et l’équipe Secure Productive Enterprise envisagent une application correcte de la stratégie dans votre organisation. 

>[!NOTE]
>Pour obtenir une vue d’ensemble des concepts de base permettant de bien comprendre les fonctionnalités de protection décrites dans ces recommandations, consultez [Vue d’ensemble des services EMS et Office 365](secure-email-ems-office365-service-descriptions.md).
>

## <a name="core-concepts"></a>Principaux concepts

Les mesures de sécurité importent peu lorsque les utilisateurs, affectés par des frictions inutiles pendant leurs tâches, contournent les stratégies de sécurité de votre organisation. L’authentification unique Azure AD (SSO) tente de réduire la charge qui pèse sur les utilisateurs. Ainsi, les utilisateurs peuvent rester productifs tout en se conformant aux stratégies de contrôle d’accès de l’organisation. 

### <a name="single-sign-on-authentication"></a>Authentification unique

Le diagramme suivant illustre un flux d’authentification unique classique :

![Expérience d’authentification unique de l’utilisateur](./media/secure-email/typical-authentication-flow.png)

Pour commencer l’authentification, le client envoie des informations d’identification (par exemple, nom d’utilisateur et mot de passe) et/ou les artefacts d’authentification unique déjà obtenus pour Azure AD. Un artefact d’authentification unique peut être un jeton de session pour un navigateur ou un jeton d’actualisation pour des applications complexes. 

Azure AD vérifie les informations d’identification et/ou l’artefact d’authentification unique, et il évalue toutes les stratégies applicables. Ensuite, il émet un jeton d’accès pour le fournisseur de ressources (Exchange Online dans le diagramme). Azure AD émet également un artefact d’authentification unique afin que le client bénéficie d’une authentification unique dans ses requêtes ultérieures. 

Le client stocke l’artefact d’authentification unique et envoie le jeton d’accès comme une preuve de son identité au fournisseur de ressources. Une fois qu’Exchange Online a vérifié le jeton d’accès et effectué les contrôles nécessaires, il accorde l’accès client aux e-mails.

#### <a name="single-sign-on-sso-refresh-tokens"></a>Jetons d’actualisation d’authentification unique

L’authentification unique peut être obtenue de différentes manières. Par exemple, des cookies provenant d’un fournisseur d’identité peuvent servir d’artefact d’authentification unique afin de stocker l’état de connexion d’un utilisateur dans un navigateur. Il est ainsi possible de se connecter par la suite aux applications en mode silencieux (sans invite à saisir les informations d’identification) au travers du même fournisseur d’identité. 

Lorsqu’un utilisateur s’authentifie auprès d’Azure AD, une session d’authentification unique est établie avec le navigateur de l’utilisateur et Azure AD. Le jeton SSO représente cette session sous la forme d’un cookie. Azure AD utilise deux types de jeton de session SSO : persistant et non persistant. Les jetons de session persistants sont stockés sous forme de cookies persistants par les navigateurs lorsque la case Maintenir la connexion est cochée au moment de la connexion. Les jetons de session non persistants sont stockés sous forme de cookies de session et ils sont détruits lors de la fermeture du navigateur. 

Pour les applications fiables capables d’utiliser des protocoles d’authentification modernes, comme [OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html) et [OAuth 2.0](https://tools.ietf.org/html/rfc6749), l’authentification unique est activée à l’aide de jetons d’actualisation utilisés sous forme d’artefacts d’authentification unique (en plus de des cookies d’authentification unique précédemment décrits). Les jetons d’actualisation sont présentés à un serveur d’autorisation lorsqu’une application demande un nouveau jeton d’accès. 

Le jeton d’actualisation contient les revendications et les attributs sur le type de méthode d’authentification utilisé lors de l’authentification des utilisateurs. Par exemple, si un utilisateur a été authentifié à l’aide de plusieurs méthodes (nom d’utilisateur/mot de passe et téléphone), une revendication d’authentification multifacteur (MFA) est présente dans le jeton d’actualisation. En outre, des revendications supplémentaires peuvent contenir des données telles que la durée de validité de l’authentification multifacteur. 

Les jetons d’actualisation permettent au client d’obtenir un nouveau jeton d’accès, sans nécessiter de nouvelle authentification interactive. Les jetons d’actualisation ont une durée de vie beaucoup plus longue que les jetons d’accès et ils peuvent être utilisés pour obtenir un nouvel accès et ainsi qu’un jeton d’actualisation. Les nouveaux jetons d’actualisation obtenus peuvent ensuite être utilisés en continu pour récupérer un autre ensemble de jetons d’accès et d’actualisation. 

Le client poursuit ce processus d’authentification unique jusqu’à ce que le paramètre de délai d’inactivité maximal du jeton d’actualisation expire, l’âge maximal du jeton d’actualisation expire, ou les critères de stratégie d’authentification et d’autorisation changent. Cette modification se produit au moment de l’émission du jeton d’actualisation d’origine. Les modifications significatives apportées à un attribut utilisateur, par exemple une réinitialisation de mot de passe, requièrent également la création d’un jeton d’authentification. Le client doit procéder à une nouvelle authentification interactive pour continuer. Cela désigne essentiellement une interruption du processus d’authentification unique que le client n’a pas encore rencontrée. 

#### <a name="conditional-access"></a>Accès conditionnel

Azure AD, en tant que serveur d’autorisation pour vos applications, détermine s’il faut émettre des jetons d’accès à partir d’une évaluation de toutes les stratégies d’accès conditionnel appliquées à la ressource à laquelle vous tentez d’accéder. Si toutes les exigences de la stratégie sont satisfaites, un jeton d’accès et un jeton d’actualisation mis à jour sont émis. Dans le cas contraire, l’utilisateur reçoit des instructions permettant de satisfaire cette stratégie et/ou doit effectuer des étapes supplémentaires, y compris procéder à l’authentification multifacteur (MFA).  Une fois que l’authentification multifacteur a été effectuée, sa revendication est ajoutée au jeton d’actualisation qui en résulte.  

Les revendications du jeton d’actualisation sont cumulées au fil du temps. Certaines revendications présentent des délais d’expiration après lesquels elles ne sont plus prises en compte lors des vérifications d’autorisation. Cela peut parfois provoquer des résultats inattendus. Par exemple, si une stratégie d’accès conditionnel est configurée de sorte que l’authentification multifacteur est requise pour les tentatives d’authentification provenant d’emplacements extranet. Dans ce cas, l’utilisateur ne reçoit parfois pas l’invite MFA attendue lorsqu’il accède à une ressource de l’extranet. Cela peut être dû au fait que l’utilisateur a effectué une authentification multifacteur peu de temps avant de quitter l’intranet. Par conséquent, il a une revendication d’authentification multifacteur valide dans son jeton d’accès. Cette revendication d’authentification multifacteur satisfait les exigences de la stratégie et Azure AD n’affiche donc pas de nouvelle demande d’authentification multifacteur.

#### <a name="token-lifetime-policy"></a>Stratégie de durée de vie des jetons

Au-delà de l’expiration des réclamations individuelles des jetons, les jetons eux-mêmes présentent des délais d’expiration. Comme mentionné précédemment, l’expiration des jetons est l’une des raisons de l’interruption d’une authentification unique. Vous pouvez définir la durée de vie d’un jeton émis par Azure AD à l’aide de [stratégies de durée de vie du jeton](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes). Par conséquent, il est plus difficile de définir une session d’authentification unique. Par exemple, pour les applications riches, car différents facteurs apparemment distincts peuvent affecter la durée de vie d’une session d’authentification unique.

>[!NOTE]
>Les administrateurs généraux Azure AD peuvent contrôler les périodes de validité et d’inactivité des jetons d’actualisation. Pour en savoir plus sur les jetons d’accès et les revendications recourant aux paramètres décrits, consultez l’article [Durées de vie des jetons configurables dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes).
>

#### <a name="primary-refresh-tokens"></a>Jetons d’actualisation principaux

Jusqu’ici, cet article décrit le fonctionnement de l’authentification unique dans le contexte d’un seul client. Mais cela n’est pas suffisant pour une expérience d’authentification unique dans une seule application. Aujourd’hui, les utilisateurs font l’expérience de workflows interactifs englobant plusieurs applications au sein de la même suite d’applications, comme Microsoft Office. Les utilisateurs doivent accéder à des applications non liées, y compris des applications LOB développées en interne. 

En règle générale, les appareils Windows qui sont joints à un domaine et utilisent l’authentification intégrée Windows (Kerberos) bénéficient d’un niveau élevé d’authentification unique dans de nombreuses applications et ressources. Ces applications comprennent les navigateurs pris en charge, tels qu’Internet Explorer ou Edge. Il en va de même pour le domaine Azure AD, par le biais d’un jeton d’actualisation principal (PRT). 

Ce jeton PRT privilégié est uniquement disponible pour la sélection d’un ensemble d’entités clientes (par exemple, les composants système de plateforme). Ces entités permettent ensuite d’accéder par authentification répartie à d’autres applications clientes, afin de proposer également une expérience d’authentification unique transparente. 

Pour les utilisateurs d’Office 365 sur les appareils [iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios) et [Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android), le nombre d’authentifications requises a été réduit grâce à l’application d’une fonctionnalité de répartition d’authentification. Cette fonctionnalité est intégrée dans les applications Microsoft Authenticator et Portail d’entreprise Intune.

>[!NOTE]
>Les recommandations décrites dans ce document supposent que l’une des applications a été déployée sur les appareils iOS ou Android des utilisateurs. Par exemple, une application de type répartiteur d’authentification (Microsoft Authenticator ou Portail d’entreprise Intune).
>

### <a name="multi-factor-authentication"></a>Authentification multifacteur

L’authentification multifacteur (MFA) offre un niveau élevé de confiance sur l’objet de l’authentification, car cet objet fournit plusieurs preuves ou éléments de preuve concernant son identité. Les preuves peuvent être des secrets établis au préalable et dont seuls le sujet et l’autorité ont connaissance, ou une entité physique que seul le sujet est censé posséder. L’authentification multifacteur s’effectue généralement par étapes. Tout d’abord, elle établit l’identité à l’aide de mots de passe, puis elle requiert une méthode d’authentification différente (moins sujette aux attaques) en tant que second facteur, ou inversement.

Plusieurs autorités peuvent avoir une interprétation légèrement différente de l’authentification multifacteur, ou authentification forte. Par exemple, certaines autorités (plus spécifiquement, les administrateurs configurant la stratégie sur ces autorités) peuvent choisir d’interpréter l’authentification basée sur une carte à puce physique en tant qu’authentification multifacteur. Cela peut se produire même si l’authentification par carte à puce à strictement parler est une authentification à une seule étape. 

La combinaison de la demande d’une carte à puce physique et l’obligation d’entrer un code PIN (secret) pour utiliser une carte à puce peut être interprétée comme une authentification multifacteur. D’autres peuvent choisir une stratégie plus modérée en termes de fréquence à laquelle des méthodes d’authentification onéreuses sont requises. Dans ce cas, les authentifications normales, qui ont lieu entre des authentifications plus fortes, sont considérées comme valides pour les ressources qui nécessitent généralement une authentification forte. Par exemple, dans certaines organisations, il peut être acceptable de demander à un utilisateur de procéder à une authentification multifacteur toutes les quelques heures ou tous les quelques jours. Cette donnée dépend de la sensibilité des ressources protégées et de la préservation de l’emplacement physique de l’utilisateur qui tente d’accéder à une ressource.

Azure AD et AD FS utilisent la revendication MFA pour indiquer si l’authentification s’effectue par le biais de MFA. Par défaut, Azure AD émet des jetons avec une revendication MFA lorsque l’authentification est effectuée avec [Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud) ou [Windows Hello Entreprise](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). Dans les scénarios de fédération, Azure AD respecte la revendication MFA provenant de fournisseurs d’identité fédérés comme AD FS, et transfère la réclamation MFA dans les jetons. 



## <a name="recommended-client-configuration-for-sso-and-conditional-access"></a>Configuration du client recommandée pour l’authentification unique et l’accès conditionnel
Cette section décrit les configurations de client de plateforme par défaut que nous vous recommandons pour offrir une expérience d’authentification unique optimale à vos utilisateurs, ainsi que les conditions préalables techniques pour l’accès conditionnel.

### <a name="windows-devices"></a>Appareils Windows
Nous vous recommandons d’utiliser Windows 10 (version 1703 ou version ultérieure), car Azure est conçu pour fournir l’authentification unique la plus fluide possible en local et sur Azure AD. Les appareils scolaires ou professionnels doivent être configurés de manière à joindre Azure AD directement ou, si l’organisation utilise la jonction de domaine AD locale, ces appareils doivent être [configurés pour s’inscrire automatiquement et silencieusement auprès d’Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup). 

Pour les appareils BYOD Windows, les utilisateurs peuvent choisir l’option Ajouter un compte professionnel ou scolaire. Pour les appareils BYOD Windows, les utilisateurs peuvent choisir l’option Ajouter un compte professionnel ou scolaire. Les utilisateurs du navigateur Chrome sur Windows 10 doivent [installer une extension](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?utm_source=chrome-app-launcher-info-dialog) pour bénéficier de la même fluidité de connexion que dans Edge/IE. En outre, si votre organisation dispose d’appareils Windows 7 joints au domaine, vous pouvez installer le package Microsoft Workplace Join pour les ordinateurs ne disposant pas de Windows 10 [afin d’inscrire](https://www.microsoft.com/download/details.aspx?id=53554) les appareils auprès d’Azure AD.

### <a name="ios-devices"></a>Appareils iOS

Nous vous recommandons d’installer l’[application Microsoft Authenticator](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to) sur les appareils des utilisateurs avant de déployer l’accès conditionnel ou les stratégies d’authentification multifacteur. Au minimum, l’application doit être installée lorsque les utilisateurs sont [invités à inscrire leur appareil](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time) auprès d’Azure AD en ajoutant un compte professionnel ou scolaire, ou lorsqu’ils installent l’application Portail d’entreprise Intune afin d’inscrire leur appareil dans la gestion. Cela dépend de la stratégie d’accès conditionnel configurée.

### <a name="android-devices"></a>Appareils Android

Nous recommandons aux utilisateurs d’installer l’[application Portail d’entreprise Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal&hl=en
) et l’[application Microsoft Authenticator](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to) avant le déploiement des stratégies d’accès conditionnel, ou à la demande au moment de certaines tentatives d’authentification. Après l’installation de l’application, les utilisateurs peuvent être invités à inscrire auprès d’Azure AD ou à inscrire leurs appareils auprès d’Intune. Cela dépend de la stratégie d’accès conditionnel configurée. 

Nous recommandons également que les appareils d’entreprise soient normalisés sur les OEM et les versions prenant en charge Android for Work ou Samsung Knox afin d’autoriser la gestion et la protection des comptes par la stratégie GPM Intune.


### <a name="recommended-email-clients"></a>Clients de messagerie recommandés
Les clients de messagerie suivants prennent en charge l’authentification moderne et l’accès conditionnel. Azure Information Protection n’est pas encore disponible pour tous les clients.

|Plateforme|Client|Version/Notes|Azure Information Protection|
|:-------|:-----|:------------|:--------------------|
|**Windows**|Outlook|2016, 2013 ([activer l’authentification moderne]((https://support.office.com/article/Enable-Modern-Authentication-for-Office-2013-on-Windows-devices-7dc1c01a-090f-4971-9677-f1b192d6c910)))|Oui|
|**iOS**|Outlook|[La plus récente](https://itunes.apple.com/us/app/microsoft-outlook-email-and-calendar/id951937596?mt=8)|Non|
|**Android**|Outlook|[La plus récente](https://play.google.com/store/apps/details?id=com.microsoft.office.outlook&hl=en)|Non|
|**macOS**|Bientôt pris en charge||Non|
|**Linux**|Non pris en charge||Non|

#### <a name="additional-client-software"></a>Logiciels clients supplémentaires
Pour accéder à des documents protégés Azure Information Protection, des logiciels supplémentaires peuvent être nécessaires. Assurez-vous que vous utilisez [des logiciels et des formats de document pris en charge](https://docs.microsoft.com/information-protection/get-started/requirements-applications) pour créer et afficher des documents protégés avec Azure Information Protection.


### <a name="security-guidelines"></a>Conseils de sécurité

Cette section contient des directives générales de sécurité pour l’implémentation des recommandations figurant dans les sections suivantes.

#### <a name="security-and-productivity-trade-offs"></a>Compromis entre sécurité et productivité

Il est nécessaire de faire un compromis entre sécurité et productivité. Pour faciliter la compréhension de ces compromis, la triade Sécurité-Fonctionnalités-Facilité d’utilisation/sécurité d’utilisation (SFU) est souvent utilisée :

![Compromis entre sécurité et productivité](./media/secure-email/security-triad.png)

Selon le modèle de triade SFU, les recommandations de ce document reposent sur les principes suivants : 

* Connaître l’audience : s’adapter à la fonction/au niveau de sécurité
* Appliquer la stratégie de sécurité juste à temps et s’assurer de sa pertinence

#### <a name="administrators-versus-users"></a>Administrateurs et utilisateurs

Nous vous recommandons de créer des groupes de sécurité contenant tous les utilisateurs qui disposent de comptes d’administration ou qui sont admissibles pour recevoir des privilèges de compte d’administration de façon temporaire. Ces groupes de sécurité permettent de définir des stratégies d’accès conditionnel spécifiques d’Azure AD et des administrateurs Office 365.  

Les recommandations stratégiques tiennent compte des privilèges associés à un compte. Les rôles d’[administrateur Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) ont bien plus de privilèges vis-à-vis d’Exchange Online et d’Office 365. Par conséquent, nos recommandations stratégiques pour ces comptes sont plus strictes que pour les comptes d’utilisateur standard. Toutes les stratégies qui font référence aux administrateurs indiquent la stratégie recommandée pour les comptes d’administration Office 365.

#### <a name="reduce-the-number-of-accounts-with-persistent-admin-access"></a>Réduire le nombre de comptes avec accès administrateur permanent

Azure AD Privileged Identity Management permet de réduire le nombre de comptes d’administration permanents. En outre, nous recommandons que les administrateurs Office 365 disposent d’un autre compte d’utilisateur pour les tâches non administratives régulières et se servent uniquement de leur compte d’administration uniquement lorsqu’ils doivent effectuer une tâche associée à leur fonction.


## <a name="tiers-of-security-and-protection"></a>Niveaux de sécurité et de protection
La plupart des organisations ont des conditions spécifiques relatives à la sécurité et à la protection des données. Ces exigences varient selon le secteur et les fonctions au sein des organisations. Par exemple, votre service juridique et les administrateurs Office 365 peuvent demander une sécurité supplémentaire et des contrôles de protection des informations pour leur correspondance par e-mail, qui ne sont pas requis pour d’autres utilisateurs de la division. 

Chaque secteur possède également son propre ensemble de réglementations spécialisées. Au lieu de fournir une liste de toutes les options possibles pour la sécurité ou une recommandation par fonction ou secteur, les recommandations ont été fournies pour trois niveaux de sécurité et de protection qui peuvent être appliqués en fonction de la granularité de vos besoins : [protection de base, sensible et hautement réglementée](https://go.microsoft.com/fwlink/p/?linkid=841656).  

**De base**. Nous vous recommandons de déterminer une norme minimale pour la protection des données, ainsi que pour les identités et les appareils qui accèdent à vos données. Des recommandations de base peuvent être suivies pour fournir une protection renforcée par défaut, qui répond aux besoins de nombreuses organisations. 

**Sensible**. Certains clients ont un sous-ensemble de données qui doit être protégé à des niveaux supérieurs ou exigent que toutes les données soient protégées à ces niveaux supérieurs. Vous pouvez appliquer une protection renforcée à tous les jeux de données ou à certains d’entre eux dans votre environnement Office 365. Nous vous recommandons de protéger les identités et les appareils qui accèdent aux données sensibles avec des niveaux de sécurité comparables. 

**Hautement réglementée**. Certaines organisations peuvent n’avoir qu’une très petite quantité de données associées à une classification élevée, un secret commercial ou des données réglementées. Microsoft fournit des fonctionnalités qui aident les organisations à respecter ces exigences, y compris une protection renforcée des appareils et des identités. 

### <a name="default-protection-mechanism-recommendations"></a>Recommandations liées au mécanisme de protection par défaut
Le tableau suivant contient des recommandations liées au mécanisme de protection par défaut pour chacun des niveaux de protection et de sécurité précédemment définis :

|Mécanisme de protection|Ligne de base|Sensible|Hautement réglementé|
|:-------------------|:-------|:--------|:---------------|
|**Authentification multifacteur**|Risque de connexion moyen ou plus élevé|Risque de connexion faible ou plus élevé|Toutes les nouvelles sessions|
|**Modification du mot de passe**|Utilisateurs à haut risque|Utilisateurs à haut risque|Utilisateurs à haut risque|
|**Intune App Protection**|Oui|Oui|Oui|
|**Inscription auprès d’Intune**|Nécessite un appareil conforme ou joint au domaine|Nécessite un appareil conforme ou joint au domaine|Nécessite un appareil conforme ou joint au domaine|

### <a name="device-ownership"></a>Propriété de l’appareil
Le tableau ci-dessus reflète la tendance pour de nombreuses organisations à prendre en charge une combinaison d’appareils d’entreprise et d’appareils personnels ou BYOD pour permettre la productivité mobile de leur personnel. Les stratégies Intune App Protection garantissent que les e-mails ne peuvent pas être exfiltrés de l’application mobile Outlook ni d’autres applications mobiles Office, que ce soit sur les appareils d’entreprise ou BYOD.  

Les appareils d’entreprise doivent être gérés par Intune ou joints à un domaine pour permettre l’application de protections et de contrôles supplémentaires.  Selon la sensibilité des données, votre organisation peut choisir de ne pas autoriser les appareils BYOD pour des types d’utilisateur ou des applications spécifiques.


## <a name="next-steps"></a>Étapes suivantes
[Sécuriser les stratégies de messagerie électronique et les configurations](secure-email-recommended-policies.md)

