<template>
  <div class="product-management">
    <h1>Gestion des Produits</h1>
    <form @submit.prevent="saveProduct" class="product-form">
      <input v-model="product.name" placeholder="Nom" />
      <input v-model="product.description" placeholder="Description" />
      <input type="number" v-model="product.price" placeholder="Prix" />
      <button type="submit">{{ editing ? "Mettre à jour" : "Créer" }}</button>
    </form>
    <ul class="product-list">
      <li v-for="item in products" :key="item.id" class="product-item">
        <span class="product-info">{{ item.name }} - {{ item.price }} €</span>
        <div class="product-actions">
          <button class="edit-btn" @click="editProduct(item)">Modifier</button>
          <button class="delete-btn" @click="deleteProduct(item.id)">Supprimer</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      products: [],
      product: { name: "", description: "", price: 0 },
      editing: false,
    };
  },
  methods: {
    fetchProducts() {
      axios.get("http://127.0.0.1:8000/api/products/").then((response) => {
        this.products = response.data;
      });
    },
    saveProduct() {
      if (this.editing) {
        axios
          .put(
            `http://127.0.0.1:8000/api/products/${this.product.id}/`,
            this.product
          )
          .then(() => {
            this.fetchProducts();
            this.resetForm();
          });
      } else {
        axios.post("http://127.0.0.1:8000/api/products/", this.product).then(() => {
          this.fetchProducts();
          this.resetForm();
        });
      }
    },
    editProduct(item) {
      this.product = { ...item };
      this.editing = true;
    },
    deleteProduct(id) {
      axios.delete(`http://127.0.0.1:8000/api/products/${id}/`).then(() => {
        this.fetchProducts();
      });
    },
    resetForm() {
      this.product = { name: "", description: "", price: 0 };
      this.editing = false;
    },
  },
  mounted() {
    this.fetchProducts();
  },
};
</script>

<style>
form {
  margin-bottom: 20px;
}
input {
  margin: 5px;
  padding: 10px;
}
button {
  margin: 5px;
}
</style>
<style scoped>
.product-management {
  font-family: Arial, sans-serif;
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background: #f9f9f9;
  border: 1px solid #ddd;
  border-radius: 8px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

h1 {
  text-align: center;
  color: #333;
  margin-bottom: 20px;
}

.product-form {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-bottom: 20px;
}

.product-form input {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}

.product-form button {
  padding: 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
}

.product-form button:hover {
  background-color: #0056b3;
}

.product-list {
  list-style: none;
  padding: 0;
}

.product-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  margin-bottom: 10px;
  background: white;
  border: 1px solid #ddd;
  border-radius: 4px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

.product-info {
  font-size: 16px;
  color: #333;
}

.product-actions button {
  padding: 5px 10px;
  margin-left: 5px;
  border: none;
  border-radius: 4px;
  font-size: 14px;
  cursor: pointer;
}

.product-actions .edit-btn {
  background-color: #ffc107;
  color: #fff;
}

.product-actions .edit-btn:hover {
  background-color: #e0a800;
}

.product-actions .delete-btn {
  background-color: #dc3545;
  color: #fff;
}

.product-actions .delete-btn:hover {
  background-color: #c82333;
}
</style>
