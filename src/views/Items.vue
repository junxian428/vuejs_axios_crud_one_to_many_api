<template>
  <div>
    <h3>Alarm Configuration</h3>
    <img :src="imageUrl" alt="Image" style="width: 500px; height: 300px;">
    <br><br><br><br>

    <!-- Form for creating new item -->
    <form @submit.prevent="addItem">
      <input v-model="newItemText" type="text" placeholder="Enter item text" required>
      <input v-model="newItemAlarm" type="text" placeholder="Enter alarm message" required>
      <button type="submit">Add Item</button>
    </form>
    <br><br><br>

    <!-- List of items -->
    <table>
      <thead>
        <tr>
          <th>PLC ID</th>
          <th>Address</th>
          <th>Alarm Message</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in items" :key="item.id">
          <td v-if="index !== editIndex">{{ item.plc_id }}</td>
          <td v-if="index !== editIndex">{{ item.text }}</td>
          <td v-else>
            <form @submit.prevent="updateItem(index)">
              <input v-model="editedItemText" type="text" required>
              <input v-model="editedItemAlarm" type="text" required>
              <button type="submit">Update</button>
              <button @click="cancelEdit">Cancel</button>
            </form>
          </td>
          <td>{{ item.alarm }}</td>
          <td>
            <button @click="editItem(index)" v-if="index !== editIndex">Edit</button>
            <button @click="deleteItem(item.id)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      imageUrl: './AlarmConfiguration.png',  // Replace with the URL of your image
      items: [],                // Array to store items
      newItemText: '',          // Text for new item
      newItemAlarm: '',         // Alarm message for new item
      editIndex: null,          // Index of item being edited (-1 for new item)
      editedItemText: '',       // Text for the item being edited
      editedItemAlarm: '',      // Alarm message for the item being edited
    };
  },
  mounted() {
    this.fetchItems();
  },
  methods: {
    fetchItems() {
      // Your code to fetch items using axios
      
      const sessionCookie = document.cookie.replace(
          /(?:(?:^|.*;\s*)access_Token\s*\=\s*([^;]*).*$)|^.*$/,
          '$1'
        );
        const blobCookie = document.cookie.replace(
            /(?:(?:^|.*;\s*)blob_data\s*\=\s*([^;]*).*$)|^.*$/,
            '$1'
          );

        
        axios.get('http://localhost:8082/api/items', {
          headers: {
            Authorization: `Bearer ${sessionCookie}`,
          },
        })
          .then(response => {
            this.items = response.data;
          })
          .catch(error => {
            console.error('Error fetching items:', error);
          });


    },
    addItem() {
      // Your code to add an item using axios

      const sessionCookie = document.cookie.replace(
          /(?:(?:^|.*;\s*)access_Token\s*\=\s*([^;]*).*$)|^.*$/,
          '$1'
        );

        const blobCookie = document.cookie.replace(
            /(?:(?:^|.*;\s*)blob_data\s*\=\s*([^;]*).*$)|^.*$/,
            '$1'
          );

        const newItem = {
          text: this.newItemText,
          alarm: this.newItemAlarm,
        };
        console.log(newItem);
        axios.post('http://localhost:8082/api/items', newItem, {
          headers: {
            Authorization: `Bearer ${sessionCookie}`,

          },
        })
          .then(response => {
            this.items.push(response.data);
            this.newItemText = '';
            this.newItemAlarm = '';
          })
          .catch(error => {
            console.error('Error adding item:', error);
          });



    },
    editItem(index) {
      // Your code to edit an item
      this.editIndex = index;
        this.editedItemText = this.items[index].text;
        this.editedItemAlarm = this.items[index].alarm;
    },
    updateItem(index) {
      // Your code to update an item using axios

      const sessionCookie = document.cookie.replace(
          /(?:(?:^|.*;\s*)access_Token\s*\=\s*([^;]*).*$)|^.*$/,
          '$1'
        );

        const blobCookie = document.cookie.replace(
            /(?:(?:^|.*;\s*)blob_data\s*\=\s*([^;]*).*$)|^.*$/,
            '$1'
          ); 
        const updatedItem = {
          id: this.items[this.editIndex].id,
          text: this.editedItemText,
          alarm: this.editedItemAlarm,
        };
  
        axios.put(`http://localhost:8082/api/items/${updatedItem.id}`, updatedItem, {
          headers: {
            Authorization: `Bearer ${sessionCookie}`,

          },
        })
          .then(() => {
            this.items[this.editIndex].text = this.editedItemText;
            this.items[this.editIndex].alarm = this.editedItemAlarm;
            this.cancelEdit();
          })
          .catch(error => {
            console.error('Error updating item:', error);
          });



    },
    cancelEdit() {
      // Your code to cancel the item edit

      this.editIndex = null;
        this.editedItemText = '';
    },
    deleteItem(itemId) {
      // Your code to delete an item using axios

      const sessionCookie = document.cookie.replace(
          /(?:(?:^|.*;\s*)access_Token\s*\=\s*([^;]*).*$)|^.*$/,
          '$1'
        );

        const blobCookie = document.cookie.replace(
            /(?:(?:^|.*;\s*)blob_data\s*\=\s*([^;]*).*$)|^.*$/,
            '$1'
          );

        axios.delete(`http://localhost:8082/api/items/${itemId}`, {
          headers: {
            Authorization: `Bearer ${sessionCookie}`,
          },
        })
          .then(() => {
            this.items = this.items.filter(item => item.id !== itemId);
          })
          .catch(error => {
            console.error('Error deleting item:', error);
          });



    },
  },
};
</script>

<style>
table {
  border-collapse: collapse;
  width: 100%;
}

th, td {
  border: 1px solid black;
  padding: 8px;
  text-align: center;
}

th {
  background-color: #f2f2f2;
}

form {
  display: inline-block;
  border: 1px solid black;
  padding: 8px;
  text-align: center;
}

form input[type="text"],
form button {
  display: block;
  margin: 0 auto;
  padding: 4px 8px;
}

form button {
  margin-top: 8px;
}
</style>
