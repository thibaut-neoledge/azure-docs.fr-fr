<properties
	pageTitle="Gestion des services AD FS (Active Directory Federation Services) et personnalisation avec Azure AD Connect | azure.microsoft.com/ Azure"
	description="Gestion d’AD FS avec Azure AD Connect et personnalisation de la connexion de l’utilisateur à AD FS avec Azure AD Connect et Powershell."
	keywords="AD FS, ADFS, gestion AD FS, AAD Connect, Connect, connexion, personnalisation d’AD FS, réparer l’approbation, O365, fédération, partie de confiance"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="anandy"/>

# Gestion des services AD FS (Active Directory Federation Services) et personnalisation avec Azure AD Connect

Cet article présente différentes tâches associées à AD FS qu’il est possible d’effectuer à l’aide de azure.microsoft.com/ Azure Active Directory Connect, ainsi que d’autres tâches AD FS courantes qui peuvent être nécessaires pour configurer une batterie de serveurs AD FS.

| Rubrique | Sujet traité |
|:------|:-------------|
|**Gestion AD FS.**|
|[Réparation de l’approbation](#repairthetrust)| Réparation de l’approbation de fédération avec Office 365 |
|[Ajout d’un serveur AD FS](#addadfsserver) | Extension de la batterie de serveurs AD FS à l’aide d’un serveur AD FS supplémentaire|
|[Ajout d’un serveur proxy d’application web AD FS](#addwapserver) | Extension de la batterie de serveurs AD FS à l’aide d’un serveur WAP supplémentaire|
|[Ajout d’un domaine fédéré](#addfeddomain)| Ajout d’un domaine fédéré|
| **Personnalisation d’AD FS**|
|[Ajout d’une illustration ou d’un logo de société personnalisé](#customlogo)| Personnalisation de la page de connexion AD FS à l’aide d’une illustration ou d’un logo de société |
|[Ajout d’une description de connexion](#addsignindescription) | Ajout d’une description de la page de connexion |
|[Modification des règles de revendication AD FS](#modclaims) | Modification des revendications AD FS pour différents scénarios de fédération |

## Gestion AD FS.

Azure AD Connect intègre différentes tâches liées à AD FS qu’il est possible d’effectuer à l’aide de l’Assistant Azure AD Connect, avec une intervention minime de l’utilisateur. Une fois que vous avez terminé l’installation d’Azure AD Connect à l’aide de l’Assistant, vous pouvez à nouveau exécuter l’Assistant pour effectuer des tâches supplémentaires.

### Réparation de l’approbation <a name=repairthetrust></a>

Azure AD Connect peut vérifier l’intégrité de l’approbation des services AD FS et Azure Active Directory, et prendre les mesures appropriées pour la réparer. Pour réparer l’approbation des services Azure AD et AD FS, procédez comme suit :

1. Sélectionnez **Réparer la confiance AAD et ADFS** dans la liste des tâches disponibles. ![Réparer l’approbation d’AAD et d’ADFS](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

2. Dans la page **Connexion à Azure AD**, saisissez vos informations d’identification d’administrateur global d’Azure AD, puis cliquez sur **Suivant**. ![Se connecter à Azure AD](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

3. Dans la page **Informations d’identification d’accès à distance**, indiquez les informations d’identification de l’administrateur de domaine. ![Informations d’identification d’accès à distance](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

    Lorsque vous cliquez sur **Suivant**, Azure AD Connect vérifie l’intégrité du certificat et affiche les éventuels problèmes.

    ![État des certificats](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

    La page **Prêt à configurer** affiche la liste des actions qui seront effectuées afin de réparer l’approbation.

    ![Prêt à configurer](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

4. Cliquez sur **Installer** pour réparer l’approbation.

>[AZURE.NOTE] Azure AD Connect peut seulement réparer ou modifier les certificats auto-signés. Azure AD Connect ne permet pas de réparer les certificats tiers.

### Ajout d’un serveur AD FS <a name=addadfsserver></a>

> [AZURE.NOTE] Azure AD Connect requiert le fichier de certificat PFX pour ajouter un serveur AD FS. Par conséquent, vous ne pouvez effectuer cette opération que si vous avez configuré la batterie de serveurs AD FS à l’aide d’Azure AD Connect.

1. Sélectionnez **Déploiement d’un serveur de fédération supplémentaire**, puis cliquez sur **Suivant**. ![Serveur de fédération supplémentaire](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

2. Dans la page **Connexion à Azure AD**, saisissez vos informations d’identification d’administrateur global d’Azure AD, puis cliquez sur **Suivant**. ![Se connecter à Azure AD](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

3. Indiquez les informations d’identification de l’administrateur de domaine. ![Informations d’identification de l’administrateur de domaine](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

4. Azure AD Connect vous demande le mot de passe du fichier PFX que vous avez fourni lors de la configuration de votre nouvelle batterie de serveurs AD FS avec Azure AD Connect. Cliquez sur **Saisie du mot de passe** pour fournir le mot de passe du fichier PFX. ![Mot de passe du certificat](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

    ![Spécifier le certificat SSL](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

5. Dans la page **Serveurs AD FS**, entrez le nom ou l’adresse IP du serveur à ajouter à la batterie de serveurs AD FS. ![Serveurs AD FS](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

6. Cliquez sur **Suivant** et parcourez la page **Configurer** finale. Une fois qu’Azure AD Connect a fini d’ajouter les serveurs à la batterie de serveurs AD FS, vous avez la possibilité de vérifier la connectivité. ![Prêt à configurer](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

    ![Installation terminée](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### Ajout d’un serveur proxy d’application web AD FS <a name=addwapserver></a>

> [AZURE.NOTE] Azure AD Connect requiert le fichier de certificat PFX pour ajouter un serveur proxy d’application web. Par conséquent, vous ne pouvez effectuer cette opération que si vous avez configuré la batterie de serveurs AD FS à l’aide d’Azure AD Connect.

1. Sélectionnez **Déployer le proxy d’application web** dans la liste des tâches disponibles. ![Déployer le proxy d’application web](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

2. Indiquez les informations d’identification de l’administrateur global Azure. ![Se connecter à Azure AD](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

3. Dans la page **Spécifiez le certificat SSL**, indiquez le mot de passe du fichier PFX que vous avez fourni lors de la configuration de la batterie de serveurs AD FS avec Azure AD Connect. ![Mot de passe du certificat](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

    ![Spécifier le certificat SSL](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

4. Ajoutez le serveur à ajouter en tant que proxy d’application web. Comme le serveur proxy d’application web peut être joint ou non au domaine, l’Assistant vous demande les informations d’identification de l’administrateur du serveur en cours d’ajout. ![Informations d’identification du serveur d’administration](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

5. Dans la page **Informations d’identification de confiance du proxy**, indiquez les informations d’identification de l’administrateur pour configurer l’approbation du proxy et accéder au serveur principal dans la batterie de serveurs AD FS. ![Informations d’identification de confiance du proxy](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

6. Dans la page **Prêt à configurer**, l’Assistant affiche la liste des actions qui seront effectuées. ![Prêt à configurer](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

7. Cliquez sur **Installer** pour terminer la configuration. Une fois la configuration terminée, l’Assistant vous permet de vérifier la connectivité aux serveurs. Cliquez sur **Vérifier** pour vérifier la connectivité. ![Installation terminée](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### Ajout d’un domaine fédéré <a name=addfeddomain></a>

Il est facile d’ajouter un domaine à fédérer avec Azure AD à l’aide d’Azure AD Connect. Azure AD Connect ajoute le domaine de la fédération et modifie les règles de revendication pour identifier correctement l’émetteur lorsque plusieurs domaines sont fédérés avec Azure AD.

1. Pour ajouter un domaine fédéré, sélectionnez la tâche **Ajout d’un domaine Azure AD supplémentaire**. ![Domaine Azure AD supplémentaire](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

2. Dans la page suivante de l’Assistant, indiquez les informations d’identification de l’administrateur global d’Azure AD. ![Se connecter à Azure AD](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

3. Dans la page **Informations d’identification d’accès à distance**, indiquez les informations d’identification de l’administrateur de domaine. ![Informations d’identification d’accès à distance](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

4. Dans la page suivante, l’Assistant affiche une liste de domaines Azure AD dans lesquels vous pouvez fédérer votre annuaire local. Sélectionnez le domaine dans la liste ![Domaine Azure AD](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

    Après avoir choisi le domaine, l’Assistant présente des informations utiles concernant les autres actions qui seront effectuées par l’Assistant et l’impact de la configuration. Dans certains cas, si vous sélectionnez un domaine qui n’est pas encore vérifié dans Azure AD, l’Assistant affiche des informations pour vous aider à vérifier le domaine. Pour plus d’informations, consultez [Ajouter un nom de domaine personnalisé à Azure Active Directory](active-directory-add-domain.md).

5. Cliquez sur **Suivant**. La page **Prêt à configurer** affiche la liste des actions qui seront effectuées par Azure AD Connect. Cliquez sur **Installer** pour terminer la configuration. ![Prêt à configurer](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## Personnalisation d’AD FS

Les sections suivantes fournissent des informations concernant certaines tâches courantes que vous devrez peut-être effectuer pour personnaliser votre page de connexion AD FS.

### Ajout d’une illustration ou d’un logo de société personnalisé <a name=customlogo></a>

Pour changer le logo de la société affiché sur la page **Connexion**, utilisez l’applet de commande et la syntaxe Windows PowerShell suivantes.

> [AZURE.NOTE] Les dimensions recommandées pour le logo sont 260 x 35 à 96 PPP avec une taille de fichier maximale de 10 Ko.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] Le paramètre *TargetName* est obligatoire. Le thème par défaut publié avec AD FS est nommé Par défaut.


### Ajout d’une description de connexion <a name=addsignindescription></a>

Pour ajouter une description de la **page de connexion**, utilisez l’applet de commande et la syntaxe Windows PowerShell suivantes.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### Modification des règles de revendication AD FS <a name=modclaims></a>

AD FS prend en charge un langage complet, qui permet de créer des règles de revendication personnalisées. Pour plus d’informations, consultez [Rôle du langage de règle de revendication](https://technet.azure.microsoft.com/.com/library/dd807118.aspx).

Les sections suivantes décrivent comment écrire des règles personnalisées pour certains scénarios se rapportant à la fédération des services Azure AD et AD FS.

#### ID non modifiable à condition que la valeur soit présente dans l’attribut

Azure AD Connect vous permet de spécifier un attribut à utiliser comme ancre source, lorsque les objets sont synchronisés avec Azure AD. Si la valeur de l’attribut personnalisé n’est pas vide, vous souhaiterez peut-être émettre une revendication d’ID non modifiable. Par exemple, vous pouvez sélectionner **ms-ds-consistencyguid** comme attribut de l’ancre source et émettre **ms-ds-consistencyguid** comme **ImmutableID**, si l’attribut a une valeur. Si l’attribut n’a pas de valeur, émettez l’ID non modifiable **objectGuid**. Vous pouvez construire le jeu de règles de revendication personnalisées, comme indiqué dans la section suivante.

**Règle 1 : attributs de la requête**

    c:[Type == "http://schemas.azure.microsoft.com/.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Dans cette règle, vous interrogez les valeurs de **ms-ds-consistencyguid** et **objectGuid** de l’utilisateur à partir d’Active Directory. Remplacez le nom du magasin par un nom de magasin approprié dans votre déploiement AD FS. Remplacez également le type de revendication par un type approprié à votre fédération, comme défini pour **objectGuid** et **ms-ds-consistencyguid**.

Par ailleurs, utiliser **add** à la place de **issue** évite d’ajouter un problème à la sortie de l’entité et permet d’utiliser les valeurs comme des valeurs intermédiaires. Vous allez émettre la revendication dans une règle ultérieure, après avoir établi la valeur à utiliser comme ID non modifiable.

**Règle 2 : vérifier si ms-ds-consistencyguid existe pour l’utilisateur**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Cette règle définit un indicateur temporaire **idflag** dont la valeur est **useguid** si aucun ID **ms-ds-concistencyguid** n’est renseigné pour l’utilisateur. Il y a une logique à cela : AD FS n’autorise pas les revendications vides. Par conséquent, lorsque vous ajoutez les revendications http://contoso.com/ws/2016/02/identity/claims/objectguid et http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid dans la règle 1, vous n’obtenez une revendication **msdsconsistencyguid** que si la valeur est renseignée pour l’utilisateur. Si elle n’est pas indiquée, AD FS voit que sa valeur sera vide et le supprime immédiatement. Tous les objets auront un **objectGuid**. Donc, cette revendication sera toujours là après l’exécution de la règle 1.

**Règle 3 : émettre ms-ds-consistencyguid comme ID non modifiable s’il est présent**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Il s’agit d’un contrôle **Exist** implicite. Si la valeur de la revendication existe, alors émettez-la en tant qu’ID non modifiable. L’exemple précédent utilise le **nameidentifier** de revendication. Vous devez le remplacer par un type de revendication approprié pour l’ID non modifiable dans votre environnement.

**Règle 4 : émettre objectGuid comme ID non modifiable si ms-ds-consistencyGuid n’est pas présent**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

Dans cette règle, vous vérifiez simplement l’indicateur temporaire **idflag**. Vous décidez s’il faut émettre la revendication en fonction de sa valeur.

> [AZURE.NOTE] L’ordre de ces règles est important.

#### Authentification unique avec un UPN de sous-domaine

Vous pouvez ajouter plusieurs domaines à fédérer à l’aide d’Azure AD Connect, comme indiqué dans [Ajout d’un domaine fédéré](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain). Vous devez modifier la revendication UPN pour faire correspondre l’ID de l’émetteur au domaine racine et non au sous-domaine, car le domaine racine fédéré englobe également l’enfant.

Par défaut, la règle de revendication pour l’ID de l’émetteur est définie telle quelle :

	c:[Type
	== “http://schemas.xmlsoap.org/claims/UPN“]

	=> issue(Type = “http://schemas.azure.microsoft.com/.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Revendication de l’ID d’émetteur par défaut](media\active-directory-aadconnect-federation-management\issuer_id_default.png)

La règle par défaut prend simplement le suffixe UPN et l’utilise dans la revendication de l’ID de l’émetteur. Par exemple, John est un utilisateur de sub.contoso.com et contoso.com est fédéré à Azure AD. John entre john@sub.contoso.com comme nom d’utilisateur lors de la connexion à Azure AD. La règle de revendication de l’ID de l’émetteur par défaut dans AD FS le traite alors de la manière suivante :

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.azure.microsoft.com/.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Valeur de la revendication :** http://sub.contoso.com/adfs/services/trust/

Pour n’avoir que le domaine racine dans la valeur de revendication de l’émetteur, modifiez la règle de revendication de la manière suivante :

	c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

	=> issue(Type = “http://schemas.azure.microsoft.com/.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## Étapes suivantes

En savoir plus sur les [options de connexion de l’utilisateur](active-directory-aadconnect-user-signin.md).

<!---HONumber=AcomDC_0817_2016-->