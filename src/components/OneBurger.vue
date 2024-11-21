<template>
  <div class="burger-item">
    <h2>{{ burger.name }}</h2>

        <!-- Display image -->
    <img :src="burger.imageUrl" :alt="burger.name" />

    <p>Calories: {{ burger.kCal }} kcal</p>
    <p>Lactose Free: {{ burger.lactosefree ? 'Yes' : 'No' }}</p>
    <p>Gluten Free: {{ burger.glutenfree ? 'Yes' : 'No' }}</p>
    <p>Vegetarian: {{ burger.vegetarian ? 'Yes' : 'No' }}</p>

    <div class="amount-container">

      <p> Amount: {{ amountOrdered }} </p>

      <!-- Buttons for changing amount of burgers -->
      <button @click="increaseBurgers">+</button>
      <button @click="decreaseBurgers" :disabled="amountOrdered <= 0">-</button>
    
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
    amountOrdered: 0, // Track the amount of burgers ordered
    }
  },

  methods: {

  // Method to increase the burger amount
  increaseBurgers() {
    this.amountOrdered++;
  },

  // Method to decrease the burger amount
  decreaseBurgers() {
    if (this.amountOrdered > 0) {
      this.amountOrdered--;
    }
  },

  // Optional: This can be used to handle an order event or submit the burger
  orderBurger(burger) {
    console.log('Burger ordered:', burger);
    alert('Order placed for: ' + burger.name + ' Quantity: ' + this.amountOrdered);
  }

  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>



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




