Certaines réponses Bing incluent des URL vers les images miniatures fournies par Bing. Vous pouvez redimensionner et rogner les images miniatures. Pour redimensionner une image, insérez les paramètres de requête w (largeur) et h (hauteur) dans l’URL de la miniature. Spécifiez la largeur et la hauteur en pixels. Par exemple :  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Si vous redimensionnez l’image, ses proportions sont conservées. Pour conserver les proportions, vous pouvez ajouter une marge intérieure blanche à la bordure de l’image. Exemple : si vous redimensionnez une image de 480x359 sur 200x200 sans la rogner, la largeur totale contient l’image, mais la hauteur contient une marge intérieure blanche de 25 pixels en haut et en bas de l’image. Il en serait de même si l’image avait une taille de 359x480, mais les bordures gauche et droite contiendraient une marge intérieure blanche. Si vous rognez l’image, aucune marge intérieure blanche n’est ajoutée.  
  
L’illustration suivante présente la taille d’origine d’une image miniature (480x300).  
  
![Image d’origine en mode Paysage](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
L’illustration suivante présente l’image redimensionnée sur 200x200. Les proportions sont conservées, et les bordures supérieure et inférieure sont remplies de blanc (la bordure noire est incluse afin de montrer la marge intérieure).  
  
![Image redimensionnée en mode Paysage](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  
  
Si vous spécifiez des dimensions qui sont supérieures à la largeur et à la hauteur d’origine de l’image, l’image est remplie de blanc dans les bordures gauche et supérieure.  
  
Pour rogner une image, incluez le paramètre de requête c (rogner). Vous pouvez spécifier les valeurs indiquées ci-dessous.  
  
- 4&mdash;Blind Ratio (Ratio aveugle)  
- 7&mdash;Smart Ratio (Ratio intelligent)  
  
Si vous demandez un rognage Smart Ratio (Ratio intelligent) (c=7), l’image est rognée à partir du centre de la région d’intérêt de l’image vers l’extérieur tout en conservant les proportions de l’image. La région d’intérêt est la zone de l’image qui, d’après Bing, contient les parties les plus importantes. L’illustration suivante présente un exemple de région d’intérêt :  
  
![Région d’intérêt](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Si vous redimensionnez une image et demandez un rognage Smart Ratio (Ratio intelligent), l’image est réduite jusqu’à la taille demandée, tout en conservant ses proportions. L’image est ensuite rognée en fonction des nouvelles dimensions. Exemple : si la largeur redimensionnée est inférieure ou égale à la hauteur, l’image est rognée à gauche et à droite du centre de la région d’intérêt. Dans le cas contraire, l’image est rognée en haut et en bas du centre de la région d’intérêt.  
  
L’illustration suivante présente l’image réduite à une taille de 200x200 à l’aide du rognage Smart Ratio (Ratio intelligent).  
  
![Image en mode Paysage rognée à 200x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
L’illustration suivante présente l’image réduite à une taille de 200x100 à l’aide du rognage Smart Ratio (Ratio intelligent).  
   
![Image en mode Paysage rognée à 200x100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
L’illustration suivante présente l’image réduite à une taille de 100x200 à l’aide du rognage Smart Ratio (Ratio intelligent).  
  
![Image en mode Paysage rognée à 100x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)
  
Si Bing ne peut pas déterminer la région d’intérêt de l’image, l’application utilise le rognage Blind Ratio (Ratio aveugle).  
  
Si vous demandez le rognage Blind Ratio (Ratio aveugle) (c=4), Bing utilise les règles ci-dessous pour rogner l’image.  
  
- Si (largeur d’image d’origine/hauteur d’image d’origine) < (largeur d’image demandée/hauteur d’image demandée), l’image est mesurée à partir du coin supérieur gauche et rognée en bas.  
- Si (largeur d’image d’origine/hauteur d’image d’origine) > (largeur d’image demandée/hauteur d’image demandée), l’image est mesurée à partir du centre et rognée à gauche et à droite.  
  
L’illustration suivante présente une image en mode Portrait de 225x300.  
  
![Image d’origine de tournesol](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
L’illustration suivante présente l’image réduite à une taille de 200x200 à l’aide du rognage Blind Ratio (Ratio aveugle). L’image est mesurée à partir du coin supérieur gauche, ce qui entraîne le rognage de sa partie inférieure.  
  
![Image de tournesol rognée à 200x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
L’illustration suivante présente l’image réduite à une taille de 200x100 à l’aide du rognage Blind Ratio (Ratio aveugle). L’image est mesurée à partir du coin supérieur gauche, ce qui entraîne le rognage de sa partie inférieure.  
  
![Image de tournesol rognée à 200x100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
L’illustration suivante présente l’image réduite à une taille de 100x200 à l’aide du rognage Blind Ratio (Ratio aveugle). L’image est mesurée à partir du centre, ce qui entraîne le rognage de ses parties gauche et droite.  
  
![Image de tournesol rognée à 100x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)
