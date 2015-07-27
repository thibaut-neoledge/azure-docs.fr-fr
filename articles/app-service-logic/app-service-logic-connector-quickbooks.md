<properties
   pageTitle="Connecteur QuickBooks"
   description="Utilisation du connecteur QuickBooks"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/02/2015"
   ms.author="sameerch"/>


#Utilisation du connecteur QuickBooks dans votre application logique#

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. Le connecteur QuickBooks vous permet de créer et de modifier différentes entités QuickBooks. Voici la liste des entités QuickBooks que le connecteur QuickBooks prend en charge.

<Table>
<TR><TD><B>Entités</TD><TD><B>Description</TR> <TR> <TD> Account </TD> <TD> Un compte est un composant d'un plan de comptes et fait partie d'un livre. Il permet d'enregistrer un montant monétaire alloué à une utilisation spécifique </TD> </TR> <TR> <TD> CreditMemo </TD> <TD> L'avoir est une transaction financière qui représente un remboursement, un crédit de paiement ou un acompte pour des biens ou des services vendus. </TD> </TR> <TR> <TD> Customer </TD> <TD> Un client est un consommateur du service ou du produit proposé par votre entreprise. </TD> </TR> <TR> <TD> Estimate </TD> <TD> L'estimation représente une proposition de transaction financière entre une entreprise et un client, concernant des biens ou des services en vue d'une vente ou de l'établissement d'un devis. </TD> </TR> <TR> <TD> Invoice </TD> <TD> Une facture formalise une vente dans laquelle le client règle un produit ou un service ultérieurement. D’autres informations sur l’utilisation du modèle de données Facture sont disponibles ici. </TD> </TR> <TR> <TD> Item </TD> <TD> Un élément est une chose que votre entreprise achète, vend ou revend, comme des produits, des frais d'expédition et de manutention, des remises et des taxes (le cas échéant). Un élément figure sur une ligne d’une facture ou d’un autre document de vente. </TD> </TR> <TR> <TD> SalesReceipt </TD> <TD> Cette entité représente le ticket remis au client. </TD> </TR> </Table>


##Actions QuickBooks ##
Voici les différentes actions disponibles dans le connecteur QuickBooks. <table> <tbody> <tr><td> <strong>Action</strong> </td> <td> <strong>Description</strong> </td> </tr> <tr> <td> Lire l'entité </td> <td> Lire l'objet de l'entité. </td> </tr> <tr> <td> Créer ou mettre à jour une entité </td> <td> Créer ou mettre à jour l'objet de l'entité. L’objet est mis à jour. S’il existe déjà, un autre objet est créé. </td> </tr> <tr> <td> Supprimer </td> <td> Cette action supprime l'objet spécifié de l'entité sélectionnée. </td> </tr> <tr> <td> Interroger </td> <td> L'opération d'interrogation permet de créer une requête concernant une entité. </td> </tr> </tbody> </table>

##Créer une application API du connecteur QuickBooks##
1.	Ouvrez Azure Marketplace à l’aide de l’option « + NOUVEAU » en bas à droite du portail Azure.
2.	Accédez à « Web et mobilité > API Apps » et recherchez « QuickBooks ».
3.	Configurez le connecteur QuickBooks en indiquant les détails du plan d’hébergement, le groupe de ressources et en sélectionnant le nom de l’application API.

	![][13]
4. Configurez les entités QuickBooks que vous souhaitez lire/écrire dans « Paramètres du package ».

Vous pouvez maintenant créer une application API du connecteur QuickBooks.


##Créer une application logique##
Créons une application logique simple qui crée un compte dans QuickBooks et mettons à jour le « Type de catégorie » de ce compte.

1.	Connectez-vous au portail Azure et cliquez sur Nouveau -> Web + mobile -> Application logique.

	![][1]

2.	Dans la page Créer une application logique, fournissez les informations requises, telles que le nom, le plan de service de l'application et l'emplacement.

	![][2]

3.	Cliquez sur Déclencheurs et actions. L'écran de l'éditeur d'application logique s'affiche.

	![][3]

4.	Sélectionnez Exécuter cette logique manuellement, ce qui signifie que cette application logique ne peut être appelée que manuellement.


5.	Développez « Applications API dans ce groupe de ressources » dans la galerie pour afficher toutes les applications API disponibles. Sélectionnez « Connecteur QuickBooks » dans la galerie. Le « Connecteur QuickBooks » est ajouté au flux.


6.	Vous devez vous authentifier et autoriser les applications logiques pour effectuer des opérations en votre nom si QuickBooks est en ligne. Pour lancer l’autorisation, cliquez sur Autoriser dans le connecteur QuickBooks.

	![][4]

7.	Le fait de cliquer sur ce bouton ouvre la boîte de dialogue d’authentification de QuickBooks. Indiquez les identifiants de connexion du compte QuickBooks sur lequel vous souhaitez effectuer les opérations.

	![][5]

8. Autorisez les applications logiques à accéder à votre compte et à effectuer l’opération en votre nom en cliquant sur Autoriser dans la boîte de dialogue de consentement.

	![][6]

9.	Une fois que l'autorisation est terminée, toutes les actions s'affichent.

	![][7]

10.	Sélectionnez l'action « Créer ou mettre à jour le compte ». Les paramètres d'entrée s'affichent.

	![][8]

11.	Renseignez les champs « Nom » et « Type de compte », puis cliquez sur ✓.

	![][9]

12.	Sélectionnez « Connecteur QuickBooks » dans la section « Récemment utilisé » de la galerie. Une action QuickBooks est ajoutée.

13.	Sélectionnez « Créer ou mettre à jour le compte » dans la liste des actions. Les paramètres d'entrée de l'action s'affichent.

	![][10]

14.	Cliquez sur « + » en regard de « ID » pour sélectionner la valeur de l'ID dans la sortie de l'action Créer le compte.

	![][11]

15.	Indiquez les nouvelles valeurs dans Type de compte et cliquez sur ✓.

	![][12]

16. Cliquez sur OK dans l’éditeur d’application logique, puis cliquez sur Créer. Environ 30 secondes sont nécessaires pour terminer la création.

17. Accédez à l’application logique créée et cliquez sur Exécuter pour l’exécuter.

18. Vous pouvez vérifier qu'un compte nommé « Contoso » est créé dans votre compte QuickBooks.

<!--Image references-->
[1]: ./media/app-service-logic-connector-quickbooks/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-quickbooks/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-quickbooks/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-quickbooks/4_QuickBooks_Authorize.png
[5]: ./media/app-service-logic-connector-quickbooks/5_QuickBooks_Login.png
[6]: ./media/app-service-logic-connector-quickbooks/6_QuickBooks_User_Consent.png
[7]: ./media/app-service-logic-connector-quickbooks/7_QuickBooks_Actions.png
[8]: ./media/app-service-logic-connector-quickbooks/8_QuickBooks_Create_Account.png
[9]: ./media/app-service-logic-connector-quickbooks/9_Create_Account_OK.png
[10]: ./media/app-service-logic-connector-quickbooks/10_QuickBooks_Update_Account.png
[11]: ./media/app-service-logic-connector-quickbooks/11_Record_ID_from_Create.png
[12]: ./media/app-service-logic-connector-quickbooks/12_Update_Account_Address.png
[13]: ./media/app-service-logic-connector-quickbooks/13_Create_new_quickbooks_connector.png

<!---HONumber=July15_HO3-->