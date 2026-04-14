# Objectif
Ce projet a pour but d’étudier le marché de la volaille pour une entreprise d’élevage et de vente de poulet en France, en vue d’une éventuelle expansion à l’international.
L’objectif est de proposer des groupes de pays cibles adaptés à cette expansion. Pour cela, l’analyse est organisée en deux grandes parties :
- la préparation et le nettoyage des données utilisées pour l’étude ;
- le regroupement des pays et l’analyse des résultats.
L’ensemble du travail est réalisé en utilisant le langage Python, conformément aux consignes qui prévoient l’usage exclusif de Python ou de R pour cette étude.
## Contexte et description des données
La Poule qui chante est une entreprise française de l’agroalimentaire dont l’activité principale est l’élevage et la vente de poulets sous le label « Poulet Agriculture Biologique ». L’entreprise, aujourd’hui centrée sur le marché national, souhaite se développer à l’international, sans a priori sur une zone géographique particulière (pays ou continent).
Dans ce cadre, il est demandé de réaliser une étude de marché visant à proposer une analyse de groupements de pays cibles pour l’exportation de poulet, comme précisé dans la section Objectif. Les données mobilisées proviennent d’organismes internationaux tels que la FAO (Food and Agriculture Organization), la Banque mondiale et le site Données mondiales, à partir desquels ont été extraits des indicateurs démographiques, économiques et agroalimentaires utiles à l’étude.
Pour faciliter le démarrage du projet, l’entreprise a pré agrégé une première sélection d’indicateurs dans deux fichiers CSV fournis au départ de l’analyse. Ces fichiers ont servi de base, mais ne suffisaient pas à couvrir l’ensemble des besoins de l’étude. Conformément au cahier des charges, des données complémentaires ont donc été récupérées directement auprès de la FAO et de la Banque mondiale, afin d’obtenir plus de huit variables pertinentes au sens de l’approche PESTEL (démographie, économie, production et échanges de volaille, emploi agroalimentaire, etc.) et de constituer un échantillon final de plus de 100 pays à analyser.
## Préparation et nettoyage des données
La préparation et le nettoyage des données ont consisté à sélectionner, harmoniser et consolider des jeux de données issus de différentes sources, afin de constituer une base suffisamment solide pour l’analyse. L’objectif est d’obtenir un DataFrame final cohérent, exploitable pour l’analyse exploratoire, l’ACP puis le clustering.
IV.	Identification des variables numériques et catégorielles
Dans un DataFrame, comme dans la plupart des jeux de données, les variables se divisent en deux grandes catégories : numériques (ou quantitatives) et catégorielles (ou qualitatives) .
Les variables numériques correspondent à des données mesurables exprimées par des nombres, sur lesquelles on peut effectuer des opérations mathématiques (moyenne, écart type, corrélation, etc.) . Elles se subdivisent en :
-	variables continues (par exemple : PIB, disponibilité alimentaire, production, importations, exportations) ;
-	variables discrètes (par exemple : population ou nombre d’habitants, lorsqu’il est compté en unités entières).
Les variables catégorielles représentent des qualités ou catégories non numériques, comme les noms de pays, les codes ISO, les continents ou les identifiants de cluster ; elles servent surtout à identifier, filtrer ou regrouper les observations, sans calcul direct de moyennes ou de corrélations .
Dans le DataFrame final de cette étude, les variables ont été séparées en deux ensembles :
les variables numériques, stockées dans la matrice XX, qui servent de base aux traitements quantitatifs (standardisation, ACP, clustering, calculs de corrélations, etc.) ;
les variables catégorielles, conservées pour l’identification des observations (pays, année) et pour l’interprétation des résultats (appartenance à un cluster, continent, etc.), mais non utilisées directement dans l’ACP.
## Détection et traitement des outliers
Les outliers (ou valeurs aberrantes) désignent des valeurs extrêmes dans une variable, qui s'écartent significativement de la distribution générale des données. Elles peuvent être atypiques (extrêmes mais acceptables dans le contexte métier) ou aberrantes (inacceptables, souvent dues à des erreurs de saisie ou de mesure).
Plusieurs méthodes permettent leur détection, parmi lesquelles :
•	la méthode IQR (Interquartile Range), qui identifie les valeurs en dehors de l'intervalle [Q1−1.5×IQR;Q3+1.5×IQR][Q1−1.5×IQR;Q3+1.5×IQR] ;
•	le Z-score, qui repère les valeurs à plus de 3 écarts-types de la moyenne ;
•	les visualisations comme les boîtes à moustaches (boxplots).
Dans cette étude, la détection des outliers a été réalisée à l’aide de :
•	la méthode IQR (Interquartile Range), en repérant les valeurs situées en dehors de l’intervalle [Q1−1,5×IQR;Q3+1,5×IQR][Q1−1,5×IQR;Q3+1,5×IQR] ;
•	l’analyse visuelle des boxplots pour chaque variable et chaque cluster, afin d’identifier les pays présentant des valeurs très éloignées du reste de la distribution.
Après examen détaillé, la plupart des valeurs extrêmes observées correspondaient à des situations réellement atypiques mais cohérentes avec le contexte métier (très grands pays, fortes productions, marchés fortement importateurs, etc.), et non à des erreurs manifestes de données. Afin de préserver cette information et de ne pas biaiser la structure des données, il a donc été décidé de ne pas corriger ni supprimer ces outliers, mais de les conserver pour l’ACP et le clustering tout en les gardant à l’esprit lors de l’interprétation des résultats.
## Analyse des séries temporelles et propriétés des variables numériques
L’analyse exploratoire des variables numériques vise à examiner leur distribution, à détecter d’éventuelles asymétries ou anomalies, et à étudier les relations entre variables avant de lancer l’ACP et le clustering. Pour cela, plusieurs outils ont été mobilisés :
•	des histogrammes avec estimation de densité (KDE), afin de visualiser la forme de la distribution (asymétrie, concentrations, queues longues) ;
•	des QQ plots (quantile quantile), pour comparer la distribution empirique de chaque variable à une loi normale théorique et évaluer la normalité ;
•	une matrice de corrélation, pour quantifier les relations entre paires de variables et repérer redondances ou complémentarités.
Ces méthodes ont été appliquées aux principales variables numériques (population, PIB, production et commerce de volaille, disponibilités, etc.), sur la période retenue (données FAO 2017 principalement). Les histogrammes et KDE ont mis en évidence des distributions souvent très asymétriques, en particulier pour les indicateurs économiques (PIB, production, importations, exportations), ce qui a été confirmé par des QQ plots s’écartant nettement de la droite de normalité. Ces constats justifient l’utilisation d’une standardisation préalable des variables avant l’ACP et le k means, afin de rendre les échelles comparables.
La forte non normalité observée (asymétries marquées, queues longues) a conduit à privilégier, pour la matrice de corrélation, le coefficient de Spearman, basé sur les rangs plutôt que sur les valeurs brutes. Cette approche fournit une mesure de dépendance plus robuste que la corrélation de Pearson en présence d’outliers et de distributions non gaussiennes, et limite ainsi leur impact sur l’évaluation des relations entre variables.
La matrice de corrélation a montré des liens importants entre certaines variables, par exemple entre population, production et disponibilités, révélant une multicolinéarité partielle dans les données. Cette information a été prise en compte pour la sélection finale des variables intégrées à l’ACP, de manière à limiter les redondances tout en conservant les dimensions les plus structurantes du marché avicole international.
## Analyse multivariée
L’analyse multivariée regroupe les méthodes qui étudient simultanément plusieurs variables afin de mettre en évidence des structures globales dans les données (axes principaux, groupes homogènes, relations entre variables). Elle complète l’analyse univariée et bivariée en offrant une vision synthétique de l’information.
Dans cette étude, l’analyse multivariée a mobilisé deux méthodes de classification, la classification ascendante hiérarchique (CAH) et le k means, afin de comparer leurs performances et de retenir la solution la plus pertinente pour le clustering des pays.
Dans un premier temps, une CAH a été appliquée sur les variables standardisées. À partir du dendrogramme, un nombre plausible de clusters a été sélectionné, puis évalué à l’aide de scores de qualité internes (indice de silhouette, indice de Calinski Harabasz, indice de Davies Bouldin, etc.).
Ensuite, un clustering k means a été réalisé sur les mêmes variables que celles utilisées pour la CAH. Le nombre de clusters a été déterminé à partir de la méthode du coude sur la courbe d’inertie, puis les mêmes indices de qualité (silhouette, Calinski Harabasz, Davies Bouldin) ont été calculés et comparés à ceux obtenus avec la partition hiérarchique.
La comparaison des scores obtenus a montré que le k means produisait des groupes plus compacts et mieux séparés que la CAH. La segmentation finale des pays repose donc sur le k means, tandis que la CAH a servi de point de référence pour valider le choix de méthode de clustering.
Enfin, une Analyse en composantes principales (ACP) a été réalisée pour projeter les pays et les centres de clusters sur des plans factoriels, interpréter les axes à l’aide du cercle de corrélation et caractériser visuellement les profils de chaque groupe
### Classification ascendante hiérarchique
La Classification Ascendante Hiérarchique (CAH) est une méthode non supervisée qui regroupe les observations en une hiérarchie de clusters par fusion successive, en minimisant la distance entre les éléments les plus proches à chaque étape.
Dans cette étude, la CAH a été appliquée sur les variables standardisées sélectionnées (croissance démographique, PIB par habitant, production/exportations de volaille, etc.). La méthode de Ward a été utilisée pour sa capacité à produire des groupes compacts en minimisant l'augmentation de l'inertie intra-classe lors des fusions.
Le dendrogramme obtenu a permis de visualiser la structure hiérarchique et de déterminer le nombre de clusters à étudier : 7 clusters. Les partitions ont ensuite été affichées avec le nombre exact de pays qu'elles contenaient. Cela a révélé l'existence de clusters avec un ou deux pays seulement, identifiant ainsi des pays extrêmes ou outliers.
Une suppression de ces pays a été effectuée, suivie d'une réapplication de la CAH pour le même nombre de clusters. Les nouvelles partitions ont été évaluées avec les indices de qualité suivants :
•	Indice de silhouette : mesure la cohésion intra-groupe et la séparation inter-groupes (valeur idéale proche de 1).
•	Indice de Calinski-Harabasz : ratio variance inter/variance intra-classe (plus élevé = meilleur).
•	Indice de Davies-Bouldin : rapport des dispersions intra aux distances inter (plus bas = meilleur).
Les résultats ont montré des scores corrects mais inférieurs à ceux du k-means, confirmant son rôle de méthode de référence pour valider la segmentation finale.
 
 
 
### K-means
Le k-means est une méthode de clustering non supervisée qui partitionne les observations en k clusters préalablement définis, en minimisant l’inertie intra-classe, c’est-à-dire la somme des carrés des distances entre chaque observation et le centroïde de son cluster.
Dans cette étude, le k-means a été appliqué sur les mêmes variables standardisées que pour la CAH (croissance démographique, PIB par habitant, production/exportations de volaille, etc.), après suppression des outliers identifiés lors de l’analyse hiérarchique. Plusieurs valeurs de k (de 3 à 10) ont été testées à l’aide de la méthode du coude, en observant l’évolution de l’inertie intra-classe en fonction du nombre de clusters. La courbe obtenue présente un infléchissement net autour de k=5, ce qui a conduit à retenir une partition en 5 clusters.
La qualité de cette partition a ensuite été évaluée à l’aide de plusieurs indices internes :
•	Indice de silhouette : mesure la cohésion intra-groupe et la séparation inter-groupes (valeur idéale proche de 1).
•	Indice de Calinski–Harabasz : ratio entre la variance inter-classe et la variance intra-classe (plus élevé = meilleur).
•	Indice de Davies–Bouldin : rapport des dispersions intra-classe aux distances inter-classe (plus bas = meilleur).
Les scores obtenus pour k=5 ont surpassé ceux de la CAH, avec des groupes plus compacts et mieux séparés, ce qui justifie le choix du k-means comme méthode de segmentation finale des pays.
 

 
### Analyse en composantes principales (ACP)
L’Analyse en composantes principales (ACP) est une méthode de réduction de dimension qui transforme un ensemble de variables potentiellement corrélées en un nombre plus réduit de nouvelles variables, appelées composantes principales, décorrélées entre elles et expliquant la plus grande part possible de la variance des données. Elle permet ainsi de résumer l’information et de représenter les données dans un espace de plus faible dimension, tout en conservant l’essentiel de la structure globale.
Dans cette étude, l’ACP a été appliquée aux mêmes variables standardisées que celles utilisées pour le clusteringa prés la suppression des pays outliers, afin de projeter les pays dans un plan factoriel et de faciliter l’interprétation des groupes. 
 
 
Les six premiers axes, qui explique 78 % de la variance totale, ont servi à visualiser les pays et les centres de clusters, tandis que le cercle de corrélation a permis d’identifier les variables les plus contributrices à chaque axe et de caractériser les profils associés aux différents clusters.
 
Le cercle des corrélations ci-dessus représente les axes F1 et F2, qui expliquent respectivement 30,6 % et 14,5 % de l'inertie totale. 
Sur l'axe 1 (F1 - horizontal) : Intensité du marché avicole Les variables fortement corrélées à F1 positif (droite) sont :
- Production, Disponibilité intérieure, Pertes, Importations – Quantité, Exportations – Quantité, Nourriture
Ces variables pointent toutes vers la droite avec des flèches longues, indiquant qu'elles progressent ensemble. F1 distingue donc **les pays avec une forte activité avicole** (production élevée, gros marché intérieur, imports/exports importants, pertes non négligeables) 

Sur l'axe 2 (F2 - vertical) : richesse et Dynamique démographique vs poids agroalimentaire et disponibilité
Les variables fortement corrélées à F2 positif (haut) sont :
- Indicateur_emploi_agroalimentaire_%, Tendance_elevage, Disponibilité_alimentaire
À l'inverse, les variables corrélées à F2 négatif (bas)  sont :
- PIB_USD, var_population (pointe vers le bas).
 
Cette lecture conjointe des axes F1 et F2 permet de distinguer, d’un côté, des pays où la filière avicole est structurée et fortement mobilisée, avec une dynamique de croissance démographique et d’emploi qui augmente la pression sur les disponibilités alimentaires, De l’autre côté, on observe des pays où la filière avicole et la disponibilité alimentaire pèsent davantage dans le poids global du secteur agroalimentaire, mais avec des niveaux de production et de disponibilités intérieures plus limités. Cette opposition éclaire l’interprétation des clusters obtenus par k means.
Enfin, les six axes de l’ACP et les projections des individus correspondantes sont présentés et détaillés dans le notebook de l’étude, ce qui permet d’approfondir l’analyse au delà du seul plan (F1,F2) montré dans ce rapport
## Interprétation des groupes et recommandations
Après application des différentes méthodes de clustering, l’algorithme k means a permis d’identifier 5 groupes de pays présentant des profils proches, chaque cluster regroupant des pays aux caractéristiques socio économiques et agroalimentaires similaires (par exemple, les Bahamas sont dans le même groupe que Bahreïn ou Brunéi). Pour caractériser ces groupes, les centroïdes ont été calculés et les moyennes des variables analysées pour chaque cluster, ce qui permet de dégager un profil moyen par groupe.
Synthèse des profils de clusters
•	Cluster 1 : Petits marchés insulaires ou isolés
Population la plus faible ≈ 436 000 d’habitants, Le PIB est le plus bas du jeu de données (≈ 0,048e9 USD et La distance moyenne est la plus élevée (≈ 10 500 km). Malgré leur petite taille, ces pays affichent une disponibilité alimentaire calorique par habitant record (254 kcal/pers/jour) et une tendance d'élevage positive (0,25)
•	Cluster 2 : Pays en développement à forte intensité sociale.
La population est intermédiaire (≈ 20 millions) avec un PIB par habitant qui reste faible. Ce cluster se distingue par un indicateur d'emploi agroalimentaire très élevé (le tableau affiche une valeur brute de 9,86e9, suggérant une erreur d'unité ou un poids total cumulé très fort). Les importations y sont faibles, indiquant une économie plus fermée ou moins dépendante des flux mondiaux. 
•	Cluster 3 : Grands exportateurs et plateformes d'échanges.
Population moyenne de ≈ 46 millions d'habitants. C'est le cluster des records commerciaux : les Exportations (4,5e8) et la Disponibilité intérieure (1,2e9) y sont massives. Les variations de stocks sont très positives (3,1e7), indiquant une capacité de stockage stratégique importante.
•	Cluster 4 : Géants démographiques et centres de production.
Ce cluster regroupe les pays les plus peuplés (≈ 99 millions d'habitants en moyenne). La production y est la plus élevée (≈ 1,99e9), tout comme la nourriture totale (1,94e9) et les pertes (8,8e7). Ce sont les moteurs du marché mondial de la volaille, situés à une distance moyenne élevée (9 138 km).
•	Cluster 5 : Marchés émergents en forte intensification. 
 Population plus restreinte (≈ 7,5 millions) mais avec un dynamisme technique marqué. Il présente la Tendance_elevage la plus forte de tous les groupes (0,68), signalant une phase d'industrialisation rapide de la filière.  La gestion des flux y est active avec une variation de stock positive (1,5e7) et une distance intermédiaire (3 759 km).
 Pour confirmer ces profils, plusieurs visualisations ont été réalisées : 
-	plan factoriel coloré par clusters  
Le plan factoriel (Dim1, Dim2) coloré par clusters permet de vérifier la stabilité des groupes au delà des deux premiers axes de l’ACP. On observe que les centroïdes (croix jaunes) restent bien séparés, et que les pays d’un même cluster sont globalement regroupés autour de leur centroïde, ce qui confirme la cohérence interne des cinq groupes définis par le k means même sur des dimensions d’inertie plus faibles
-	boxplots des variables par groupe, 
-	 Les boxplots par cluster confirment les profils mis en évidence par l’analyse des centroïdes. Le cluster 1 regroupe des pays à faible population, avec le PIB le plus bas et la disponibilité alimentaire (Kcal/personne/jour) est la plus élevée,  L'indicateur d'emploi agroalimentaire y est particulièrement fort et étalé, ce qui en fait un pilier de l'économie locale malgré des volumes globaux de production quasi nuls Ce sont les pays les plus distants. À l’inverse, le cluster4 correspond à des pays plus peuplés, caractérisés par une forte production, quasi-totalité de la production est destinée à la nourriture intérieure, avec très peu d'exportations par rapport au volume total, Ils subissent les pertes les plus lourdes en raison de l'échelle massive de leur marché

-	Diagrammes en radar des profils moyens 
 
Les diagrammes en radar ci dessus synthétisent le profil moyen de chaque cluster sur l’ensemble des variables. On voit par exemple que le cluster 1 Très marqué sur la disponibilité alimentaire par habitant (pic le plus haut du groupe) et l'indicateur d'emploi agroalimentaire. le PIB et les flux d'échanges y sont les plus faibles du jeu de données. 
Le cluster 5 Pic extrême et unique sur la tendance d'élevage. Il affiche également des valeurs élevées pour le PIB et les variations de stocks.
-	 carte de chaleur des moyennes par cluster  
La carte de chaleur des moyennes standardisées (z scores) par cluster permet de comparer rapidement les niveaux relatifs de chaque variable entre les groupes. Les valeurs rouges indiquent des niveaux supérieurs à la moyenne globale, tandis que les valeurs bleues signalent des niveaux inférieurs. On observe par exemple que le cluster 3 présente des z scores très élevés pour l’Exportations - Quantité, les Importations - Quantité, confirmant son profil d’Acteurs majeurs du commerce international, alors que le cluster 5 affiche des valeurs nettement plus faibles sur la plupart des variables, ce qui correspond à Pays de taille moyenne dont la filière avicole s'industrialise rapidement, représentant un potentiel de croissance technique. Le cluster4 se distingue par des niveaux élevés de population, de production et de pertes, illustrant des Marchés géants (ex: Mexique, Russie) auto-consommateurs avec des enjeux critiques de gestion des pertes massives. Cette visualisation synthétique confirme ainsi la hiérarchie et la différenciation des profils identifiés entre les clusters.
### Recommandations de ciblage
L'analyse statistique identifie le potentiel théorique. Pour un exportateur français, le succès repose sur des leviers métier extrinsèques (géopolitique, culture, logistique) et une vision dynamique (temporelle).
1.	 Levier Réglementaire : Le Bloc UE (Marché Unique)
Justification métier : Absence de droits de douane et normes sanitaires communes. C'est la zone de sécurité maximale pour l'exportation.
Profil	Clusters cibles	Pays clés (selon clusters)	Opportunité Métier
Volume & Flux	Cluster 3	Allemagne, Belgique, Italie, Espagne, Pologne	Marchés de proximité, gros volumes, idéaux pour la RHF (Restauration Hors Foyer).
Modernisation	Cluster 5	Autriche, Danemark, Suède, Tchéquie, Slovaquie	Pays avec une tendance d'élevage en hausse. Opportunité pour l'export de génétique et d'équipements.
2.	Levier Culturel : La Francophonie et DOM-TOM
Justification métier : Facilité de négociation, proximité des goûts alimentaires et liens historiques.
Profil	Clusters cibles	Pays/Territoires clés	Opportunité Métier
Interne/Spécifique	Cluster 1	Martinique, Guadeloupe, Réunion, Guyane	Marchés captifs avec une consommation par habitant record. Logistique de continuité territoriale.
Émergence	Cluster 2	Sénégal, Cameroun, Côte d'Ivoire, Bénin	Forte croissance démographique. Forte image du produit français, mais production locale encore faible.
Relais	Cluster 5	Maroc, Tunisie, Nouvelle-Calédonie	Marchés plus stables avec une demande pour la qualité et la certification.
3.	 Levier de Richesse : Les Marchés à Fort Pouvoir d'Achat (Hors UE)
Justification métier : Pays dépendant des importations pour leur sécurité alimentaire, capables de payer des marges élevées.
Profil	Clusters cibles	Pays clés	Opportunité Métier
Premium / Halal	Cluster 3	Arabie saoudite	Le géant du Golfe. Importations massives, demande pour le congelé certifié.
Premium Niche	Cluster 5	Émirats arabes unis, Qatar, Koweït, Corée du Sud	Fort PIB, forte demande pour des produits à haute valeur ajoutée (découpes spécifiques).

4.	Proposition d'analyse temporelle : La "Méthode des Horizons"
Pour transformer ces données statiques en stratégie, nous proposons d'analyser les données sous trois angles temporels :
1.	Court Terme (Saisonnalité des flux) :
•	Cibles : Cluster 3.
•	Méthode : Analyser les importations mensuelles. Identifier les périodes de fêtes (Ramadan pour l'Arabie Saoudite, Noël pour l'Europe) pour anticiper la demande.
2.	Moyen Terme (Transition Protéique) :
•	Cibles : Cluster 2 (Afrique/Asie émergente).
•	Méthode : Croiser la hausse du PIB/habitant avec la consommation de poulet. Le passage d'un régime "céréalier" à "protéiné" marque le moment idéal pour entrer sur le marché.
3.	Long Terme (Évolution de l'Offre) :
•	Cibles : Cluster 5 (Australie, Chili, etc.).
•	Méthode : Surveiller la variable Tendance_elevage. Si la production locale dépasse la croissance de la population, ces pays passeront de "clients" à "concurrents".
________________________________________
5.	Recommandations stratégiques finales
Stratégie	Horizon	Clusters Prioritaires	Risque principal
Flux Massifs	2024-2025	Cluster 3 (Allemagne, Belgique)	Concurrence de la Pologne/Ukraine
Niches Luxe	2024-2026	Cluster 5 (Qatar, Émirats, Corée)	Coûts logistiques élevés
Pari Futur	2027+	Cluster 2 (Sénégal, Côte d'Ivoire)	Instabilité monétaire et politique
.

## Conclusion :
Ce travail a permis de construire un jeu de données cohérent à partir de sources internationales, de le nettoyer puis de le structurer autour d’indicateurs démographiques, économiques, agroalimentaires et PESTEL, afin d’évaluer le potentiel d’export de volaille des différents pays. L’analyse descriptive puis multivariée (CAH, k means et ACP) a conduit à une segmentation en cinq groupes de pays aux profils contrastés, allant de petits marchés éloignés à faibles volumes jusqu’à de grands marchés fortement structurants pour la filière avicole. La comparaison des méthodes de clustering a montré la meilleure performance du k means, qui a servi de base à la typologie finale et aux recommandations de ciblage, en identifiant notamment trois clusters prioritaires pour l’expansion commerciale. Enfin, malgré certaines limites liées à la qualité et à l’actualité des données, cette approche fournit un cadre d’aide à la décision reproductible et extensible à d’autres produits ou zones géographiques.
