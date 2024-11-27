
<template>

   <header id="part_welcomeburgeronline">
            <h1>Welcome to BurgerHeavenOnline</h1>
            <p> You can order from options below! </p>
   </header>



    <main>
        <section class="ingredient" id="part_orderburger"> 
            <h2> Choose your burger: </h2>

                <p> Burger options: </p>

                    <div class="wrapper" id="div_allburgers" >      <!-- div wrapper with under div to make grid layout-->
                        
                        <!-- Loop through the burgers array and pass each burger to the Burger component, updated for OneBurger -->
                      
                        <Burger v-for="burger in burgers" 
                                v-bind:key="burger.name" 
                                v-bind:burger="burger" 
                                v-on:orderBurger="addToOrder($event)" />
                    </div>
            </section>

        <section id="part_costumerinformation"> 
            
            <div id="div_costumerinformation">
                <h2> Costumerinformation: </h2>
                    <p> Fill in your paymentinformation below.  </p>


                    <h3> Delivery information: </h3>
                        <!-- For binding info in templates to be used in script section, use v-model -->
                        <p>
                            <label for="namn">Name:</label><br>
                            <input type="text" id="namn" v-model="customerInfo.name" required="required" placeholder="First- and lastname">
                        </p>
                    
                        <p>
                            <label for="email">Email:</label><br>
                            <input type="email" id="email" v-model="customerInfo.email" required="required" placeholder="E-mail address">
                        </p>

                        <p>
                            <label for="adress">Adress:</label><br>
                            <input type="text" id="adress" v-model="customerInfo.adress" placeholder="Streetname">
                        </p>

                        <p>
                            <input type="number" id="number" v-model="customerInfo.housenumber" required="required" placeholder="Housenumber">
                        </p>

                        <p>
                            <label for="recipient">Choose payment option:</label><br>
                            <select id="recipient" v-model="customerInfo.paymentOpt">
                                <option>Swish</option>
                                <option selected="selected" > Bank Card  </option>
                                <option>Invoice</option>
                                <option>At pick-up</option>
                            </select>
                        </p>

                        <p> Choose your sex: </p> 
                        <label>
                            <input type="radio" v-model="customerInfo.sex" value="male" > Male
                        </label> <br>
                        <label>
                            <input type="radio" v-model="customerInfo.sex" value="female"> Female
                        </label>
                        <label> <br>
                            <input type="radio" v-model="customerInfo.sex" value="wontsay" checked="checked"> Do not wish to provide
                        </label>
                      
                </div>

                <!-- map of city for placing orders -->
                <div id="div_costumer_map">
                    <div id="map" v-on:click="addOrder">
                      
               <!-- The v-if="location.x && location.y" ensures the dot is only rendered after a location is set.//chat-->

                      <div
                        v-if="location.x && location.y" class="dot" v-bind:style="{ left: `${location.x}px`, top: `${location.y}px` }"> 
                      </div>

                    </div>
                </div>

        </section>

        <section id="part_button">

                <!-- button to place order-->
                <!-- byta ut type mot v-on:click -->

                <button v-on:click="placeOrder">
                  <img src="/img/placeraorder.jpeg" alt="Place Order" >
                </button>
        </section>
    </main>

     <!-- to separate footer from rest of the website, adds a horisontial line-->
     <hr> 


<footer>
    <p> Copyright Hypothetical Burgers Inc 2024 </p>
</footer>

</template>

<script>

import Burger from '../components/OneBurger.vue'
import menu from '/Users/mariekexs/Documents/GranssnittLAB1/1MD031-lab-2024/src/assets/menu.json';
import io from 'socket.io-client'


const socket = io("localhost:3000");

    // Constructor function for MenuItem - NOT USED ANYMORE (menu.json instead), BUT KEEP
      function MenuItem(name, kCal, glutenfree, lactosefree, vegetarian, imageUrl) {
        this.name = name;
        this.kCal = kCal;
        this.glutenfree = glutenfree;
        this.lactosefree = lactosefree;
        this.vegetarian = vegetarian;
        this.imageUrl = imageUrl;
      }


    // Define burgers array
    const burgers = [  
      menu[0], 
      menu[1], 
      menu[2]
      ];


//from code given

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {

    console.log("Burger array:", burgers);

    return {
      burgers: menu, // Use the array from json.menu, load all burgers

      //default values now
      customerInfo: {
        name: "",
        email: "",
        adress: "",
        housenumber: "",
        paymentOpt: "Bank Card",
        sex: "wontsay",

      },

      // show location for click on map later
      location: { 
                  x: 0,
                  y: 0
                }

      };
    
  },

  methods: {

    // Generates a radom 5-digit order number
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },

    //This function is triggered when a user interacts with an element (like clicking a map or a button)
    addOrder: function (event) {

      const mapRect = event.currentTarget.getBoundingClientRect();
      const offsetX = event.clientX - mapRect.left;
      const offsetY = event.clientY - mapRect.top;

      //Update location with latest click
      this.location = { x: offsetX, y: offsetY };

      // Emit to the socket
      socket.emit("addOrder", { orderId: this.getOrderNumber(),

                                details: this.location, //Use updated location for the order

                                orderItems: ["Beans", "Curry"] //Probably change
                              }
                 );
    },


    
    //lyssnare
    addToOrder: function() {
      console.log("Är i addToOrder")
      this.orderBurger[event.name]=event.amount
    },

    //Placing order, connected to place order button in templates

    placeOrder: function() {
      
      const orderData = {

        orderID: this.getOrderNumber(),
        orderItems: this.burgers.map (burger => burger.name),
        customer: this.customerInfo,

      };
      
      // Emit order data via socket or console log (as a placeholder)
      console.log("Order placed:", orderData);
      socket.emit("placeOrder", orderData);

      //Feedback to user
      alert(`Your order is placed! Your order ID is ${orderData.orderId}`);

      this.resetCostumerData();

    },

      resetCostumerData() {

      // Reset the costumer information values
      this.customerInfo.name = "";
      this.customerInfo.email = "";
      this.customerInfo.adress = "";
      this.customerInfo.housenumber = "";
      this.customerInfo.paymentOpt = "Bank Card";
      this.customerInfo.sex = "wontsay";
    
    }


  }
}
</script>








<style>

@import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
@import url('https://fonts.googleapis.com/css?family=Pacifico|Dosis');
@import url('https://fonts.googleapis.com/css2?family=Kanit:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');


/* GENERAL STYLING */

body {
    font-family: Kanit,  arial;
    font-size: 14pt;
 }

/*set background for header*/

 header {
    margin: 2px;

    background-image: url("/Users/mariekexs/Documents/GranssnittLAB1/1MD031-lab-2024/public/img/burgerheavenhead3.jpg");
    background-size: cover;
    background-position: center top; /* Positions the image's center at the top */

    overflow: hidden;
    
    height: 350px;
    opacity: 0.95;
}


/* Centering and styling the header text */
#part_welcomeburgeronline {
    display: flex;
    flex-direction: column; /* Stacks the <h1> and <p> vertically */
    justify-content: center; /* Centers content vertically */
    align-items: center; /* Centers content horizontally */

    /*height: 100%;  Ensures it takes up the entire height of the header */
    text-align: center;
    color: white; /* Adjust text color for contrast against the background */
    padding: 0 20px; /* Optional: some padding for spacing */
}

/* Style adjustments for header text */
#part_welcomeburgeronline h1 {
    font-size: 40pt; /* Increases font size of the title */
    margin: 0; /* Removes default margin */
    text-shadow: /* adds outline to the text*/
    -3px -3px 0 #000, /* Top-left shadow */
     3px -3px 0 #000, /* Top-right shadow */
    -3px  3px 0 #000, /* Bottom-left shadow */
     3px  3px 0 #000; /* Bottom-right shadow */
}

#part_welcomeburgeronline p {
    font-size: 24pt; /* Adjust font size of the paragraph */
    margin-top: 10px; /* Adds spacing below the <h1> */
    font-weight: bold;
    text-shadow: /* adds outline to the text*/
    -2px -2px 0 #000, /* Top-left shadow */
     2px -2px 0 #000, /* Top-right shadow */
    -2px  2px 0 #000, /* Bottom-left shadow */
     2px  2px 0 #000; /* Bottom-right shadow */
}





/* STYLING SECTIONS */

/* set margin for every type scetion */
section {
    margin: 80px 60px;
}

#part_orderburger {
    padding: 20px; /* Adds space inside each burger option */
    border: 2px dotted #a33600;
    border-radius: 30px; /* Rounds the corners of border for a softer look */
    border-width: 15px;
}


#part_costumerinformation {
    padding: 40px 80px; /* Adds space inside rectangle */
    border: 4px double #d05111; /* Adds a border to better see the padding */
    border-radius: 30px; /* Rounds the corners of border for a softer look */
    border-width: 15px;
}



/* FIRST SECTION - BURGER OPTIONS */

/* Ingredient size, type and color */

 .ingredient {
    color: #632100; /* Is set in box layout aswell, choose one*/
 }

 .headline_options {
        font-size: 20pt;
 }
 
    #glutenfri {
        font-weight: bold;
        color: #d9520f;
        font-size: 12 pt;
    }
    #mjolkfri {
        font-weight: bold;
        color: #d9520f;
        font-size: 12 pt;
    }
    #vegetarisk {
        font-weight: bold;
        color: #008b15;
        font-size: 12 pt;
    }


/* set backround and text color for costumer infromation */

#part_costumerinformation {
    color: #ffefcc;
    background-color: #632100;

}


#div_costumerinformation {
    margin: 10px;
}


/*HAMBURGGER OPTIONS STYLING*/

/* Grid template for all burger options */

.wrapper {   
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    grid-gap: 20px;
    max-width: 1200px;
    margin: 0 auto;
    padding: 20px;
}

/* Styling for each burger */
.box {
    background-color: #ffe091;
    color: #59291c;
    border-radius: 30px;
    padding: 10px;
    text-align: center;
    border: 8px solid #a33600;

}

/* Styling for each image */
.box img {
    width: 230px;   
    height: 180px;
    border-radius: 10px;
}

/* Positioning certain burgers to span multiple rows/columns */

.meat {
    grid-column: 1 / span 1; /* spans 1 column in the grid */
}
.chicken {
    grid-column: 2 / span 1;
}
.halloumi {
    grid-column: 3 / span 1;
}

/* Align the ingredients list to the left */
.box ul {
    padding: 0;
    margin: 2em;
    text-align: left;
    width: 100%; /* Makes it span the full width of the box */
}

/* MIDDLE SECTION - COSTUMER INFORMATION */


/* Change size of textboexes to be filled in */
#div_costumerinformation input[type="text"],
#div_costumerinformation input[type="email"],
#div_costumerinformation input[type="number"] {
    width: 250px;    /* Adjust width as desired */
    height: 15px;    /* Adjust height as desired */
    padding: 8px;   /* Add padding, gives a larger feel */
    font-size: 14px; /* Fix font size for readability */
    border-radius: 10px;
}

/* Change size of roll down meny for payment options */
#recipient  {
    width: 200px;
    height: 40px;
    padding: 8px;
    font-size: 14px; /* Fix font size for readability */
    border-radius: 10px;
}


/* LAST SECTION - BUTTON */

#part_button {
    display: flex;
    justify-content: center; /* Centers the button horizontally */
    align-items: center; /* Centers vertically if needed */
    padding: 20px;
}


/* change hover of button */

button:hover {
    background-color:#1f53e2;
    transition: 0.7s;       /* make transition smoother, does not work on mobile apps */

    cursor:pointer;

 }


 /* set size radiobuttons */
 input[type="radio"] {
    height: .9em;
    width: .9em;
}


/* from code given, now with map backgrpund and added scrolling tool */
  #map {
    position: relative;
    width: 1920px;
    height: 1078px;
    background-color: red;
    background: url("/img/polacks.jpg");
    overflow: hidden;

  }
  #div_costumer_map {
    overflow: scroll;
  }

/* styling for dots placed on map */

  .dot {
  position: absolute;
  width: 10px;
  height: 10px;
  background-color: rgb(255, 94, 45);
  border-radius: 50%;
  transform: translate(-50%, -50%);
}


</style>