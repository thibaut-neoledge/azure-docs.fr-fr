<properties
	pageTitle="Meilleures pratiques pour la modification de la configuration par défaut"
	description="Décrit les méthodes conseillées pour la modification de la configuration par défaut d’Azure AD Connect Sync."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect Sync : meilleures pratiques pour modifier la configuration par défaut

La configuration créée par Azure AD Connect fonctionne « telle quelle » pour la plupart des environnements qui synchronisent Active Directory local avec Azure AD.<br> Toutefois, dans certains cas, il est nécessaire d’appliquer certaines modifications à une configuration pour répondre à une spécification ou un besoin particulier.

Bien que l’application de modifications à votre configuration Azure AD soit prise en charge, vous devez les appliquer avec précaution, car Azure AD est supposée être une appliance la plus ressemblante possible.

Voici une liste de comportements attendus :

- Après la mise à niveau d’Azure AD Connect vers une version plus récente, la valeur par défaut est rétablie pour la plupart des paramètres.
- Les modifications apportées aux règles de synchronisation de base sont perdues après la mise à niveau.
- Les règles de synchronisation de base supprimées sont recréées en cas de mise à niveau vers une version plus récente.
- Les règles de synchronisation personnalisées que vous avez créées restent intactes en cas de mise à niveau vers une version plus récente.



Lorsque vous devez modifier la configuration par défaut, procédez comme suit :

- Lorsque vous devez modifier le flux d’attributs d’une règle de synchronisation de base, ne modifiez pas cette règle. Créez plutôt une nouvelle règle de synchronisation avec une priorité plus élevée (valeur numérique inférieure), qui contient le flux d’attributs souhaité.
- Exportez vos règles de synchronisation personnalisées à l’aide de l’éditeur de règles de synchronisation. Cela vous donne un script PowerShell que vous pouvez utiliser pour facilement les recréer dans le cadre d’une récupération d’urgence.
- Si vous devez modifier la portée ou le paramètre de jointure dans une règle de synchronisation de base, documentez l’opération et appliquez la modification après la mise à niveau vers une version plus récente d’Azure AD Sync.



**Autres remarques importantes :**

- Si vous avez configuré la synchronisation de mot de passe et le filtrage basé sur les attributs, assurez-vous que seuls les objets qui sont synchronisés avec Azure AD sont dans la portée de la synchronisation de mot de passe. 





## Ressources supplémentaires

* [Azure AD Connect Sync : personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=July15_HO5-->