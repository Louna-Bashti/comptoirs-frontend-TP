<template>
  <main>
    <div>
      <!-- Un formulaire pour saisir les valeurs du produit à ajouter -->
      <form @submit.prevent="ajouteProduit">
        <div>
          <input
            id="nom"
            v-model="data.formulaireProduit.nom"
            placeholder="Nom"
          />
        </div>
        <div>
          <input
            id="unitesStock"
            v-model="data.formulaireProduit.UnitesEnStock"
            placeholder="Unités en stock"
          />
        </div>
        <div>
          <input
            id="unitesCommandees"
            v-model="data.formulaireProduit.UnitesCommandees"
            placeholder="Unités commandées"
          />
        </div>
        <button type="submit">Ajouter</button>
      </form>
    </div>
    <div>
      <table>
        <caption>
          Liste des produits
        </caption>
        <tr>
          <th>Référence</th>
          <th>Nom</th>
          <th>Unités en stock</th>
          <th>Unités commandées</th>
          <th>Action</th>
        </tr>
        <!-- Si le tableau des produits est vide -->
        <tr v-if="!data.listeProduits">
          <td colspan="4">Veuillez patienter, chargement des produits...</td>
        </tr>
        <!-- Si le tableau des produits n'est pas vide -->
        <tr v-for="produit in data.listeProduits" :key="produit.reference">
          <td>{{ produit.reference }}</td>
          <td>{{ produit.nom }}</td>
          <td>{{ produit.unitesEnStock }}</td>
          <td>{{ produit.unitesCommandees }}</td>
          <td>
            <button @click="deleteEntity(produit._links.self.href)">
              Supprimer
            </button>
          </td>
        </tr>
        <tr>
          <th>button @click="pagePrecedente()">Page précédente</th>
          <th><button @click="pageSuivante()">Page suivante</button></th>
          <th><button @click="debutPage()">Début</button></th>
          <th>button @click="finPage()">Fin</th>
        </tr>
      </table>
    </div>
  </main>
</template>

<script setup>
import { reactive, onMounted } from "vue";
import { BACKEND, doAjaxRequest } from "../api";

// Pour réinitialiser le formuaire
const produitVide = {
  libelle: "",
  description: "",
};

let data = reactive({
  // Les données saisies dans le formulaire
  formulaireProduit: { ...produitVide },
  // La liste des produits affichée sous forme de table
  listeProduits: [],
});

function chargeProduits() {
  // Appel à l'API pour avoir la liste des produits
  // Trié par code, descendant
  // Verbe HTTP GET par défaut
  doAjaxRequest(BACKEND + "/api/produits?sort=reference,nom&size=5")
    .then((json) => {
      data.listeProduits = json._embedded.produits;
    })
    .catch((error) => alert(error.message));
}

function debutPage() {
  // Appel à l'API pour avoir la liste des produits
  // Trié par code, descendant
  // Verbe HTTP GET par défaut
  doAjaxRequest(BACKEND + "/api/produits?sort=reference,nom&page=0&size=5")
    .then((json) => {
      data.listeProduits = json._embedded.produits;
    })
    .catch((error) => alert(error.message));
}

function pageSuivante() {
  // Appel à l'API pour avoir la liste des produits
  // Trié par code, descendant
  // Verbe HTTP GET par défaut
  doAjaxRequest(BACKEND + "/api/produits?sort=reference,nom&page=1&size=5")
    .then((json) => {
      data.listeProduits = json._embedded.produits;
    })
    .catch((error) => alert(error.message));
}

function ajouteProduit() {
  // Ajouter un produit avec les données du formulaire
  const options = {
    method: "POST", // Verbe HTTP POST pour ajouter un enregistrement
    body: JSON.stringify(data.formulaireProduit),
    headers: {
      "Content-Type": "application/json",
    },
  };
  doAjaxRequest(BACKEND + "/api/produits", options)
    .then(() => {
      // Réinitialiser le formulaire
      data.formulaireProduit = { ...produitVide };
      // Recharger la liste des catégories
      chargeProduits();
    })
    .catch((error) => alert(error.message));
}
/**
 * Supprime une entité
 * @param entityRef l'URI de l'entité à supprimer
 */
function deleteEntity(entityRef) {
  doAjaxRequest(entityRef, { method: "DELETE" })
    .then(chargeProduits)
    .catch((error) => alert(error.message));
}

// A l'affichage du composant, on affiche la liste
onMounted(chargeProduits);
</script>

<style scoped>
td,
th {
  border: 1px solid #ddd;
  padding: 8px;
}

th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #232623;
  color: rgb(255, 255, 255);
}
</style>
