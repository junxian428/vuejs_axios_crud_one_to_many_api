<template>
<!---
  <div class="container mt-5 text-center">
      <div class="message-container">
        <h5>{{ message }}</h5>
      </div>
  </div>
      
  <div class="container mt-5 text-center">
    <a href="javascript:void(0)" class="btn btn-lg btn-primary" id="logoutBtn">Logout</a>
    <br>

  </div>


  <br>
  <br>

  
  <div class="button-container" >
  <button class="button" @mouseover="toggleHoverEffect(1)" @mouseout="toggleHoverEffect(1)" @click="navigateToItems">
    <img :src="buttonImages[0]" alt="Button 1">
  </button>
  <button class="button" @mouseover="toggleHoverEffect(2)" @mouseout="toggleHoverEffect(2)" @click="navigateToItems2">
    <img :src="buttonImages[1]" alt="Button 2">
  </button>
</div>
-->




<div id="app">
  
    <div class="sidebar">
      <p>User ID: {{ userID }}</p>
      <p>Token: {{ userToken }}</p>
    </div>
    <div class="navbar">
        <a href="#" @click="selectedLevel = 'Level 5 ERP'">Level 5 ERP</a>
        <a href="#" @click="selectedLevel = 'Level 4 WMS'">Level 4 WMS</a>
        <a href="#" @click="selectedLevel = 'Level 4 MES'">Level 4 MES</a>
        <a href="#" @click="selectedLevel = 'Level 3 SCADA/ HMI'">Level 3 SCADA/ HMI</a>
        <a href="#" @click="selectedLevel = 'Level 2 PLC'">Level 2 PLC</a>
        <a href="#" @click="selectedLevel = 'Level 1 Sensor and Actuator'">Level 1 Sensor and Actuator</a>

      <div class="logout">
        <a href="#" @click="logout">Logout</a>
      </div>

      </div>

    <div class="main" style="margin-top:50px; color:white;">
      <h2>Level 2 PLC </h2>
      <br>
      <br>
      <!-- -->






      <div>
    <!-- Form -->

    <div v-if="successModalVisible" class="modal">
      <div class="modal-content">
        <h2>Success!</h2>
        <p>Your data has been saved successfully.</p>
        <button @click="closeSuccessModal">Close</button>
      </div>
    </div>
  </div>




  <!-- Confirmation Modal -->
  <div v-if="confirmationModalVisible" class="modal">
      <div class="modal-content">
        <h2>Confirm Delete?</h2>
        <p>Are you sure you want to delete this data?</p>
        <button @click="confirmDelete">Delete</button>
        <button @click="cancelDelete">Cancel</button>
      </div>
    </div>

    
  <br>










      <!---->
      <table v-for="(plc, plcIndex) in plcItems" :key="plcIndex">
        <tr>
            <td width="60%"><button @click="toggleDropdown(plcIndex)">{{ plc.name }}</button></td>
            <td width="20%"><button @click="showDeletePLCConfirmation(plcIndex)" class="red-alert-button" style="margin-left:30px;">Delete PLC</button></td>
            <td width="20%"><button @click="editPLCToken(plc)"  class="ocean-blue-edit-button"  style="margin-left: 30px;">Edit Token</button>
            
            
              <form v-if="plc.editingToken" @submit.prevent="savePLCToken(plc)">
                  <label for="newToken">Hardware Token:</label>
                  <input v-model="plc.newToken" id="newToken" style="line-height: 28px;" required>
                  <br>
                  <button type="submit" class="purple-save-button">Save</button>
                  <button @click="cancelEditPLCToken(plc)" class="dark-cancel-button">Cancel</button>


                                      <!-- Success Modal -->
                    <div v-if="successModalVisible" class="modal">
                      <div class="modal-content">
                        <h2>Success!</h2>
                        <p>Your data has been saved successfully.</p>
                        <button @click="closeSuccessModal">Close</button>
                      </div>
                    </div>



              </form>
            </td>
                
          

                    <!--  -->
           
          </tr>

          

        <table v-if="plc.showDropdown">
          <thead>
            <tr>
              <th>Address ID</th>
              <th>Address</th>
              <th>Description</th>
              <th>Token</th>
              <th>PLC ID</th>
              <th>Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(address, addressIndex) in plc.addresses" :key="addressIndex">
              <td>{{ address.id }}</td> <!-- Display Address ID here -->
              <td>{{ address.name }}</td>
              <td v-if="!address.editing">{{ address.description }}</td>
              <td v-else><input v-model="address.description" @keyup.enter="saveAddress(address)" @blur="saveAddress(address)" placeholder="Address description"  style="line-height: 28px;" ></td>
              <td>{{ plc.token }}</td>
              <td>{{ plc.id }}</td>
              <td>
                <button @click=" showDeleteConfirmation(plcIndex, addressIndex)" class="red-alert-button">Delete</button>
                <br>
                <button v-if="!address.editing" @click="editAddress(address)" class="ocean-blue-edit-button">&nbsp&nbspEdit&nbsp&nbsp</button>
                <template v-else>
                  <button @click="saveAddress(address)" class="purple-save-button">Save</button>
                  <button @click="cancelEdit(address)" class="dark-cancel-button">Cancel</button>
                </template>
              </td>
            </tr>
          </tbody>
          <tfoot>
            <tr>
              <td>
                <input v-model="newAddressName"   style="line-height: 28px;" placeholder="Address Name">
              </td>
              <td>
                <input v-model="newAddressDescription"  style="line-height: 28px;" placeholder="Address description">
              </td>
              <td>
                {{ plc.token }}
              </td>
              <td>
                {{ plc.id }}
              </td>
              <td>
                <button @click="addAddress(plcIndex)" class="grass-green-add-button">Add Address</button>
              </td>
            </tr>
          </tfoot>
        </table>


      </table>




      
      <div>
        <input v-model="newPLCName" style="line-height: 28px;" placeholder="PLC name">
  <input v-model="newPLCToken" style="margin-left: 30px; line-height: 28px;" placeholder="PLC token">
  <button @click="addPLC" class="grass-green-add-button" style="margin-left: 30px;">Add PLC</button>
      </div>
    </div>
  </div>



</template>

<script>
import {onMounted, ref} from "vue";
import axios from "axios";
import {useRouter} from "vue-router";
import VueCookies from 'vue-cookies';

export default{
  name: "Home",
  
  data() {
    return{
      formData: {
        name: "",
        email: "",
        // Add more form fields as needed
      },
      addresses: {
        id: '', // Bind the 'name' input value to this property
        name: '', // Bind the 'description' input value to this property
      },
      responseData: null,
      error: null,
      successModalVisible: false,
      userID: '5', // Replace 'User123' with your user ID
      userToken: 'UserToken123', // Replace 'UserToken123' with your user token
      plcItems: [],
      categorizedAddresses : {},
      newAddressDescription: '',
      newAddressName : '',
      
    }
  },
  mounted() {
    // Making a GET request
    axios.get('http://localhost:8089/plc')
      .then(response => {
        this.plcItems = response.data;
        //for (let i = 0; i < response.data.length; i++) {
        //  console.log(response.data[i].name);
       // }
        //console.log(response.data[0].addresses[0]);
        //console.log(response.data[0].name);




// Initialize an empty object to store the categorized addresses under tokens
//const categorizedAddresses = {};

// Loop through the data array and categorize addresses under tokens
this.plcItems.forEach((item) => {
  if (item.token && item.addresses && item.addresses.length > 0) {
    // If the token does not exist in the categorizedAddresses object, initialize an array for it
    if (!this.categorizedAddresses[item.token]) {
      this.categorizedAddresses[item.token] = [];
    }

    // Add the addresses of the current item to the corresponding token array
    item.addresses.forEach((address) => {
      if (address.id && address.name) {
        this.categorizedAddresses[item.token].push({
          id: address.id,
          name: address.name,
          description: address.description,
        });
      }
    });
  }
});

// Output the categorized addresses under tokens
console.log("Categorized Addresses:", this.categorizedAddresses);
        //
        //
        
      })
      .catch(error => {
        this.error = error.message;
      });
  },
  methods: {
    showDeleteConfirmation(plcIndex, addressIndex) {
    const confirmed = confirm("Are you sure you want to delete this address?");
    if (confirmed) {
      this.deleteAddress(plcIndex, addressIndex);
    }
  },
//
saveData() {
      // Simulate saving data to the server (replace this with your actual API call)
      // For example, using Axios:
      // axios.post('/api/saveData', this.formData)
      //   .then((response) => {
      //     // Handle success
      //     this.showSuccessModal();
      //     // Clear the form data after successful submission
      //     this.formData = {};
      //   })
      //   .catch((error) => {
      //     // Handle error
      //   });

      // Simulate success for this example
      this.showSuccessModal();
      this.formData = {}; // Clear the form data after successful submission
    },
    showSuccessModal() {
      this.successModalVisible = true;
    },
    closeSuccessModal() {
      this.successModalVisible = false;
    },
  


//
findPLCWithAddressId(addressId) {
  for (let plcIndex = 0; plcIndex < this.plcItems.length; plcIndex++) {
    const plc = this.plcItems[plcIndex];
    for (let addressIndex = 0; addressIndex < plc.addresses.length; addressIndex++) {
      if (plc.addresses[addressIndex].id === addressId) {
        return { plcIndex, addressIndex };
      }
    }
  }
  return null; // Address ID not found in any PLC
},

    toggleDropdown(plcIndex) {
          this.plcItems[plcIndex].showDropdown = !this.plcItems[plcIndex].showDropdown;
        },
        deletePLC(plcIndex) {
           // Get the PLC ID from the PLC object to be deleted
            const plcIdToDelete = this.plcItems[plcIndex].id;

          // Make the DELETE request using Axios
          axios.delete(`http://localhost:8089/plc/${plcIdToDelete}`)
            .then(response => {
              // If the request is successful, remove the PLC item from the array in the frontend
              this.plcItems.splice(plcIndex, 1);
            })
            .catch(error => {
              // Handle errors if needed
              console.error('Error deleting PLC:', error);
            });
        },
        addPLC() {
      if (this.newPLCName.trim() !== '' && this.newPLCToken.trim() !== '') {
        const isDuplicateToken = this.plcItems.some(
          (plcItem) => plcItem.token === this.newPLCToken
        );

        if (isDuplicateToken) {
          alert('Token is already used in another PLC. Please use a different token.');
          return;
        }

        // Create the new PLC object
        const newPLC = {
          name: this.newPLCName,
          token: this.newPLCToken,
          editingToken: false,
          newToken: '',
          showDropdown: false,
          addresses: [],
          plc_userid: this.userID,
        };

        // Send a POST request to the server to create the new PLC
        axios.post('http://localhost:8089/plc', newPLC)
          .then(response => {
            // Handle success and update the list of PLC items or perform any other actions as required
            console.log('New PLC added successfully');
            this.plcItems.push(response.data); // Assuming the server responds with the newly created PLC object
          })
          .catch(error => {
            // Handle error, if needed
            console.error('Error adding new PLC:', error);
          });

        this.newPLCName = '';
        this.newPLCToken = '';
      }
    },
    async addAddress(plcIndex) {
  // Create a new address object
      //console.log(plcIndex);
      //console.log(this.plcItems[plcIndex]);
      //plc.addresses.name
      //console.log(plc.addresses.name);
      //console.log(this.plc.addresses.description);

      //console.log(newAddress.id);
      /*
        31/7/2023
        Author: junxian428
      */
     //console.log(this.newAddressName);
     //console.log(this.newAddressDescription);
     //console.log(this.plcItems[plcIndex]);
    // console.log(this.plcItems[plcIndex].id);

     //

      
     try {
      const plcId = this.plcItems[plcIndex].id; // Replace this with the correct PLC ID you want to update
      const response = await axios.put(`http://localhost:8089/plc/${plcId}`, {
        name: this.plcItems[plcIndex].name,
        token: this.plcItems[plcIndex].token,
        userid: this.userID,
        addresses: [
          {
            name: this.newAddressName,
            description: this.newAddressDescription,
          },
        ],
      });

      // The response data will contain the updated PLC information
      console.log("Updated PLC:", response.data);
      console.log("Updated PLC:", response.data.addresses[0].id);
      // After successfully adding the address, you can update the local Vue data to reflect the changes
      this.plcItems[plcIndex].addresses.push({
        id: response.data.addresses[0].id,
        name: this.newAddressName,
        description: this.newAddressDescription,
      });

      // Clear the input fields after adding the address
      this.newAddressName = "";
      this.newAddressDescription = "";



    } catch (error) {
      console.error("Error updating PLC:", error);
    }
    

     //console.log(this.addresses.id);

},

async updatePLCAddress(plcIndex, newAddress) {
      const plc = this.plcItems[plcIndex];

      try {
        // First, push the new address to the addresses array of the corresponding PLC
        //this.plcItems[plcIndex].addresses.push(newAddress);

        // Perform the PUT request to update the address array on the server
        const response = await axios.post(`http://localhost:8089/address`, {
          userid: 1,
          plcid: plcIndex,
          
        });

        // Optionally, update the local data with the response from the server if needed
        // this.plcItems[plcIndex].addresses = response.data.addresses;

        console.log('PLC address updated successfully');
      } catch (error) {
        console.error('Error updating PLC address:', error);
        // If there's an error during the PUT request, remove the newly added address from the local data
        this.plcItems[plcIndex].addresses.pop();
        throw error; // Re-throw the error to be caught by the calling method (addAddress)
      }
    },

    deleteAddress(addressIdToDelete,addressIndex) {
      // Find the PLC that contains the address with the specified Address ID
      //console.log(addressIdToDelete); 
      //console.log(addressIdToDelete);
      //console.log(addressIndex);
      //console.log(this.plcItems[addressIdToDelete].token);
      const address = this.categorizedAddresses[this.plcItems[addressIdToDelete].token];
      //console.log(address[addressIndex]);
      //console.log(address[addressIndex].id);

            // Make the DELETE request using Axios
            axios.delete(`http://localhost:8089/address/${address[addressIndex].id}`)
            .then(response => {
              // If the request is successful, remove the PLC item from the array in the frontend
              console.log(addressIndex);
              //this.address.pop(addressIndex, 1);
              //this.address.splice(addressIndex, 1);
              //this.plcItems[plcIndex].addresses.splice(addressIndex, 1);

            })
            .catch(error => {
              // Handle errors if needed
              console.error('Error deleting PLC:', error);
            });


      //console.log(this.categorizedAddresses[this.plcItems[addressIdToDelete].token[addressIndex]]);
    },
/*

            deleteAddress(plcIndex, addressIndex) {
          // Check if the PLC item exists at plcIndex

          if (this.plcItems[plcIndex]) {
            // Get the specified PLC item



           // const plcItem = this.plcItems[plcIndex];
            //
           // console.log(plcItem.addresses.id);

            // Check if the address exists at addressIndex within the addresses array of the PLC item
            if (plcItem.addresses && plcItem.addresses.length > addressIndex) {
              // Remove the address at addressIndex from the addresses array
                        // Make the DELETE request using Axios
                axios.delete(`http://localhost:8089/address/${addressIndex}`)
                  .then(response => {
                    // If the request is successful, remove the PLC item from the array in the frontend
                    plcItems.addresses.splice(addressIndex, 1);
                  })
                  .catch(error => {
                    // Handle errors if needed
                    console.error('Error deleting PLC:', error);
                  });

            } else {
              console.error('Invalid addressIndex:', addressIndex);
            }
          } else {
            console.error('Invalid plcIndex:', plcIndex);
          }
        },
        */

        editAddress(address) {
          address.editing = true;
        },
        saveAddress(address,addressIndex) {
          address.editing = false;
          //console.log(address.description);
          //console.log(address);
          axios.put(`http://localhost:8089/address/${address.id}`, address).then(response => {
          // Handle the response if needed
          console.log('Object updated successfully!', response);
          // You can also update the local object if needed
          this.proxyObject.description = this.updatedDescription;
          // Reset the input field for the next update
          this.updatedDescription = '';
          this.showSuccessModal();

        })
        .catch(error => {
          // Handle errors if any
          console.error('Error updating object:', error);
        
        });

        },
        cancelEdit(address) {
          address.editing = false;
        },
        editPLCToken(plc) {
          plc.editingToken = true;
          plc.newToken = plc.token;
        },

       savePLCToken(plc) {
      // Check if the new token is not already used in any other PLC
          const isDuplicateToken = this.plcItems.some(
            (plcItem) => plcItem !== plc && plcItem.token === plc.newToken
          );

          if (isDuplicateToken) {
            // Display an error message or take any action to handle the duplicate token
            alert('Token is already used in another PLC. Please use a different token.');
            return;
          }


          plc.token = plc.newToken;
          plc.editingToken = false;
          plc.newToken = '';
          this.showSuccessModal();
    },


        cancelEditPLCToken(plc) {
          plc.editingToken = false;
          plc.newToken = '';
        },

        showDeletePLCConfirmation(plcIndex) {
        const confirmed = confirm("Are you sure you want to delete this PLC?");
        if (confirmed) {
          this.deletePLC(plcIndex);
        }
      },

  },
  
}
/** 
import {onMounted, ref} from "vue";
import axios from "axios";
import {useRouter} from "vue-router";
import VueCookies from 'vue-cookies';

export default {
  name: "Home",
  data() {
  return {
    buttonImages: ['Config.png', 'Monitoring.png'],  // Replace with the paths to your button images
    hoverEffects: [false, false]  // Track hover effects for each button
  };
},
methods: {
  navigateToItems() {
    this.$router.push("/Items"); // Replace "/Items" with the desired route path
  },
  navigateToItems2() {
    this.$router.push("/Monitor"); // Replace "/Items" with the desired route path
  },
  toggleHoverEffect(index) {
    this.hoverEffects[index - 1] = !this.hoverEffects[index - 1];
  }
},


  setup() {
    const message = ref('');
    const router = useRouter();

    /*
    onMounted(async () => {
      try {
        const {data} = await axios.get('user');

        message.value = `Hi ${data.name}`;
      } catch (e) {
        await router.push('/login');
      }
    });



    
    onMounted(async () => {
      const sessionCookie = document.cookie.replace(
        /(?:(?:^|.*;\s*)access_Token\s*\=\s*([^;]*).*$)|^.*$/,
        '$1'
      );
      console.log(sessionCookie);
      const logoutBtn = document.getElementById('logoutBtn');


      //
      if(sessionCookie != ""){
        
        console.log("logout button event attached");
        logoutBtn.addEventListener('click', logout);
      } else{
        logoutBtn.textContent = "login";
        logoutBtn.addEventListener('click', login);
        //message.value = `Please login your account`;

      }
      
      //headers.append('Access-Control-Allow-Origin', 'http://localhost:3000');
      //headers.append('Access-Control-Allow-Credentials', 'true');
      try {
        const response = await axios.get('http://localhost:8087/dashboard', {
          headers: {
            Authorization: `Bearer ${sessionCookie}`,
            'Access-Control-Allow-Origin': '*'
          },
        });

        const { data } = response;


        //console.log(data);
        //

    
        // Access the object properties
        const username = data.username;
        const blob = data.blob;

        console.log("Username:", username);
        //console.log("Blob:",  atob(blob));
        VueCookies.set('blob_data',  blob, '7d'); // Save the access token in a cookie for 7 days

        const blobCookie = document.cookie.replace(
          /(?:(?:^|.*;\s*)blob_data\s*\=\s*([^;]*).*$)|^.*$/,
          '$1'
        );
      //console.log(blobCookie);
      //
      // URL decoding
      //var decodedValue = decodeURIComponent(blobCookie);

      // Base64 decoding
      //var base64DecodedValue = atob(decodedValue);
      //console.log(base64DecodedValue);
        //
        message.value = `Username : ${data.username}`;
        if(data.username === undefined){
          message.value = "Please login your account";

        }
      } catch (error) {
        console.error(error);
        await router.push('/');
      }
    });
    

        function convertToLogin() {
      logoutBtn.textContent = 'Login';
      logoutBtn.removeEventListener('click', logout);
      logoutBtn.addEventListener('click', login);
    }
          function login() {
        // Perform login actions here
        console.log('Login clicked');
        router.push('/login');
      }



    const logout = async () => {
      const sessionCookie = document.cookie.replace(
        /(?:(?:^|.*;\s*)access_Token\s*\=\s*([^;]*).*$)|^.*$/,
        '$1'
      );
      console.log(sessionCookie);

      //

      axios.post('http://')
        .then(() => {
          // Handle successful logout
          // Clear any user-related data in your Vue.js application
          // Redirect the user to the login page or any other desired route
          console.log("success");
          sessionStorage.removeItem('access_Token');
          document.cookie = "access_Token=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;";
          localStorage.removeItem('access_Token');

          message.value = `You are logout please login again`;
          //

          //
          convertToLogin();
          router.push('/login');

        })
        .catch(error => {
          // Handle logout error
          console.log(error);
          router.push('/login');
        });
    }

    return {
      message,
      logout
    }



  }
}


*/



</script>

<style>
  .modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 9999; /* Set the z-index to place the modal on top */
  }

  .modal-content {
    background-color: #fde2e8; /* Set the background color to white-pink */
    border: 2px solid #ff1493; /* Add a 2px border with pink color */
    padding: 20px;
    border-radius: 5px;
    color:black;
  }


  .modal-content button {
    /* Add styles for the "Close" button */
    border: 1px solid #000; /* Black border */
    padding: 8px 12px; /* Adjust padding as needed */
    background-color: #fff; /* White background */
    color: #000; /* Black text color */
    cursor: pointer;
    border-radius: 5px;
  }

  .modal-content button:hover {
    background-color: #000; /* Change background color on hover */
    color: #fff; /* Change text color on hover */
  }

/* Styling for the black alert button */
.dark-cancel-button {
  padding: 10px 20px;
  background-color: black; /* Change the background color to black */
  color: white;
  border: 2px solid darkgray; /* Change the border color to a darker shade of gray */
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
}

/* On hover effect */
.dark-cancel-button:hover {
  background-color: darkgray; /* Change the hover background color to a darker shade of gray */
}

/* On click effect */
.dark-cancel-button:active {
  background-color: dimgray; /* Use a darker shade of gray on click */
}



/* Styling for the purple alert button */
.purple-save-button {
  padding: 10px 20px;
  background-color: purple; /* Change the background color to purple */
  color: white;
  border: 2px solid darkpurple; /* Change the border color to a darker shade of purple */
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
}

/* On hover effect */
.purple-save-button:hover {
  background-color: darkpurple; /* Change the hover background color to a darker shade of purple */
}

/* On click effect */
.purple-save-button:active {
  background-color: #800080; /* Use a darker shade of purple on click (here using hex code) */
}




/* Styling for the grass green alert button */
.grass-green-add-button {
  padding: 10px 20px;
  background-color: #3CB371; /* Use your preferred grass green color code */
  color: white;
  border: 2px solid #2E8B57; /* Use a slightly darker shade of grass green for the border */
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
}

/* On hover effect */
.grass-green-add-button:hover {
  background-color: #2E8B57; /* Use a slightly darker shade of grass green on hover */
}

/* On click effect */
.grass-green-add-button:active {
  background-color: #228B22; /* Use a darker shade of grass green on click */
}


/* Styling for the ocean blue alert button */
.ocean-blue-edit-button {
  padding: 10px 20px;
  background-color: #007BAC; /* Use your preferred ocean blue color code */
  color: white;
  border: 2px solid #005A80; /* Use a slightly darker shade of ocean blue for the border */
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
}

/* On hover effect */
.ocean-blue-edit-button:hover {
  background-color: #005A80; /* Use a slightly darker shade of ocean blue on hover */
}

/* On click effect */
.ocean-blue-edit-button:active {
  background-color: #00416A; /* Use a darker shade of ocean blue on click */
}

/* Styling for the red alert button */
.red-alert-button {
  padding: 10px 20px;
  background-color: red;
  color: white;
  border: 2px solid darkred;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
}

/* On hover effect */
.red-alert-button:hover {
  background-color: darkred;
}

/* On click effect */
.red-alert-button:active {
  background-color: crimson;
}


/* Sidebar styles */
.sidebar {
  height: 100%;
  width: 200px;
  position: fixed;
  top: 0;
  left: 0;
  background-color: #111;
  padding-top: 20px;
}

.sidebar a {
  display: block;
  color: white;
  padding: 16px;
  text-decoration: none;
}

.sidebar a:hover {
  background-color: #ddd;
  color: black;
}

/* Main content styles */
.main {
  margin-left: 200px; /* Same as the width of the sidebar */
  padding: 20px;
}

table {
  border-collapse: collapse;
  width: 100%;
}

th, td {
  border: 1px solid black;
  padding: 8px;
  text-align: left;
}


/* Navbar styles */
.navbar {
  background-color: #333;
  overflow: hidden;
  width: 100%;
  position: fixed; /* Position the navbar as fixed */
  top: 0; /* Place the navbar at the top */
  left: 200px; /* Align with the sidebar */
  z-index: 2; /* Add z-index to position the navbar above the main content */
}

.navbar a {
  float: left;
  display: block;
  color: white;
  text-align: center;
  padding: 14px 16px;
  text-decoration: none;
}

.navbar a:hover {
  background-color: #ddd;
  color: black;
}

/* Right-aligned logout button */
.logout {
  display: flex;
  align-items: center;
}

.logout a {
  color: white;
  padding: 14px 16px;
  text-decoration: none;
}

.logout a:hover {
  background-color: #ddd;
  color: black;
}

</style>