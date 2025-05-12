#  API d'Analyse de Sentiments avec Flask, Hugging Face et MySQL

Ce projet est une API Flask permettant d’analyser le sentiment de textes (tweets) à l’aide de deux modèles :

- Un modèle Hugging Face (`distilbert`)
- Un modèle de régression logistique entraîné localement

Les résultats sont enregistrés dans une base de données MySQL. Le tout fonctionne avec Docker pour garantir la portabilité.

---

## 🚀 Lancer le projet avec Docker

### 1. Cloner le dépôt
```bash
git clone https://github.com/votre-utilisateur/algo-api-analyse-sentiments.git
cd algo-api-analyse-sentiments/api-flask
```

### 2. Lancer Docker
Assure-toi que Docker est installé, puis exécute :
```bash
docker-compose up --build
```
Cela va :

Lancer une base de données MySQL (sentiments)

Démarrer l’API Flask sur http://localhost:5001

### 3. Initialisation de la base de données
Une fois les conteneurs démarrés, exécute cette commande dans un nouveau terminal pour créer la table tweets :

```bash
docker exec -it flask-api python3 db/create_table.py
```
✅ Un message Table 'tweets' créée avec succès. doit s'afficher.

#  Entraîner un modèle et générer la matrice de confusion

Un script est disponible pour entraîner un modèle de régression logistique sur les tweets déjà enregistrés dans la base de données et générer une matrice de confusion.

### 1. Depuis le dossier src, exécuter :

```bash
cd ../src
python model_training.py
```

✅ Le modèle s'entraîne automatiquement

✅ Une matrice de confusion est affichée

✅ Une image src/matrice_confusion.png est générée


## Réentraînement Automatique Hebdomadaire
Un script retrain_model.py entraîne le modèle chaque semaine avec les tweets les plus récents de la base.

 Le fichier run_retrain.sh lance ce script automatiquement dans le conteneur Docker.

Le modèle et le vectorizer sont sauvegardés dans le dossier models/.

Cron automatique
Le réentraînement est automatisé chaque lundi à 9h grâce à une tâche cron :
 ```bash
 0 9 * * 1 /Users/youssefalaouielmrani/PycharmProjects/algo-api-analyse-sentiments/algo-api-analyse-sentiments/api-flask/run_retrain.sh >> /Users/youssefalaouielmrani/PycharmProjects/algo-api-analyse-sentiments/algo-api-analyse-sentiments/logs/retrain.log 2>&1

 ```


## À savoir sur le modèle Hugging Face

Le modèle est entraîné sur des textes en anglais uniquement.
👉 Pour obtenir une analyse de sentiment cohérente, les tweets envoyés à l’API doivent donc être en anglais.

```bash
["I love this product", "I want to hurt someone"]  ✅ Compris

["J'adore ce produit", "j’ai envie de te tuer"]   ❌ Mauvaise détection

```


