# StarCraft-2-Player-Pr-diction-Challenge
Dans ce projet, nous explorerons l'identification des joueurs dans StarCraft 2 en analysant leurs actions. Nous développerons un modèle prédictif d'apprentissage automatique pour lier les comportements observés à l'identité des joueurs.

hello c'est moi

importer  des pandas  en tant que  PD
importer  un fichier CSV
de  Sklearn . model_selection  importer  train_test_split
de  Sklearn . importation d'ensemble  RandomForestClassifier 
de  Sklearn . importation de métriques  précision_score 
de  Sklearn . prétraitement  importation  LabelEncoder

# Chemins des fichiers de données
test  =  'TEST.CSV'
train  =  'TRAIN.CSV'
test_long  =  'TEST_LONG.CSV'
train_long  =  'TRAIN_LONG.CSV'
sample_sub  =  'SAMPLE_SUBMISSION.CSV'

# Fonction pour former les fichiers d'entraînement
def  formatTrain ( fichier ):
    profile  = []   # Stocker les profils des joueurs
    avatar  = []    # Stocker les avatars des joueurs
    move  = []     # Stocker les mouvements des joueurs
    avec  open ( file ) comme  fichier_csv :
        csv_reader  =  csv . lecteur ( fichier_csv , délimiteur = ',' , quotechar = "'" )
        pour  la ligne  dans  csv_reader :
            profil . append ( row [ 0 ])   # Ajoute le profil du joueur
            avatar . append ( row [ 1 ])    # Ajoute l'avatar du joueur
            se déplace . append ( row [ 2 :])    # Ajoute les mouvements du joueur
    # Crée un DataFrame à partir des données collectées
    d  = { 'Profil' : profil , 'Avatar' : avatar , 'Moves' : mouvements }
    df  =  pd . DataFrame ( données = d )
    retour  df

# Fonction pour former les fichiers de test
def  formatTest ( fichier ):
    avatar  = []    # Stocker les avatars des joueurs de test
    move  = []     # Stocker les mouvements des joueurs de test
    avec  open ( file ) comme  fichier csv :
        csvreader  =  csv . lecteur ( fichier csv , délimiteur = ',' , quotechar = "'" )
        pour  la ligne  dans  csvreader :
            avatar . append ( row [ 0 ])    # Ajout de l'avatar du joueur de test
            se déplace . append ( row [ 1 :])   # Ajoute les mouvements du joueur de test
    # Crée un DataFrame à partir des données collectées
    d  = { 'Avatar' : avatar , 'Moves' : mouvements }
    df  =  pd . DataFrame ( données = d )
    retour  df

# Nettoyage des données
def  clean_data ( df ):
    # Suppression des valeurs manquantes
    df . dropna ( en place = True )

    # Suppression des doublons basés sur la colonne "Moves"
    df [ 'Moves_str' ] =  df [ 'Moves' ]. appliquer ( lambda  x : ',' . join ( map ( str , x )))
    df . drop_duplicates ( sous-ensemble = [ 'Moves_str' ], inplace = True )
    df . drop ( colonnes = [ 'Moves_str' ], inplace = True )

    # Ajouter d'autres étapes de nettoyage au besoin

    retour  df

# Extraction des caractéristiques
def  extract_features ( df ):
    fonctionnalités  =  pd . DataFrame ( index = df . index )
    fonctionnalités [ 'num_actions' ] =  df [ 'Moves' ]. appliquer ( lambda  x : len ( x ))
    fonctionnalités [ 'unique_actions' ] =  df [ 'Moves' ]. appliquer ( lambda  x : len ( set ( x )))
     fonctionnalités de retour

# Prétraitement des données
def  preprocess_data ( df_train , df_test ):
    # Nettoyage des données
    df_train_cleaned  =  clean_data ( df_train )
    df_test_cleaned  =  clean_data ( df_test )

    # Extraction des caractéristiques
    X_train  =  extract_features ( df_train_cleaned )
    X_test  =  extract_features ( df_test_cleaned )

    # Encodage des colonnes catégorielles
    le_avatar  =  LabelEncoder ()
    X_train [ 'Avatar' ] =  le_avatar . fit_transform ( df_train_cleaned [ 'Avatar' ])
    X_test [ 'Avatar' ] =  le_avatar . transformer ( df_test_cleaned [ 'Avatar' ])

    retourner  X_train , X_test

# Séparation des données en ensembles d'entraînement et de validation
def  split_data ( X_train , y_train ):
    return  train_test_split ( X_train , y_train , test_size = 0.2 , random_state = 42 )

# Entraînement du modèle
def  train_model ( X_train , y_train ):
    modèle  =  RandomForestClassifier ( n_estimators = 100 , random_state = 42 )
    modèle . en forme ( X_train , y_train )
     modèle de retour

# Évaluation du modèle
def  évalue_model ( modèle , X_val , y_val ):
    y_pred  =  modèle . prédire ( X_val )
    retourner  précision_score ( y_val , y_pred )

# Prédiction sur les données de test
def  predict_test_data ( modèle , X_test ):
     modèle de retour . prédire ( X_test )

# Chargement des données formatées
df_train  =  formatTrain ( train )
df_test  =  formatTest ( test )

# Prétraitement des données
X_train , X_test  =  preprocess_data ( df_train , df_test )

# Encodage des étiquettes des données d'entraînement
le_profile  =  LabelEncoder ()
y_train  =  le_profile . fit_transform ( df_train [ 'Profil' ])

# Séparation des données d'entraînement en ensembles d'entraînement et de validation
X_train_split , X_val , y_train_split , y_val  =  split_data ( X_train , y_train )

# Entraînement du modèle
modèle  =  train_model ( X_train_split , y_train_split )

# Évaluation du modèle
précision  =  évaluer_modèle ( modèle , X_val , y_val )
print ( "Précision du modèle sur l'ensemble de validation :" , précision )

# Prédiction sur les données de test
test_pred  =  prédict_test_data ( modèle , X_test )

# Conversion des prédictions en noms de joueurs
test_pred_player_ids  =  le_profile . transformation_inverse ( test_pred )

# Création du fichier de soumission
soumission  =  pd . DataFrame ({ 'prédiction' : test_pred_player_ids })
soumission . to_csv ( 'SUBMISSION.CSV' , index = False )
