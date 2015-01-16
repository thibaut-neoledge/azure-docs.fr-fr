<properties title="Azure Machine Learning Sample: Color quantization using K-Means clustering" pageTitle="Exemple sous Machine Learning : quantification des couleurs à l'aide de l'algorithme des k-moyennes | Azure" description="Un exemple d'expérience Azure Machine Learning qui évalue l'utilisation de différentes valeurs de clustering K-Means pour quantifier une image en couleurs." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Exemple sous Azure Machine Learning : quantification des couleurs à l'aide de l'algorithme des k-moyennes

>[AZURE.NOTE]
>L'[exemple d'expérience] et l'[exemple de jeu de données] associés à ce modèle sont disponibles dans ML Studio. Pour plus d'informations, consultez la section ci-dessous.
[Exemple d'expérience]: #exemple-expérience
[Exemple de jeu de données]: #exemple-jeudedonnées


##Description du problème

[La quantification des couleurs](http://en.wikipedia.org/wiki/Color_quantization "Color quantization") est le processus de réduction du nombre de couleurs distinctes dans une image, ce qui la compresse. Normalement, l'objectif est de conserver autant que possible l'apparence des couleurs de l'image, tout en réduisant le nombre de couleurs, que ce soit pour respecter des limites de mémoire ou de compression. 

##Données

Dans cet exemple d'expérience, imaginons que nous ayons une image RVB 24 bits avec 256 x 256 x 256 couleurs possibles. Bien sûr, nous pouvons construire des histogrammes de couleurs standard en fonction de ces valeurs d'intensité. Cependant, une autre approche consiste à quantifier l'image et à *réduire* le nombre de couleurs à 16 ou 64. Cela crée un espace plus petit et, dans l'idéal, moins de bruit et de variations. Pour cela, nous avons passé les données des pixels (chaque pixel en tant qu'une ligne du jeu de données) dans notre module d'algorithme des k-moyennes. 

##Modèle

Le modèle est créé comme indiqué dans l'image ci-dessous :

![Model][image1]

Nous avons exécuté l'algorithme des k-moyennes, où K=10 à 500 dans 5 branches différentes. Tout d'abord, nous avons calculé les clusters, puis avons rassemblé l'algorithme à la moyenne de leurs couleurs de pixels (à l'aide d'un script R). 
Enfin, nous avons associé chaque pixel à la couleur de cluster regroupé et avons envoyé la nouvelle image au format CSV. Pendant ce temps, nous avons également calculé la différence moyenne quadratique des nouvelles couleurs de pixel avec l'image d'origine et nous les avons affichées sous forme de graphique R (à l'aide du module Exécuter un script R). 

##Résultats

Nous avons testé le résultat sur plusieurs clusters (couleurs) différents, comme illustré dans le modèle d'expérience ci-dessous. Comme vous pouvez le voir ci-dessous, plus il y a d'algorithmes, plus la qualité des images est élevée avec moins de compression :

||
------------ | ---------
**Image originale** | ![Original][image2a]
**K=10**     | ![K=10][image2b]
**K=20**     | ![K=20][image2c]
**K=50**     | ![K=50][image2d]
**K=100**    | ![K=100][image2e]
**K=500**    | ![K=500][image2f]


Nous avons également mesuré l'exactitude à l'aide de la différence moyenne quadratique sur les couleurs de l'image d'origine. Cela est visible sur le deuxième port de sortie du module " Exécuter un script R " :

![Output of Execute R Script module][image3]

Comme on peut le voir, plus il y a de clusters de couleurs, plus les couleurs correspondent aux images d'origine (meilleure qualité). 

##Code pour convertir les images en CSV et vice-versa

Afin de passer les images dans ML Studio, nous avons écrit un code de conversion simple qui permet de convertir les fichiers image au format CSV, utilisable par ML Studio. C'est également un format qui permet de reconvertir le fichier en image. N'hésitez pas à utiliser le code suivant. Nous comptons prochainement ajouter un module permettant également de lire dans les images. 

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Drawing;
	using System.Drawing.Imaging;
	using System.IO;
	 
	namespace Text2Image
	{
	    class Program
	    {
	        static void img2csv(string img_path)
	        {
	            FileInfo img_info = new FileInfo(img_path);
	            string destination_file_directory = img_info.DirectoryName + "\\";
	            string destination_file_name = img_info.Name.Remove(img_info.Name.LastIndexOf('.'), 4);
	            string destination_file_path = destination_file_directory + destination_file_name + ".csv";
	 
	            // Read the image
	            Bitmap img = new Bitmap(img_path);
	 
	            // Create the CSV File and write the header values
	            System.IO.StreamWriter file = new System.IO.StreamWriter(destination_file_path);
	            file.WriteLine("X,Y,R,G,B");
	 
	            // Write the Pixel values
	            for (int x = 0; x < img.Width; x++)
	                for (int y = 0; y < img.Height; y++)
	                {
	                    string line = x + "," + y + "," + img.GetPixel(x, y).R + "," + img.GetPixel(x, y).G + "," + img.GetPixel(x, y).B ;
	                    file.WriteLine(line);
	                }
	 
	            file.Close();
	        }
	 
	        static void csv2img(string csv_path)
	        {
	            FileInfo csv_info = new FileInfo(csv_path);
	            string destination_file_directory = csv_info.DirectoryName + "\\";
	            string destination_file_name = csv_info.Name.Remove(csv_info.Name.LastIndexOf('.'), 4);
	            string destination_file_path = destination_file_directory + destination_file_name + ".png";
	            
	            // Read all the lines in the CSV file
	            string[] lines = System.IO.File.ReadAllLines(csv_path);
	 
	            // set a new bitmap image with the provided width and height in the header
	            string[] wh = lines.Last().Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
	            int img_width = Convert.ToInt32(wh[0])+1;
	            int img_height = Convert.ToInt32(wh[1])+1;
	 
	            Bitmap bmp_img = new Bitmap(img_width, img_height);
	 
	            for (int i = 1; i < lines.Length ;i++ )
	            {
	                string[] values = lines[i].Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
	                if (values.Length < 3)
	                    continue;
	 
	                int x = Convert.ToInt16(values[0]);
	                int y = Convert.ToInt32(values[1]);
	                int r = Convert.ToInt32(values[2]);
	                int g = Convert.ToInt32(values[3]);
	                int b = Convert.ToInt32(values[4]);
	 
	                bmp_img.SetPixel(x, y, Color.FromArgb(r, g, b));
	            }
	 
	            bmp_img.Save(destination_file_path);
	        }
	 
	        static void Main(string[] args)
	        {
	            string source_path = args[1];
	            FileInfo source_info = new FileInfo(source_path);
	 
	            if (source_info.Extension == ".csv")
	                csv2img(source_path);
	            else
	                img2csv(source_path);
	        }
	    }
	}


## Exemple d'expérience

L'exemple d'expérience suivant associé à ce modèle est disponible dans ML Studio dans la rubrique **EXPÉRIENCES** de l'onglet **EXEMPLES**.

> **Exemple d'expérience - Compression d'image via les couleurs à l'aide de l'algorithme des k-moyennes - Développement**


## Exemple de jeu de données

L'exemple de jeu de données suivant utilisé dans cette expérience est disponible dans ML Studio dans la palette de module sous **Jeux de données enregistrés**.

###Image RVB de Bill Gates
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]



[image1]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image1.png
[image2a]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg
[image2b]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png
[image2c]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png
[image2d]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png
[image2e]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png
[image2f]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png
[image3]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image3.png

