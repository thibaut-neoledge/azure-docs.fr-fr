Pour pouvoir stocker des données d'application dans le nouveau service mobile, vous devez d'abord créer une table dans l'instance de base de données SQL associée.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Cliquez sur l'onglet **Data**, puis sur **+Create**.
    
   	![mobile-data-tab-empty](./media/mobile-services-create-new-service-data-2/mobile-data-tab-empty.png)
        
   	La boîte de dialogue **Create new table** s’affiche.

3.  Dans **Table name**, saisissez *TodoItem*, puis cliquez sur le
    bouton de vérification.

 	![mobile-create-todoitem-table](./media/mobile-services-create-new-service-data-2/mobile-create-todoitem-table.png) 

  Une nouvelle table de stockage **TodoItem** est créée avec les autorisations par défaut. Cela signifie que quiconque possédant la clé de l'application, qui est distribuée avec l'application, peut accéder aux données de la table et les modifier.

> [WACOM.NOTE] 
> Le même nom de table est utilisé dans le démarrage rapide avec Mobile Services. Toutefois, chaque table est créée dans un schéma spécifique pour un service mobile donné. Cela vise à éviter les collisions de données lorsque plusieurs services mobiles utilisent la même base de données.

1.  Cliquez sur la nouvelle table **TodoItem** et vérifiez qu'aucune ligne de données n'est présente.

2.  Cliquez sur l'onglet **Colonnes**. Vérifiez si les colonnes par défaut suivantes ont été automatiquement créées pour vous :
    
    <table  border="1" cellpadding="10">
     	<tr>
     	<th>Nom de la colonne</th>
    
     	<th>Type</th>
    
     	<th>Index</th>
    
     	</tr>
    
     	<tr>
     	<td>id</td>
    
     	<td>string</td>
    
     	<td>Indexé</td>
    
     	</tr>
    
     	<tr>
     	<td>__createdAt</td>
    
     	<td>date</td>
    
     	<td>Indexé</td>
    
     	</tr>
    
     	<tr>
     	<td>__updatedAt</td>
    
     	<td>date</td>
    
     	<td><font  color="transparent">-</font>
    </td>
    
     	</tr>
    
     	<tr>
     	<td>__version</td>
    
     	<td>timestamp (MSSQL)</td>
    
     	<td><font  color="transparent">-</font>
    </td>
    
     	</tr>
     	
     	</table>

 	Cela correspond à la configuration minimale requise pour une table dans Mobile Services.	  
 
 	<div class="dev-callout"><b>Note</b>
 	<p>Lorsque le schéma dynamique est activé sur votre service mobile, de nouvelles colonnes sont créées automatiquement lorsque des objets JSON sont envoyés au service mobile par une opération d’insertion ou de mise à jour.</p>
 	</div>


Vous pouvez maintenant utiliser le nouveau service mobile en tant que stockage des données pour l'application.