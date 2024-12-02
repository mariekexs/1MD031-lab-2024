<template>
  <div class="burger-item">
    <h2>{{ burger.name }}</h2>

        <!-- Display image -->
    <img :src="burger.imageUrl" :alt="burger.name" />

    <div clas="ingredient">
        <ul>
           <li v-if="burger.vegetarian"> <span id="vegetarisk"> Vegetarian </span></li>
            <li v-if="burger.lactosefree"> Contains <span id="mjolkfri">lactose</span></li>
            <li v-if="burger.glutenfree">Contains <span id="glutenfri">gluten</span></li>
            <li> Calories: {{ burger.kCal }} kcal </li> 
        </ul>
     </div>

    <div class="amount-container">
      <p> Amount: {{ amountOrdered }} </p>
      <!-- Buttons for changing amount of burgers -->
      <button v-on:click="increaseBurgers">+</button>
      <button v-on:click="decreaseBurgers" :disabled="amountOrdered <= 0">-</button>  
    </div>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },

  data: function () {
  return {
    amountOrdered: this.burger.amount || 0, // Track the amount of burgers ordered, defaulted to 0 (förut 0 bara, uppdaterad 30/11)
    };
  },

  methods: {
  // Method to increase the burger amount
  increaseBurgers: function() {
    this.amountOrdered++;
    this.$emit('orderBurger', { name:   this.burger.name, 
                                amount: this.amountOrdered 
                                });
    },

  // Method to decrease the burger amount
  decreaseBurgers: function() {
    if (this.amountOrdered > 0) {
      this.amountOrdered--;
      this.$emit('orderBurger', { name:   this.burger.name, 
                                  amount: this.amountOrdered 
                                });
      }
    }
  }
}
</script>





<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

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


.burger-item {
  background-color: #ffe091;
  color: #59291c;
  border-radius: 30px;
  padding: 10px;
  text-align: center;
  border: 8px solid #a33600;
  margin-bottom: 20px;
}

.burger-item img {
  width: 230px;
  height: 180px;
  border-radius: 10px;
}

.burger-item ul {
  padding: 0;
  margin: 2em;
  text-align: left;
  width: 100%;
}

.burger-item .headline_options {
  font-size: 20pt;
}

.burger-item .option {
  font-weight: bold;
  color: #d9520f;
  font-size: 12pt;
}


/* styling for the buttons - to match style and line in with amount text */
.amount-container {
  display: flex;         /* Use Flexbox */
  align-items: center;   /* Vertically align items */
  justify-content: center; /* Center items horizontally */
  gap: 10px;             /* Add space between the items */
}

button {
  margin: 0;
  padding: 5px 10px;
  background-color: #a33600;
  color: white;
  font-size: 18px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

</style>




