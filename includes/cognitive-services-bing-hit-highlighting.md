Bing prend en charge la mise en surbrillance des correspondances qui marque les termes de requête (ou d’autres termes que Bing estime pertinents) dans les chaînes d’affichage de certaines des réponses. Par exemple, les champs `name`, `displayUrl` et `snippet` d’une page web peuvent marquer les termes de requête.

Par défaut, Bing n’inclut pas les marqueurs de mise en surbrillance dans les chaînes d’affichage. Pour inclure les marqueurs, ajoutez le paramètre de requête `textDecorations` dans votre demande et définissez-le sur **true**. Bing marque les termes de requête en utilisant des caractères Unicode E000 et E001 pour marquer le début et la fin du terme. Par exemple, si le terme de requête est Sailing Dinghy et si l’un des deux termes existe dans le champ, le terme est placé entre les caractères de mise en surbrillance des correspondances comme indiqué dans l’exemple suivant :  
  
![Mise en surbrillance des correspondances](./media/cognitive-services-bing-hit-highlighting/bing-hit-highlighting.PNG) 

Avant d’afficher la chaîne dans votre interface utilisateur, vous devez remplacer les caractères Unicode par des caractères appropriés pour votre format d’affichage. Par exemple, si vous affichez le texte au format HTML, vous pouvez mettre en surbrillance le terme de requête en remplaçant E000 par <b\> et E001 par </b\>. Si vous ne souhaitez pas appliquer la mise en forme, supprimez les marqueurs de la chaîne. 

Bing vous offre la possibilité d’utiliser des caractères Unicode ou des balises HTML en tant que marqueurs. Pour spécifier les marqueurs à utiliser, incluez le paramètre de requête `textFormat`. Pour marquer le contenu avec des caractères Unicode, définissez `textFormat` sur Brut (la valeur par défaut), et pour marquer le contenu avec des balises HTML, définissez `textFormat` sur HTML. 
  
Si `textDecorations` est **true**, Bing peut inclure les marqueurs suivants dans les chaînes d’affichage des réponses. S’il n’existe aucun équivalent HTML, la cellule de la table HTML est vide.

|Unicode|HTML|Description
|-|-|-
|U+E000|\<b>|Marque le début du terme de requête (mise en surbrillance des correspondances)
|U+E001|\</b>|Marque la fin du terme de requête
|U+E002|\<i>|Marque le début du contenu en italique 
|U+E003|\</i>|Marque la fin du contenu en italique
|U+E004|\<br/>|Marque un saut de ligne
|U+E005||Marque le début d’un numéro de téléphone
|U+E006||Marque la fin d’un numéro de téléphone
|U+E007||Marque le début d’une adresse
|U+E008||Marque la fin d’une adresse
|U+E009|\&nbsp;|Marque une espace insécable
|U+E00C|\<strong>|Marque le début du contenu en gras
|U+E00D|\</strong>|Marque la fin du contenu en gras
|U+E00E||Marque le début du contenu dont l’arrière-plan doit être plus clair que son arrière-plan qui l’entoure
|U+E00F||Marque la fin du contenu dont l’arrière-plan doit être plus clair que son arrière-plan qui l’entoure
|U+E010||Marque le début du contenu dont l’arrière-plan doit être plus foncé que son arrière-plan qui l’entoure
|U+E011||Marque la fin du contenu dont l’arrière-plan doit être plus foncé que son arrière-plan qui l’entoure
|U+E012|\<del>|Marque le début du contenu qui doit être barré
|U+E013|\</del>|Marque la fin du contenu qui doit être barré
|U+E016|\<sub>|Marque le début du contenu en indice
|U+E017|\</sub>|Marque la fin du contenu en indice
|U+E018|\<sup>|Marque le début du contenu en exposant
|U+E019|\</sup>|Marque la fin du contenu en exposant

L’exemple suivant illustre une réponse `Computation` contenant des marqueurs de type indice pour un terme de requête log(2). Le champ `expression` contient les marqueurs uniquement si textDecoration est **true**.

![marqueurs de calcul](./media/cognitive-services-bing-hit-highlighting/bing-markers-computation.PNG) 

Si la demande n’a pas fait appel à des décorations, l’expression doit être log10(2). 
  
