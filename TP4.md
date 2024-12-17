## Rapport sur CouchDB

### 1. Introduction
CouchDB est une base de données NoSQL open-source développée par Apache Software Foundation. Elle est conçue pour stocker, répliquer et synchroniser des données de manière fiable. CouchDB repose sur une architecture orientée documents (Document-Oriented Database) et utilise JSON pour stocker les données.

---

### 2. Base de Données Documentaire
Contrairement aux bases de données relationnelles traditionnelles (SQL), CouchDB adopte une structure flexible, basée sur des documents JSON. Chaque document est une structure autonome avec des paires **clef-valeur** et un identifiant unique (*id*).

- **Structure JSON** : Les données sont stockées dans un format JSON lisible par les humains.
- **Schema flexible** : Contrairement à une base relationnelle, chaque document peut avoir une structure différente.
- **Documents autonomes** : Les documents ne dépendent pas les uns des autres, ce qui facilite les opérations sur des données distribuées.

#### Exemple d'un document JSON dans CouchDB :
```json
{
  "_id": "001",
  "name": "Alice",
  "age": 25,
  "city": "Paris"
}
```
Chaque document a :
- `_id` : Identifiant unique obligatoire.
- Des paires clef-valeur pour stocker les informations.

---

### 3. Fonctionnement
CouchDB repose sur les principes suivants :
- **RESTful API** : CouchDB expose ses opérations via des requêtes HTTP (GET, POST, PUT, DELETE).
- **Réplication** : Supporte la réplication maître-maître permettant de synchroniser plusieurs instances de bases de données.
- **MapReduce** : CouchDB utilise MapReduce pour effectuer des requêtes complexes et créer des vues indexées.
- **ACID** : CouchDB garantit des propriétés ACID à l’échelle du document.
- **Conflits et résolutions** : CouchDB gère automatiquement les conflits de synchronisation grâce à sa réplication.

#### Vue d'ensemble de l'architecture :
CouchDB fonctionne comme un serveur HTTP où chaque base de données est un ensemble de documents stockés sous forme JSON. Les opérations se font à travers des requêtes HTTP REST, ce qui simplifie l'interaction avec CouchDB sans client spécifique.

---

### 4. MapReduce dans CouchDB
CouchDB utilise MapReduce pour créer des vues. **Map** transforme les données en clé-valeur, tandis que **Reduce** agrège les résultats.

---

#### 4.1 MapReduce en Général  
MapReduce est un paradigme de programmation permettant de traiter de grandes quantités de données de manière distribuée. Il divise le traitement en deux phases principales : **Map** et **Reduce**.

1. **Fonction Map** :  
   - La fonction Map prend en entrée un ensemble de données.  
   - Elle traite chaque élément indépendamment pour produire des paires **clé/valeur**.  
   - Chaque nœud traite une partie des données en parallèle.  

2. **Fonction Reduce** :  
   - La fonction Reduce combine les résultats intermédiaires générés par la fonction Map.  
   - Elle regroupe les données par clé et applique une opération d'agrégation (comme un total, une moyenne, etc.).  

---

#### 4.2 Exemple Général de MapReduce  
Considérons un ensemble de documents représentant des ventes de produits :  
```json
[
  {"produit": "A", "quantite": 10},
  {"produit": "B", "quantite": 5},
  {"produit": "A", "quantite": 7}
]
```

##### 4.2.1 Fonction Map  
La fonction Map extrait les ventes par produit :  
```javascript
function(doc) {
  emit(doc.produit, doc.quantite);
}
```
**Résultat de Map** :  
```
A: 10  
B: 5  
A: 7  
```

##### 4.2.2 Fonction Reduce  
La fonction Reduce additionne les quantités par produit :  
```javascript
function(keys, values) {
  return sum(values);
}
```
**Résultat Final** :  
```
A: 17  
B: 5  
```

---

#### 4.3 MapReduce dans CouchDB  
Dans CouchDB, **MapReduce** est utilisé pour créer des vues indexées. Une vue est composée d'une fonction Map (obligatoire) et d'une fonction Reduce (optionnelle).  

##### 4.3.1 Fonction Map par Défaut  
La fonction Map prend un document en paramètre et traite chaque document indépendamment pour générer des paires clé/valeur. Cette exécution est effectuée de manière parallèle sur chaque nœud dans un cluster CouchDB.  

**Structure d'une Vue** :  
```json
{
  "views": {
    "nom_vue": {
      "map": "function(doc) { emit(doc.cle, doc.valeur); }",
      "reduce": "_sum"
    }
  }
}
```

---

#### 4.4 Exemple 1 : Compter les Produits  
##### 4.4.1 Fonction Map  
```javascript
function(doc) {
  if (doc.produit) {
    emit(doc.produit, 1);
  }
}
```
**Résultat de Map** :  
```
A: 1  
B: 1  
A: 1  
```

##### 4.4.2 Fonction Reduce  
```javascript
function(keys, values) {
  return sum(values);
}
```
**Résultat Final** :  
```
A: 2  
B: 1  
```

---

#### 4.5 Exemple 2 : Total des Quantités par Produit  
##### 4.5.1 Fonction Map  
```javascript
function(doc) {
  if (doc.produit && doc.quantite) {
    emit(doc.produit, doc.quantite);
  }
}
```
**Résultat de Map** :  
```
A: 10  
B: 5  
A: 7  
```

##### 4.5.2 Fonction Reduce  
```javascript
function(keys, values) {
  return sum(values);
}
```
**Résultat Final** :  
```
A: 17  
B: 5  
```

---

#### 4.6 Exécuter une Vue dans CouchDB  
Une fois la vue créée dans un design document, exécutez-la :  
```bash
curl -X GET http://login:password@localhost:5984/BD_NAME/_design/DOC_VIEW/_view/nom_vue
```

---

#### 4.7 Réplication et MapReduce  
CouchDB garantit que les vues MapReduce sont automatiquement répliquées entre les nœuds pour une meilleure performance. Chaque nœud traite un sous-ensemble de données, puis les résultats sont combinés pour produire une sortie globale.

---

Si vous souhaitez des ajouts ou des ajustements, n'hésitez pas !

### 5. API REST de CouchDB
L'accès aux données CouchDB se fait via des requêtes HTTP simples. Voici quelques exemples courants :

#### 5.1. Vérifier l'état du serveur
```bash
curl -X GET http://login:password@localhost:5984/
```
- Réponse :
```json
{
  "couchdb": "Welcome",
  "version": "3.2.0"
}
```

#### 5.2. Créer une base de données
```bash
curl -X PUT http://login:password@localhost:5984/BD_NAME
```
- **PUT** : Crée une nouvelle base de données nommée `BD_NAME`.

#### 5.3. Ajouter un document
```bash
curl -X PUT http://login:password@localhost:5984/BD_NAME/DOC -d '{"cle":"valeur"}'
```
- **DOC** : Nom du document.
- **-d** : Contenu JSON à insérer.

#### 5.4. Insérer un fichier JSON dans un document
```bash
curl -X PUT http://login:password@localhost:5984/BD_NAME/JSN -d @jsn1:10098.json -H "Content-Type: application/json"
```
- Permet d'envoyer un fichier JSON complet dans CouchDB.

#### 5.5. Insertion de documents en masse (bulk insert)
```bash
curl -X POST http://login:password@localhost:5984/BD_NAME/_bulk_docs -d @docs_collection.json -H "Content-Type: application/json"
```
- Envoie plusieurs documents en une seule requête.
- **_bulk_docs** : Point d'entrée pour l'insertion groupée.

#### 5.6. Récupérer un document par son identifiant
```bash
curl -X GET http://login:password@localhost:5984/BD_NAME/doc:id
```
- Renvoie les données du document avec l'identifiant `id`.

---

### 6. Utilisation de CouchDB avec Docker
CouchDB peut être facilement déployé avec Docker. Voici les étapes :

#### 6.1. Télécharger et déployer CouchDB avec Docker
```bash
docker run -d --name couchdb -p 5984:5984 -e COUCHDB_USER=admin -e COUCHDB_PASSWORD=password couchdb
```
Cette commande :
- **déploie** un conteneur CouchDB,
- **expose** le port 5984,
- configure un utilisateur administrateur et un mot de passe.

#### 6.2. Accéder à CouchDB
Une fois CouchDB déployé, vous pouvez accéder à l'interface web via l'URL suivante :
```http
http://localhost:5984/_utils/
```
Cette interface permet de visualiser et gérer les bases de données, documents et réplications.

---

### 7. Cas d'utilisation de CouchDB
CouchDB est utilisé dans plusieurs domaines :
- **Applications web hors-ligne** : Synchronisation des données entre clients et serveurs.
- **Applications mobiles** : Stockage local et réplication des données.
- **IoT** : Gestion des données distribuées provenant de plusieurs appareils.
- **Big Data** : Traitement et agrégation de grandes quantités de données via MapReduce.
- **Applications décentralisées** : Prise en charge d'environnements multi-utilisateurs avec gestion des conflits.

---

### 8. Conclusion
CouchDB est une solution flexible et robuste pour les bases de données documentaires, grâce à son API REST, son support de la réplication, et son modèle de stockage JSON. Son intégration avec Docker en facilite le déploiement et son utilisation dans des applications modernes. Il est particulièrement adapté aux besoins des applications distribuées et hors-ligne.

