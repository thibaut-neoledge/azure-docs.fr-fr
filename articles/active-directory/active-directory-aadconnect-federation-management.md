<properties
	pageTitle="Gestion AD FS et personnalisation avec Azure AD Connect | Microsoft Azure"
	description="Gestion AD FS à l’aide d’Azure AD Connect et personnalisation de l’expérience de connexion de l’utilisateur à AD FS à l’aide d’Azure AD Connect et Powershell."
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
	ms.date="04/14/2016"
	ms.author="anandy"/>

# Gestion AD FS et personnalisation avec Azure AD Connect

Cet article présente les différentes tâches associées à AD FS qui peuvent être effectuées à l’aide d’Azure AD Connect, ainsi que les autres tâches AD FS courantes qui peuvent être nécessaires pour configurer entièrement une batterie de serveurs AD FS.

## Gestion AD FS

Azure AD Connect intègre différentes tâches associées à AD FS qui peuvent être effectuées à l’aide de l’Assistant Azure AD Connect avec une intervention minime de l’utilisateur. Une fois que vous avez terminé l’installation d’Azure AD Connect à l’aide de l’Assistant, vous pouvez à nouveau exécuter l’Assistant pour effectuer des tâches supplémentaires.

### Réparation de l’approbation

Azure AD Connect peut vérifier l’intégrité des services AD FS et Azure AD actuels et prendre des mesures appropriées pour réparer l’approbation. Suivez les étapes ci-dessous pour réparer l’approbation des services Azure AD et AD FS.

Sélectionnez **Réparer l’approbation des services Azure AD et ADFS** dans la liste des tâches disponibles.

![](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

Sur la page **Connexion à Azure AD**, fournissez vos informations d’identification d’administrateur global pour Azure AD, puis cliquez sur Suivant.

![](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

Sur la page **Informations d’identification d’accès à distance**, indiquez les informations d’identification de l’administrateur de domaine

![](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

Lorsque vous cliquez sur Suivant, Azure AD Connect vérifie l’intégrité du certificat et affiche les problèmes s’ils existent.

![](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

La page **Prêt à configurer** affiche la liste des actions qui seront effectuées afin de réparer l’approbation.

![](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

Cliquez sur Installer pour poursuivre et réparer l’approbation.

>[AZURE.NOTE] Azure AD Connect peut seulement réparer/agir sur les certificats qui sont auto-signés. Les certificats tiers ne peuvent pas être réparés par Azure AD Connect.

### Ajout d’un nouveau serveur AD FS

> [AZURE.NOTE] Azure AD Connect exige que le fichier de certificat PFX ajoute un serveur AD FS. Par conséquent, vous ne pouvez effectuer cette opération que si vous avez configuré la batterie de serveurs AD FS à l’aide d’Azure AD Connect.

Sélectionnez **Déploiement d’un serveur de fédération supplémentaire** et cliquez sur Suivant.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

Sur la page **Connexion à Azure AD**, fournissez vos informations d’identification d’administrateur global pour Azure AD, puis cliquez sur Suivant.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

Fournissez les informations d’identification de l’administrateur de domaine sur la page suivante.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

Sur la page suivante, Azure AD Connect vous demande le mot de passe du fichier pfx que vous avez fourni lors de la configuration de votre batterie de serveurs AD FS avec Azure AD Connect. Cliquez sur Saisie du mot de passe pour fournir le mot de passe du fichier PFX.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

Sur la page suivante, fournissez le nom ou l’adresse IP supplémentaire du ou des serveurs à ajouter à la batterie de serveurs AD FS.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

Cliquez sur Suivant et parcourez la page Configurer finale. Une fois qu’Azure AD Connect a fini d’ajouter des serveurs à la batterie de serveurs AD FS, vous avez la possibilité de vérifier la connectivité.

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

![](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### Ajout d’un nouveau serveur WAP AD FS

> [AZURE.NOTE] Azure AD Connect exige le fichier de certificat PFX pour ajouter un serveur WAP. Par conséquent, vous ne pouvez effectuer cette opération que si vous avez configuré la batterie de serveurs AD FS à l’aide d’Azure AD Connect.

Sélectionnez **Déployer le proxy d’application web** dans la liste des tâches disponibles.

![](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

Sur la page suivante, fournissez les informations d’identification de l’administrateur global Azure.

![](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

La page **Spécifiez le certificat SSL** s’affiche ensuite. Vous devez y fournir le mot de passe du fichier PFX que vous avez fourni en configurant la batterie de serveurs AD FS avec Azure AD Connect.

![](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

![](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

Sur la page suivante, ajoutez le serveur à ajouter en tant que WAP. Étant donné que le serveur WAP peut être joint ou non au domaine, l’Assistant vous demande les informations d’identification administratives du serveur en cours d’ajout.

![](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

Sur la page **Informations d’identification de confiance du proxy**, fournissez des informations d’identification administratives pour configurer la confiance du proxy et accéder au serveur principal dans la batterie de serveurs AD FS.

![](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

Sur la page **Prêt à configurer**, l’Assistant affiche la liste des actions qui seront effectuées.

![](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

Cliquez sur Installer pour terminer la configuration. Une fois la configuration terminée, l’Assistant vous offre la possibilité de vérifier la connectivité aux serveurs. Cliquez sur Vérifier pour vérifier la connectivité.

![](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### Ajouter un nouveau domaine fédéré

Il est facile d’ajouter un nouveau domaine à fédérer avec Azure AD à l’aide d’Azure AD Connect. Azure AD Connect ajoute non seulement le nouveau domaine pour la fédération, mais modifie également les règles de revendication afin de refléter correctement l’émetteur dans le cas où plusieurs domaines sont fédérés avec Azure AD.

Pour ajouter un nouveau domaine fédéré, sélectionnez la tâche **Ajouter un domaine Azure AD supplémentaire**.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

Sur la page suivante de l’Assistant, fournissez les informations d’identification de l’administrateur global d’Azure AD.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

Sur la page Informations d’identification d’accès à distance, fournissez les informations d’identification de l’administrateur de domaine

![](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

Sur la page suivante, l’Assistant présente une liste de domaines Azure AD avec lesquels vous souhaitez fédérer votre annuaire local. Sélectionnez le domaine dans la liste.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

Après avoir choisi le domaine, l’Assistant présente des informations utiles concernant les autres actions qui seront effectuées par l’Assistant et l’impact de la configuration. Dans certains cas, si vous sélectionnez un domaine qui n’est pas encore vérifié dans Azure AD, l’Assistant vous fournit des informations pour vous aider à vérifier le domaine. Consultez la page [Ajouter et vérifier un nom de domaine personnalisé dans Azure Active Directory](active-directory-add-domain-add-verify-general.md) pour plus d’informations sur la façon de vérifier votre domaine.

Cliquez sur Suivant. La page **Prêt à configurer** affiche la liste des actions qui seront effectuées par Azure AD Connect. Cliquez sur Installer pour terminer la configuration.

![](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## Personnalisation d’AD FS

Les sections suivantes fournissent des informations concernant certaines tâches courantes que vous devrez peut-être effectuer pour personnaliser votre page de connexion AD FS.

### Ajouter le logo ou l’illustration personnalisé(e) de la société

Pour changer le logo de la société affiché sur la page de connexion, utilisez l’applet de commande PowerShell et la syntaxe Windows PowerShell suivantes.

> [AZURE.NOTE] Les dimensions recommandées pour le logo sont 260 x 35 à 96 PPP avec une taille de fichier ne dépassant pas 10 Ko.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] Le paramètre TargetName est obligatoire. Le thème par défaut qui est publié avec AD FS est nommé par défaut.
 

### Ajouter une description de connexion

Pour ajouter une description de page de connexion à la page de connexion, utilisez l’applet de commande PowerShell et la syntaxe Windows PowerShell suivantes.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### Modification des règles de revendication AD FS

AD FS offre la possibilité de spécifier des règles personnalisées pour émettre des revendications. Il prend en charge un langage de revendication complet qui vous permet de créer des règles de revendication personnalisées. Pour plus d’informations, vous pouvez consulter l’article [ici](https://technet.microsoft.com/library/dd807118.aspx).

Les sections suivantes expliquent en détail comment écrire des règles personnalisées pour certains scénarios se rapportant à la fédération Azure AD et AD FS.

#### ID non modifiable à condition que la valeur soit présente dans l’attribut

Azure AD Connect vous permet de spécifier un attribut à utiliser comme ancre source lorsque les objets sont synchronisés avec Azure AD. Vous voudrez peut-être émettre une revendication d’ID non modifiable reposant sur la condition suivante : la valeur de l’attribut personnalisé n’est pas vide. Dans l’exemple, on suppose que vous avez choisi ms-ds-consistencyguid comme attribut pour l’ancre source et que vous voulez émettre l’ID non modifiable en tant que ms-ds-consistencyguid si l’attribut comporte une valeur ; sinon, vous allez émettre objectGuid comme ID non modifiable. Vous pouvez construire le jeu de règles de revendications personnalisées comme décrit ci-dessous :

**Règle 1 (attributs de la requête)**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Dans cette règle, vous interrogez simplement les valeurs de ms-ds-consistencyguid et objectguid pour l’utilisateur à partir d’Active Directory. Remplacez le nom du magasin par le nom de magasin approprié disponible dans votre déploiement ADFS et le type de revendication par le type de revendication approprié disponible dans votre fédération pour objectGUID et ms-ds-consistencyguid. J’ai défini des types de revendication personnalisés dans mon environnement de test.

Par ailleurs, en utilisant « add » et pas « issue », vous évitez d’ajouter un problème à la sortie de l’entité et vous utilisez seulement les valeurs en tant que valeurs intermédiaires. Vous allez émettre la revendication dans une règle ultérieure une fois que vous aurez établi quelle valeur utiliser comme ID non modifiable

**Règle 2 : (Vérifier si ms-ds-consistencyguid existe pour l’utilisateur)**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Cette règle définit simplement un indicateur temporaire « idflag », qui est défini sur « useguid » si aucun ms-ds-concistencyguid n’est renseigné pour l’utilisateur. Il y a une logique à cela : ADFS n’autorise pas les revendications vides. Par conséquent, lorsque vous avez ajouté des revendications http://contoso.com/ws/2016/02/identity/claims/objectguid et http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid dans la règle 1, vous obtenez une revendication msdsconsistencyguid UNIQUEMENT si la valeur est renseignée pour l’utilisateur. Dans le cas où elle n’est pas renseignée, ADFS comprend que la valeur sera vide et dépose à cet emplacement la revendication ObjectGuid qui est appliquée à tous les objets. Ainsi, la revendication ObjectGuid est toujours présente après l’exécution de la règle 1.

**Règle 3 : Émettre ms-ds-consistencyguid en tant qu’ID non modifiable si présent** c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"] => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Il s’agit d’un contrôle EXIST implicite. Si la valeur de la revendication existe, alors émettez-la en tant qu’ID non modifiable. Notez que j’émets la revendication nameidentifier. Vous devez la remplacer par un type de revendication approprié pour l’ID non modifiable dans votre environnement.

**Règle 4 : Émettre ObjectGuid comme ID non modifiable si ms-ds-consistencyGuid n’est pas présent**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

Dans cette règle, vous contrôlez simplement l’indicateur temporaire « idflag » et en fonction de la valeur, vous décidez d’émettre ou non la revendication.

> [AZURE.NOTE] L’ordre de ces règles est important.

<!---HONumber=AcomDC_0427_2016-->