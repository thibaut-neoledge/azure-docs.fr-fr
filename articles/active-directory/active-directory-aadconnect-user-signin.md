<properties
    pageTitle="Azure AD Connect : Connexion utilisateur | Microsoft Azure"
    description="Connexion utilisateur Azure AD Connect pour une configuration personnalisée."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>




# <a name="azure-ad-connect-user-sign-on-options"></a>Options d’authentification de l’utilisateur via Azure AD Connect

Azure AD Connect permet à vos utilisateurs de se connecter aux ressources cloud et locales à l’aide des mêmes mots de passe.  Vous pouvez activer cette fonction en procédant de différentes manières.

## <a name="choosing-a-user-sign-in-method"></a>Choix d’une méthode de connexion utilisateur
Pour la plupart des organisations qui souhaitent juste activer l’authentification utilisateur pour Office 365, les applications SaaS et d’autres ressources basées sur Azure AD, l’option de synchronisation de mot de passe par défaut est recommandée.
Certaines organisations ont toutefois des raisons particulières d’utiliser une option d’authentification fédérée comme AD FS.  Ces raisons peuvent être les suivantes :

- Votre organisation a déjà déployé AD FS ou un fournisseur de fédération tiers.
- Votre stratégie de sécurité empêche la synchronisation des hachages de mot de passe avec le cloud.
- Vous voulez que les utilisateurs profitent d’une authentification unique transparente (sans demandes supplémentaires du mot de passe) lorsqu’ils accèdent aux ressources cloud à partir d’appareils liés au domaine sur le réseau d’entreprise.
- Vous avez besoin de certaines des capacités spécifiques d’AD FS :
    - Authentification multifacteur locale à l’aide d’un fournisseur tiers ou de cartes à puce (en savoir plus sur les fournisseurs MFA tiers pour AD FS dans Windows Server 2012 R2)
    - Fonctionnalités d’intégration Active Directory telles que le verrouillage souple du compte ou une stratégie d’heures de travail et de mot de passe AD
    - Accès conditionnel aux ressources locales et cloud via l’inscription de l’appareil, une adhésion à Azure AD ou des stratégies Intune MDM

### <a name="password-synchronization"></a>Synchronisation du mot de passe
Avec la synchronisation du mot de passe, les hachages des mots de passe utilisateur sont synchronisés de votre annuaire Active Directory local vers Azure AD.  Lorsque les mots de passe sont modifiés ou réinitialisés localement, les nouveaux mots de passe sont immédiatement synchronisés avec Azure AD afin que vos utilisateurs puissent toujours utiliser le même mot de passe pour les ressources cloud comme pour les ressources locales.  Les mots de passe ne sont jamais envoyés à Azure AD ni stockés dans Azure AD sous forme de texte clair.
La synchronisation de mot de passe peut être utilisée avec l’écriture différée de mot de passe pour activer la réinitialisation du mot de passe libre-service dans Azure AD.

<center>![Cloud](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[En savoir plus à propos de la synchronisation de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md)


### <a name="federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm"></a>Fédération à l’aide des services AD FS nouveaux ou existants dans la batterie de serveurs Windows Server 2012 R2
Avec l’authentification fédérée, vos utilisateurs peuvent se connecter aux services Azure AD avec leurs mots de passe locaux sans avoir à les saisir de nouveau et ce, alors qu’ils sont sur le réseau d’entreprise.  L’option de fédération avec les services de fédération d’Azure Directory (AD FS) vous permet de déployer un nouveau service AD FS dans la batterie de serveurs Windows Server 2012 R2 ou d’en spécifier un existant.  Si vous choisissez de spécifier une batterie existante, Azure AD Connect configure l’approbation entre votre batterie et Azure AD de manière à ce que vos utilisateurs puissent s’authentifier.

<center>![Cloud](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="to-deploy-federation-with-ad-fs-in-windows-server-2012-r2,-you-will-need-the-following"></a>Éléments requis pour le déploiement de la fédération avec AD FS dans Windows Server 2012 R2

Si vous déployez une nouvelle batterie :

- Un serveur Windows Server 2012 R2 pour le serveur de fédération.
- Un serveur Windows Server 2012 R2 pour le proxy d’application web.
- Un fichier .pfx avec un certificat SSL unique pour le nom de votre service de fédération, par exemple fs.contoso.com.

Si vous déployez une nouvelle batterie ou si vous utilisez une batterie existante :

- Les informations d’identification de l’administrateur local de vos serveurs de fédération.
- Les informations d’identification de l’administrateur local des serveurs des groupes de travail (ne faisant pas partie d’un domaine) sur lesquels vous avez l’intention de déployer le rôle de proxy d’application web.
- L’appareil sur lequel vous exécutez l’Assistant doit être en mesure de se connecter à n’importe quel autre appareil sur lequel vous souhaitez installer AD FS ou un proxy d’application web via Windows Remote Management.

#### <a name="sign-on-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Authentification à l’aide d’une version antérieure d’AD FS ou d’une solution tierce

Si vous avez déjà configuré l’authentification cloud à l’aide d’une version antérieure d’AD FS (par exemple, AD FS 2.0) ou à l’aide d’un fournisseur de fédération tiers, vous pouvez choisir d’ignorer la configuration de la connexion utilisateur via Azure AD Connect.  Cela vous permet d’obtenir la dernière synchronisation et d’autres fonctionnalités d’Azure AD Connect tout en utilisant toujours votre solution d’authentification existante.

## <a name="user-sign-in-and-user-principal-name-(upn)"></a>Connexion de l’utilisateur et nom d’utilisateur principal (UPN)

### <a name="understanding-user-principal-name"></a>Présentation du nom d’utilisateur principal

Dans Active Directory, le suffixe UPN par défaut est le nom DNS du domaine dans lequel le compte d’utilisateur est créé. Dans la plupart des cas, il s’agit du nom de domaine enregistré en tant que domaine d’entreprise sur Internet. Toutefois, vous pouvez ajouter d’autres suffixes UPN avec les domaines et approbations Active Directory.

L’UPN de l’utilisateur est au format username@domai. Par exemple, pour un utilisateur contoso.com Active Directory, John pourrait avoir l’UPN john@contoso.com. L’UPN de l’utilisateur se base sur RFC 822. Bien que l’UPN et la messagerie partagent le même format, la valeur du nom UPN d’un utilisateur n’est pas forcément identique à l’adresse de messagerie de l’utilisateur.

### <a name="user-principal-name-in-azure-ad"></a>Nom d’utilisateur principal dans Azure AD

L’Assistant Azure AD Connect utilise l’attribut userPrincipalName ou vous laisse spécifier l’attribut (dans une installation personnalisée) à utiliser en local en tant que nom d’utilisateur principal dans Azure AD. Il s’agit de la valeur qui sera utilisée pour la connexion à Azure AD. Si la valeur de l’attribut du nom principal d’utilisateur ne correspond pas à un domaine vérifié dans Azure AD, Azure AD la remplacera par une valeur .onmicrosoft.com par défaut.

Dans Azure Active Directory, chaque annuaire est fourni avec un nom de domaine intégré se présentant sous la forme contoso.onmicrosoft.com qui vous permet de commencer à utiliser Azure ou d’autres services Microsoft. Vous pouvez améliorer et simplifier l’expérience de connexion avec les domaines personnalisés. Pour plus d’informations sur les noms de domaine personnalisés dans Azure AD et sur la vérification des domaines, consultez [Ajouter votre nom de domaine personnalisé à Azure Active Directory](active-directory-add-domain.md#add-your-custom-domain-name-to-azure-active-directory)

## <a name="azure-ad-sign-in-configuration"></a>Configuration de connexion AD Azure

### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Configuration de connexion AD Azure avec Azure AD Connect
L’expérience de connexion Azure AD dépend de la capacité d’Azure AD à faire correspondre le suffixe de nom principal d’un utilisateur en cours de synchronisation à l’un des domaines personnalisés vérifiés dans l’annuaire Azure AD. Azure AD Connect fournit une aide lors de la configuration des paramètres de connexion Azure AD, afin que l’expérience de connexion utilisateur sur le cloud soit similaire à l’expérience locale. Azure AD Connect répertorie les suffixes UPN définis pour le(s) domaine(s), tente de les mettre en correspondance avec un domaine personnalisé dans Azure AD et vous aide à effectuer l’action appropriée.
La page de connexion AD Azure répertorie les suffixes UPN définis pour Active Directory local et affiche l’état correspondant à chaque suffixe. L’état peut avoir les valeurs suivantes :

* Vérifié : Azure AD Connect a trouvé un domaine vérifié correspondant dans Azure AD et aucune action n’est nécessaire
* Non vérifié : Azure AD Connect a trouvé un domaine personnalisé correspondant dans Azure AD mais il n’est pas vérifié. L’utilisateur doit vérifier le domaine personnalisé afin que le suffixe UPN des utilisateurs ne soit pas modifié en .onmicrosoft.com (suffixe par défaut) après la synchronisation.
* Non ajouté : Azure AD Connect n’a pas trouvé de domaine personnalisé correspondant au suffixe UPN. L’utilisateur doit ajouter et vérifier un domaine personnalisé correspondant au suffixe UPN afin que le suffixe UPN de l’utilisateur ne soit pas modifié en .onmicrosoft.com (suffixe par défaut) après la synchronisation.

La page de connexion AD Azure répertorie le(s) suffixe(s) UPN défini(s) pour Active Directory local et le domaine personnalisé correspondant dans Azure AD avec l’état actuel de la vérification. Dans l’installation personnalisée, vous pouvez maintenant sélectionner l’attribut du nom d’utilisateur principal sur la page **Connexion à Azure AD** .

![Page de connexion d’Azure AD](.\media\active-directory-aadconnect-user-signin\custom_azure_sign_in.png)

Vous pouvez cliquer sur le bouton Actualiser pour extraire à nouveau le dernier état des domaines personnalisés à partir d’Azure AD.

###<a name="selecting-attribute-for-user-principal-name-in-azure-ad"></a>Sélection d’un attribut pour le nom d’utilisateur principal dans Azure AD

UserPrincipalName : l’attribut userPrincipalName est utilisé par les utilisateurs lorsqu’ils se connectent à Azure AD et Office 365. Les domaines utilisés, également nommés « Suffixe UPN » doivent être vérifiés dans Azure AD avant la synchronisation des utilisateurs. Il est fortement recommandé de conserver l’userPrincipalName de l’attribut par défaut. Si cet attribut ne peut pas être acheminé ni vérifié, vous pouvez sélectionner un autre attribut, par exemple une adresse de messagerie électronique, comme attribut contenant l’ID de connexion. Il s’agit de l’ID secondaire. La valeur de l’attribut ID secondaire doit suivre la norme RFC822. Un ID secondaire peut être utilisé avec l’authentification unique par mot de passe et avec l’authentification unique de fédération comme solution de connexion.

>[AZURE.NOTE] L’utilisation d’un ID secondaire n’est pas compatible avec toutes les charges de travail Office 365. Pour plus d’informations, consultez [Configuration d’un ID secondaire de connexion](https://technet.microsoft.com/library/dn659436.aspx).

#### <a name="different-custom-domain-states-and-effect-on-azure-sign-in-experience"></a>Différents états de domaine personnalisé et impact sur l’expérience de connexion Azure
Il est très important de comprendre la relation entre les états de domaine personnalisé dans votre annuaire Azure AD et les suffixes UPN définis en local. Passons en revue les différentes expériences de connexion Azure possibles lorsque vous configurez la synchronisation avec Azure AD Connect.

Pour les informations ci-dessous, supposons que nous nous intéressons au suffixe UPN contoso.com utilisé dans l’annuaire local dans le nom UPN, par exemple user@contoso.com.

###### <a name="express-settings-/-password-synchronization"></a>Configuration rapide / Synchronisation de mot de passe
| State         | Effet sur l’expérience de connexion utilisateur Azure |
|:-------------:|:----------------------------------------|
| Non ajouté | Dans ce cas, aucun domaine personnalisé pour contoso.com n’a été ajouté à l’annuaire Azure AD. Les utilisateurs possédant un UPN local avec le suffixe @contoso.com, ne pourront pas utiliser leur UPN local pour se connecter à Azure. Ils devront utiliser un nouvel UPN fourni par Azure AD en ajoutant le suffixe de l’annuaire Azure AD par défaut. Par exemple, si vous synchronisez des utilisateurs sur l’annuaire Azure AD azurecontoso.onmicrosoft.com, l’utilisateur local user@contoso.com aura un nom UPN user@azurecontoso.onmicrosoft.com|
| Non vérifié | Dans ce cas, nous avons un domaine personnalisé contoso.com ajouté à l’annuaire Azure AD, mais il n’est pas encore vérifié. Si vous poursuivez la synchronisation des utilisateurs sans vérifier le domaine, les utilisateurs se verront attribuer un nouvel UPN d’Azure AD, comme dans le scénario « Non ajouté ».|
| Verified | Dans ce cas, nous avons un domaine personnalisé contoso.com déjà ajouté et vérifié dans Azure AD pour le suffixe UPN. Les utilisateurs pourront utiliser leur nom d’utilisateur principal local, par exemple, user@contoso.com,, pour se connecter à Azure une fois qu’ils seront synchronisés sur Azure AD|

###### <a name="ad-fs-federation"></a>Fédération AD FS
Vous ne pouvez pas créer de fédération avec le domaine .onmicrosoft.com par défaut dans Azure AD ou un domaine personnalisé non vérifié dans Azure AD. Lorsque vous exécutez l’Assistant Azure AD Connect, si vous sélectionnez un domaine non vérifié pour créer une fédération, Azure AD Connect vous invitera à créer les enregistrements nécessaires à l’endroit où est hébergé votre DNS pour le domaine. Pour plus d’informations, suivez [ce lien](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Si vous avez sélectionné l’option de connexion utilisateur « Fédération avec AD FS », vous devez disposer d’un domaine personnalisé pour poursuivre la création d’une fédération dans Azure AD. Dans notre cas de figure, cela signifie que nous devons disposer d’un domaine personnalisé contoso.com ajouté dans l’annuaire Azure AD.

| State         | Effet sur l’expérience de connexion utilisateur Azure |
|:-------------:|:----------------------------------------|
| Non ajouté | Dans ce cas, Azure AD Connect n’a pas trouvé de domaine personnalisé correspondant au suffixe UPN contoso.com dans l’annuaire Azure AD. Vous devez ajouter un domaine personnalisé contoso.com si vous avez besoin que les utilisateurs se connectent à l’aide d’AD FS avec leur UPN local, par exemple user@contoso.com.|
| Non vérifié | Dans ce cas, Azure AD Connect vous fournira les informations appropriées sur les possibilités de vérification de votre domaine à un stade ultérieur|
| Verified | Dans ce cas, vous pouvez poursuivre la configuration sans autre action|  

## <a name="changing-user-sign-in-method"></a>Modification de la méthode de connexion utilisateur

Vous pouvez modifier la méthode de connexion utilisateur de Fédération à Synchronisation de mot de passe avec les tâches disponibles dans Azure AD Connect après la configuration initiale d’Azure AD Connect à l’aide de l’Assistant. Réexécutez l’Assistant Azure AD Connect. La liste des tâches que vous pouvez effectuer s’affiche. Sélectionnez **Modifier la connexion utilisateur** dans la liste des tâches. 

![Modifier la connexion utilisateur](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Sur la page suivante, vous devrez fournir les informations d’identification pour Azure AD.

![Se connecter à Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

Sur la page **Connexion utilisateur**, sélectionnez **Synchronisation de mot de passe**. Cette action modifie l’annuaire de fédéré à géré.

![Se connecter à Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin3.png)

>[AZURE.NOTE] Si vous passez à la synchronisation de mot de passe de façon temporaire, cochez **Ne pas convertir les comptes utilisateurs**. Si vous ne cochez pas l’option, chaque utilisateur devient fédéré, ce qui peut prendre plusieurs heures.
  
## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

En savoir plus sur [Azure AD Connect : principes de conception](active-directory-aadconnect-design-concepts.md)





<!--HONumber=Oct16_HO2-->


