<template>
  <div class="about">
    <h3 style="color:white;">PLC Monitoring System</h3>
    <!---
    <img :src="imageUrl" alt="Image" style="width: 500px; height: 300px;">-->
      <br>
      <br>
      <br>
      <p style="color:white;">
        Tech Architecture is FE/BE separated will allow the system adaptability and scalability as well as the maintainability.
        However, in order to build this kind of application requires strong IT team has both understanding toward IT world and OT world as well as the various IT skillset.
        It is kinda challenging without adapting the divide and conquer metholody. This is designed for industrial 4.0. 
        Why use this platform? This platform is supported by the experience IT developer works with OT world closely.
        FAQ. Is this integrated with mobile application or third party platform ? The answer is yes. It is built based on API and event-driven architecture (subscriber and publisher archicture)
        which allows the exchange of data between the hardware and software as well as this platform. 
        Is this sustainable? The answer is yes. It is because it is FE/BE architecture allowing the ease of migration and refactor...
      </p>
      <br>
      <footer id="footer">
          <p style="color:white;">
            Robomatics (Johor) Sdn. Bhd.
          </p>
      
      </footer>
  </div>
</template>



<script>
export default {
  data() {
    return {
      imageUrl: './IoTMonitoringSystem.png'  // Replace with the URL of your image
    };
  }
};


</script>


<style>
#footer {
  position: absolute;
  bottom: 0;
  width: 100%;
  height: 2.5rem;            /* Footer height */
}
</style>