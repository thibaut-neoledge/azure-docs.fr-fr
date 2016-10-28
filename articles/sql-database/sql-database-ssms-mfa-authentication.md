<properties
   pageTitle="Prise en charge de SSMS pour Azure AD MFA avec la base de données SQL et SQL Data Warehouse | Microsoft Azure"
   description="Utilisez l’authentification multi-facteur avec SSMS pour la base de données SQL et SQL Data Warehouse."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/15/2016"
   ms.author="rick.byham@microsoft.com"/>

# Prise en charge de SSMS pour Azure AD MFA avec la base de données SQL et SQL Data Warehouse

La base de données SQL Azure et Azure SQL Data Warehouse prennent désormais en charge les connexions à partir de SQL Server Management Studio (SSMS) à l’aide de *l’authentification universelle Active Directory*. L’authentification universelle Active Directory est un workflow interactif prenant en charge *Azure Multi-Factor Authentication* (MFA). Azure MFA contribue à sécuriser l’accès aux données et aux applications tout en répondant à la demande des utilisateurs souhaitant un processus d’authentification simple. Il assure une authentification forte avec une gamme d'options de vérification simples (appel téléphonique, SMS, cartes à puce avec code PIN ou notification d'application mobile) pour permettre aux utilisateurs de choisir leur méthode préférée. Pour une description de Multi-Factor Authentication, consultez la rubrique [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).

SSMS prend désormais en charge :

- l’authentification multifacteur (MFA) interactive avec Azure AD avec l’affichage possible d’une boîte de dialogue contextuelle de validation ;
- un mot de passe Active Directory non-interactif et les méthodes d’authentification Active Directory intégrées qui peuvent être utilisés dans de nombreuses applications (ADO.NET, JDBC, ODBC, etc.). Ces deux méthodes n’entraînent jamais l’affichage de boîtes de dialogue contextuelles.

Lorsque le compte d’utilisateur est configuré pour MFA, le workflow d’authentification interactif nécessite une interaction supplémentaire de l’utilisateur (par exemple dans des boîtes de dialogue contextuelles ou avec l’utilisation d’une carte à puce, etc.). Lorsque le compte d’utilisateur est configuré pour MFA, l’utilisateur doit sélectionner l’authentification universelle Azure pour se connecter. Si le compte d’utilisateur ne nécessite pas MFA, l’utilisateur peut toujours utiliser les deux autres options d’authentification Azure Active Directory.

## Limites de l’authentification universelle pour la base de données SQL et SQL Data Warehouse

- SSMS est le seul outil actuellement activé pour MFA par le biais de l’authentification universelle Active Directory.
- Seul un compte Azure Active Directory unique peut se connecter à une instance de SSMS à l’aide de l’authentification universelle. Pour vous connecter comme un autre compte Azure AD, vous devez utiliser une autre instance de SSMS. (Cette restriction est limitée à l’authentification universelle Active Directory. Vous pouvez vous connecter à différents serveurs à l’aide de l’authentification de mot de passe Active Directory, l’authentification intégrée à Active Directory ou l’authentification SQL Server).
- SSMS prend en charge l’authentification universelle Active Directory pour la visualisation de l’Explorateur d’objets, de l’Éditeur de requête et du magasin de requêtes.
- Ni DacFx, ni le Concepteur de schémas ne prennent en charge l’authentification universelle.
- Les comptes MSA ne sont pas pris en charge pour l’authentification universelle Active Directory.
- L’authentification universelle Active Directory n’est pas prise en charge dans SSMS pour les utilisateurs qui sont importés dans l’Active Directory actuel à partir d’autres annuaires Active Directory.
- Il n’existe aucune configuration logicielle supplémentaire nécessaire pour l’authentification universelle Active Directory, sauf que vous devez utiliser une version prise en charge de SSMS.

## Configuration

L’implémentation de Multi-Factor Authentication implique quatre étapes de base.

1. **Configurer un Azure Active Directory** : pour plus d’informations, consultez [Intégration des identités locales avec Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Ajout de votre propre nom de domaine à Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure prend désormais en charge la fédération avec Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (en anglais), [Administration de votre annuaire Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx) et [Gestion d’Azure AD à l’aide de Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

2. **Configurer MFA** : pour obtenir des instructions pas à pas, consultez la page [Configuration d’Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-whats-next.md).

3. **Configurer la base de données SQL ou SQL Data Warehouse pour l’authentification Azure AD** : pour obtenir des instructions pas à pas, consultez la page [Connexion au service Base de données SQL ou SQL Data Warehouse avec l’authentification Azure Active Directory](sql-database-aad-authentication.md).

4. **Télécharger SSMS** : sur l’ordinateur client, téléchargez la dernière version de SSMS (au moins août 2016) depuis [Télécharger SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## Connexion à l’aide de l’authentification universelle avec SSMS

Les étapes suivantes montrent comment se connecter à la base de données SQL ou à SQL Data Warehouse à l’aide de la version la plus récente de SSMS.

1. Pour vous connecter à l’aide de l’authentification universelle, accédez à la boîte de dialogue **Se connecter au serveur**, puis sélectionnez **l’authentification universelle Active Directory**. ![1mfa-universal-connect][1]

2. Comme d’habitude pour la base de données SQL et SQL Data Warehouse, vous devez cliquer sur **Options** et spécifier la base de données dans la boîte de dialogue **Options**. Puis, cliquez sur **Se connecter**.
3. Lorsque la boîte de dialogue **Connectez-vous à votre compte** s’affiche, spécifiez le compte et le mot de passe de votre identité Azure Active Directory. ![2mfa-sign-in][2]

    > [AZURE.NOTE] Pour l’authentification universelle avec un compte qui ne nécessite pas MFA, vous vous connectez à ce stade. Pour les utilisateurs nécessitant MFA, passez aux étapes suivantes.
 
4. Deux boîtes de dialogue de configuration de MFA peuvent s’afficher. Cette opération à exécuter une seule fois dépend de l’administrateur du paramètre de l’administrateur MFA et peut être par conséquent facultative. Pour un domaine avec MFA activée, cette étape est parfois prédéfinie (par exemple, le domaine exige que les utilisateurs utilisent une carte à puce et un code confidentiel). ![3mfa-setup][3]

5. La deuxième boîte de dialogue unique possible vous permet de sélectionner les détails de votre méthode d’authentification. Les options possibles sont configurées par votre administrateur. ![4mfa-verify-1][4]
 
6. Azure Active Directory vous envoie les informations de confirmation. Lorsque vous recevez le code de vérification, entrez-le dans la zone **Entrez le code de vérification**, puis cliquez sur **Connexion**. ![5mfa-verify-2][5]

Lorsque la vérification est terminée, SSMS se connecte normalement à condition que les informations d’identification soient valides et qu’un accès au pare-feu soit possible.

##Étapes suivantes  

Accorder l’accès à votre base de données à d’autres utilisateurs : [Authentification et autorisation de base de données SQL : octroi de l’accès](sql-database-manage-logins.md) Assurez-vous que d’autres utilisateurs peuvent se connecter par le biais du pare-feu : [Configurer une règle de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md)


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

<!---HONumber=AcomDC_0817_2016-->