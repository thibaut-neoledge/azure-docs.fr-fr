<properties 
	pageTitle="Gestion d’Azure AD Connect" 
	description="Apprenez à étendre la configuration par défaut et les tâches opérationnelles pour Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Gestion d’Azure AD Connect 


<center><div class="dev-center-tutorial-selector sublanding">
<a href="/fr-fr/documentation/articles/active-directory-aadconnect/" title="Présentation" class="current">Présentation</a> <a href="/fr-fr/documentation/articles/active-directory-aadconnect-how-it-works/" title="Fonctionnement">Fonctionnement</a> <a href="/fr-fr/documentation/articles/active-directory-aadconnect-get-started/" title="Prise en main">Prise en main</a> <a href="/fr-fr/documentation/articles/active-directory-aadconnect-get-manage/" title="">titleGestiontitleGestion</a></div></center>


Les rubriques vous permettent de personnaliser Azure Active Directory Connect pour l’adapter aux besoins et aux spécifications de votre organisation.

## Modification de la configuration par défaut
La configuration par défaut d’Azure AD Connect est suffisante dans la plupart des instances pour étendre facilement vos annuaires locaux dans le cloud. Toutefois il existe certains cas dans lesquels vous devrez peut-être modifier la valeur par défaut et l’adapter à la logique métier de votre entreprise. Dans ce cas, vous pouvez modifier la configuration par défaut. Cependant, il existe certaines choses que vous devez savoir avant de le faire.

Si vous effectuez une mise à niveau à partir d’Azure AD Sync ou de DirSync, tenez compte des éléments suivants :

- Après la mise à niveau d’Azure AD Sync vers une version plus récente, la valeur par défaut est rétablie pour la plupart des paramètres.
- Les modifications apportées aux règles de synchronisation de base sont perdues après la mise à niveau.
- Les règles de synchronisation de base supprimées sont recréées en cas de mise à niveau vers une version plus récente.
- Les règles de synchronisation personnalisées que vous avez créées restent intactes en cas de mise à niveau vers une version plus récente.

Lorsque vous devez modifier la configuration par défaut, procédez comme suit :

- Lorsque vous devez modifier le flux d’attributs d’une règle de synchronisation de base, ne modifiez pas cette règle. Créez plutôt une nouvelle règle de synchronisation avec une priorité plus élevée (valeur numérique inférieure), qui contient le flux d’attributs souhaité.
- Exportez vos règles de synchronisation personnalisées à l’aide de l’éditeur de règles de synchronisation. Cela vous donne un script PowerShell que vous pouvez utiliser pour facilement les recréer dans le cadre d’une récupération d’urgence.
- Si vous devez modifier la portée ou le paramètre de jointure dans une règle de synchronisation de base, documentez l’opération et appliquez la modification après la mise à niveau vers une version plus récente d’Azure AD Connect.






 

## Utilisation de l’éditeur de règles de synchronisation

Dans Azure AD Connect, vous pouvez configurer et optimiser le flux des objets et des attributs entre Azure AD et vos annuaires locaux en configurant des règles de synchronisation. Les règles de synchronisation personnalisées peuvent être configurées à l’aide de l’éditeur de règles de synchronisation. L’éditeur de règles de synchronisation est installé lors de l’installation d’Azure AD Connect. Pour utiliser l’éditeur, vous devez être membre du groupe ADSyncAdmins ou du groupe Administrateur que vous avez spécifié lors de l’installation d’Azure AD Connect.

Dans la capture d’écran ci-dessous, vous voyez toutes les règles de synchronisation créées pour votre configuration lorsque vous installez Azure AD Connect avec l’installation Express. Chaque ligne du tableau correspond à une règle de synchronisation. À gauche, sous Types de règles, deux types sont présentés : Entrante et Sortante Les termes entrante et sortante sont à prendre du point de vue du métaverse. Autrement dit, les informations proviennent de nos annuaires et vont vers le métaverse. Sortante se réfère aux règles dans lesquelles nous envoyons des informations et des attributs vers notre annuaire, par exemple notre Active Directory local ou notre Azure AD.

<center>![Synch Rules Editor](./media/active-directory-aadconnect-manage/Synch_Rule.png) </center>

Pour créer une règle, sélectionnez Ajouter une nouvelle règle, puis configurez cette règle. Par exemple, supposons que nous souhaitions créer une règle de jointure dans laquelle un utilisateur dans notre annuaire local sera joint à l’objet métaverse qui possède le même numéro de téléphone. Pour cela, créez la nouvelle règle et spécifiez le système connecté, dans notre cas contoso.com, le Type d’objet système connecté, l’utilisateur, le Type d’objet du métaverse, la personne et le Type de jointure de lien.

<center>![Create Sync rule](./media/active-directory-aadconnect-manage/synch2.png) </center>


Ensuite, dans l’écran des règles de jointure, spécifiez telephoneNumber dans l’attribut source et telephoneNumber dans l’attribut cible. Et voilà. Vous avez créé une règle de jointure.

<center>![Join Rule](./media/active-directory-aadconnect-manage/synch3.png) </center>

Vous pouvez utiliser l’éditeur de règles de synchronisation pour appliquer une logique métier supplémentaire en plus de la configuration par défaut et l’adapter aux besoins de votre organisation. Pour plus d’informations sur l’éditeur de règles de synchronisation, voir [Présentation de la configuration par défaut](https://msdn.microsoft.com/library/azure/dn800963.aspx).


## Utilisation de l’approvisionnement déclaratif 
L’approvisionnement déclaratif est un approvisionnement sans code, qui peut être défini et configuré à l’aide de l’éditeur de règles de synchronisation. L’éditeur peut être utilisé pour créer et configurer vos propres règles d’approvisionnement.

Le langage d’expression utilisé dans le flux d’attributs constitue une partie essentielle de l’approvisionnement déclaratif. Le langage utilisé est un sous-ensemble de Microsoft® Visual Basic® pour Applications (VBA). Ce langage est utilisé dans Microsoft Office et les utilisateurs qui ont une certaine expérience de VBScript le reconnaîtront également. Le langage d’expression de l’approvisionnement déclaratif utilise uniquement des fonctions. Il ne s’agit pas d’un langage structuré et il ne contient ni méthodes ni instructions. Les fonctions sont imbriquées pour exprimer le déroulement du programme.

Pour plus d’informations sur le langage d’expression, consultez [Présentation des expressions de l’approvisionnement déclaratif](https://msdn.microsoft.com/library/azure/dn801048.aspx)

## Documentation supplémentaire
Une partie de la documentation qui a été créée pour Azure AD Sync est toujours adaptée et s’applique à Azure AD Connect. Bien que nous nous efforcions de transférer cette documentation sur Azure.com, une partie se trouve encore dans la bibliothèque MSDN. Pour plus de documentation, consultez [Azure AD Connect sur MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) et [Azure AD Sync sur MSDN](https://msdn.microsoft.com/library/azure/dn790204.aspx).

**Ressources supplémentaires**

* [Utiliser votre infrastructure d’identité locale dans le cloud](../active-directory-aadconnect-whatis.md)
* [Fonctionnement d’Azure AD Connect](../active-directory-aadconnect-howitworks.md)
* [Prise en main d’Azure AD Connect](../active-directory-aadconnect-getstarted.md)
* [Gestion d’Azure AD Connect](active-directory-aadconnect-manage.md)
* [Azure AD Connect sur MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)
 

<!---HONumber=58_postMigration-->