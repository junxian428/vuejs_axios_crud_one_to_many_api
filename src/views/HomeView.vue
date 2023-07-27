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
              <th>Address</th>
              <th>Description</th>
              <th>Token</th>
              <th>PLC ID</th>
              <th>Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(address, addressIndex) in plc.addresses" :key="addressIndex">
              <td>{{ address.name }}</td>
              <td v-if="!address.editing">{{ address.description }}</td>
              <td v-else><input v-model="address.description" @keyup.enter="saveAddress(address)" @blur="saveAddress(address)" placeholder="Address description"  style="line-height: 28px;" ></td>
              <td>{{ plc.token }}</td>
              <td>{{ plc.id }}</td>
              <td>
                <button @click="showDeleteConfirmation(plcIndex, addressIndex)" class="red-alert-button">Delete</button>
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
                <input v-model="newAddress.name"   style="line-height: 28px;" placeholder="Address name">
              </td>
              <td>
                <input v-model="newAddress.description"  style="line-height: 28px;" placeholder="Address description">
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
        <input v-model="newPLCName"  style="line-height: 28px;" placeholder="PLC name">
        <input v-model="newPLCToken" style=" margin-left:30px; line-height: 28px;" placeholder="PLC token">
        <button @click="addPLC" class="grass-green-add-button" style="margin-left:30px;">Add PLC</button>
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
      successModalVisible: false,
      userID: 'User123', // Replace 'User123' with your user ID
        userToken: 'UserToken123', // Replace 'UserToken123' with your user token
        plcItems: [
          {
            id: 1,
            name: 'Primary Treatment Plant Tank 1 (OMRON PLC)',
            token: 'Token 1',
            editingToken: false,
            newToken: '',
            showDropdown: false,
            addresses: [
              { name: '100.00', description: 'Pressure Alarm', editing: false },
              { name: '100.01', description: 'pH Alarm', editing: false },
              { name: '100.02', description: 'Temperature Alarm', editing: false },
              { name: '100.03', description: 'Level Alarm', editing: false },
              { name: '100.04', description: 'Security Alarm', editing: false },
              { name: '100.05', description: 'Fire Alarm', editing: false },
              // Add more addresses with descriptions here
            ],
          },
          {
            id: 2,
            name: 'Primary Treatment Plant Tank 2 (Siemen PLC)',
            token: 'Token 2',
            editingToken: false,
            newToken: '',
            showDropdown: false,
            addresses: [
              { name: 'Address 1', description: 'Description for Address 1 in PLC 2', editing: false },
              { name: 'Address 2', description: 'Description for Address 2 in PLC 2', editing: false },
              // Add more addresses with descriptions here
            ],
          },
          {
            id: 3,
            name: 'Primary Treatment Plant Tank 3 (Delta PLC)',
            token: 'Token 2',
            editingToken: false,
            newToken: '',
            showDropdown: false,
            addresses: [
              { name: 'Address 1', description: 'Description for Address 1 in PLC 3', editing: false },
              { name: 'Address 2', description: 'Description for Address 2 in PLC 3', editing: false },
              // Add more addresses with descriptions here
            ],
          },
          {
            id: 4,
            name: 'Secondary Treatment Plant Tank 1 (Mitsubushi PLC)',
            token: 'Token 2',
            editingToken: false,
            newToken: '',
            showDropdown: false,
            addresses: [
              { name: 'Address 1', description: 'Description for Address 1 in PLC 4', editing: false },
              { name: 'Address 2', description: 'Description for Address 2 in PLC 4', editing: false },
              // Add more addresses with descriptions here
            ],
          },
          {
            id: 5,
            name: 'Secondary Treatment Plant Tank 2 (ABB PLC)',
            token: 'Token 2',
            editingToken: false,
            newToken: '',
            showDropdown: false,
            addresses: [
              { name: 'Address 1', description: 'Description for Address 1 in PLC 4', editing: false },
              { name: 'Address 2', description: 'Description for Address 2 in PLC 4', editing: false },
              // Add more addresses with descriptions here
            ],
          },
          {
            id: 6,
            name: 'Secondary Treatment Plant Tank 3 (Burkert PLC)',
            token: 'Token 2',
            editingToken: false,
            newToken: '',
            showDropdown: false,
            addresses: [
              { name: 'Address 1', description: 'Description for Address 1 in PLC 4', editing: false },
              { name: 'Address 2', description: 'Description for Address 2 in PLC 4', editing: false },
              // Add more addresses with descriptions here
            ],
          },
          {
            id: 7,
            name: 'Tertiary Treatment Plant Tank 1 (Amsamotion PLC)',
            token: 'Token 2',
            editingToken: false,
            newToken: '',
            showDropdown: false,
            addresses: [
              { name: 'Address 1', description: 'Description for Address 1 in PLC 4', editing: false },
              { name: 'Address 2', description: 'Description for Address 2 in PLC 4', editing: false },
              // Add more addresses with descriptions here
            ],
          },
          {
            id: 8,
            name: 'Tertiary Treatment Plant Tank 2 (HFCA PLC)',
            token: 'Token 2',
            editingToken: false,
            newToken: '',
            showDropdown: false,
            addresses: [
              { name: 'Address 1', description: 'Description for Address 1 in PLC 4', editing: false },
              { name: 'Address 2', description: 'Description for Address 2 in PLC 4', editing: false },
              // Add more addresses with descriptions here
            ],
          },
          {
            id: 9,
            name: 'Tertiary Treatment Plant Tank 3 (Allen Bradley PLC)',
            token: 'Token 2',
            editingToken: false,
            newToken: '',
            showDropdown: false,
            addresses: [
              { name: 'Address 1', description: 'Description for Address 1 in PLC 4', editing: false },
              { name: 'Address 2', description: 'Description for Address 2 in PLC 4', editing: false },
              // Add more addresses with descriptions here
            ],
          },
          {
            id: 10,
            name: 'Advanced Treatment Plant Tank 1 (Schneider PLC)',
            token: 'Token 2',
            editingToken: false,
            newToken: '',
            showDropdown: false,
            addresses: [
              { name: 'Address 1', description: 'Description for Address 1 in PLC 4', editing: false },
              { name: 'Address 2', description: 'Description for Address 2 in PLC 4', editing: false },
              // Add more addresses with descriptions here
            ],
          },
          {
            id: 11,
            name: 'Advanced Treatment Plant Tank 2 (Toshiba PLC)',
            token: 'Token 2',
            editingToken: false,
            newToken: '',
            showDropdown: false,
            addresses: [
              { name: 'Address 1', description: 'Description for Address 1 in PLC 4', editing: false },
              { name: 'Address 2', description: 'Description for Address 2 in PLC 4', editing: false },
              // Add more addresses with descriptions here
            ],
          },
          {
            id: 12,
            name: 'Advanced Treatment Plant Tank 3 (Keyence PLC)',
            token: 'Token 2',
            editingToken: false,
            newToken: '',
            showDropdown: false,
            addresses: [
              { name: 'Address 1', description: 'Description for Address 1 in PLC 4', editing: false },
              { name: 'Address 2', description: 'Description for Address 2 in PLC 4', editing: false },
              // Add more addresses with descriptions here
            ],
          },
          // Add more PLC items here as needed
        ],
        newPLCName: '',
        newPLCToken: '',
        newAddress: { name: '', description: '' },
    }
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
  
    toggleDropdown(plcIndex) {
          this.plcItems[plcIndex].showDropdown = !this.plcItems[plcIndex].showDropdown;
        },
        deletePLC(plcIndex) {
          this.plcItems.splice(plcIndex, 1);
        },
        addPLC() {
          if (this.newPLCName.trim() !== '' && this.newPLCToken.trim() !== '') {
                  const isDuplicateToken = this.plcItems.some(
                (plcItem) => plcItem.token === this.newPLCToken
              );

              if (isDuplicateToken) {
                // Display an error message or take any action to handle the duplicate token
                alert('Token is already used in another PLC. Please use a different token.');
                return;
              }


            this.plcItems.push({
              id: this.plcItems.length + 1,
              name: this.newPLCName,
              token: this.newPLCToken,
              editingToken: false,
              newToken: '',
              showDropdown: false,
              addresses: [],
            });
            this.newPLCName = '';
            this.newPLCToken = '';
            
          }
        },
        addAddress(plcIndex) {
          if (this.newAddress.name.trim() !== '' && this.newAddress.description.trim() !== '') {
            this.plcItems[plcIndex].addresses.push({
              name: this.newAddress.name,
              description: this.newAddress.description,
              editing: false,
            });
            this.newAddress = { name: '', description: '' };
          }
        },
        deleteAddress(plcIndex, addressIndex) {
          this.plcItems[plcIndex].addresses.splice(addressIndex, 1);
        },
        editAddress(address) {
          address.editing = true;
        },
        saveAddress(address) {
          address.editing = false;
          this.showSuccessModal();

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

      axios.post('http://javaspring-env.eba-dzxvrt3g.us-east-1.elasticbeanstalk.com/api/v1/auth/quit')
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