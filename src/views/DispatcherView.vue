<template>
    <div id="orders">
      <div id="orderList">

        <!-- Display a loading message if orders are not loaded yet -->
        <div v-if="!orders">Loading orders...</div>


        <!-- Only show the order list if orders exist -->
        <div v-else id="orderList">
          <!-- <div v-for="([key, order]) in Object.entries(orders)" :key="'order' + key">-->
          
            <div v-for="(order, key) in orders" v-bind:key="'order'+key">
              
              <h3>Order #{{ key }}</h3>
              
              <p v-if="item?.name">{{ item.name }}</p>
              <p v-else>Name is not available</p>

             <!--  <p> Costumer: {{ order.details.name }}</p>-->
              <p> Email: {{ order.details?.email }}</p>
              <p> Payment option: {{ order.details?.payOpt }}</p>

              <p> {{ order.orderItems?.name }} + {{ order.orderItems?.amount }}  </p>

              <hr>
            </div>

              <button v-on:click="clearQueue">Clear Queue</button>
        </div>

      <!--Displaying messages-->
      <div id="dots">
          <div v-for="(order, key) in orders" v-bind:style="{ left: order.details?.x + 'px', top: order.details?.y + 'px'}" v-bind:key="'dots' + key">
            {{ key }}
          </div>
      </div>
    
    </div>
  </div>

  </template>



  <script>
  import io from 'socket.io-client'
  const socket = io("localhost:3000");
  

  //Receiving messages
  export default {
    name: 'DispatcherView',

    props: {
    dataFromHome: {
      type: Object, // Adjust type based on what you're passing (Object, Array, etc.)
      required: true,
     },
    },


    data: function () {
      return {
        orders: null,
      }
    },



    created: function () {
      socket.on('currentQueue', data => {
      console.log(data);  // Log the structure of the data to debug
      this.orders = data.orders;
  });
    },
    methods: {
      clearQueue: function () {
        socket.emit('clearQueue');
        console.log("Queue cleared");
      },
      changeStatus: function(orderId) {
        socket.emit('changeStatus', {orderId: orderId, status: "Annan status"});

      }
    }
  }
  </script>
  <style>
  #orderList {
    top:1em;
    left:1em;
    position: absolute;
    z-index: 2;
    color:black;
    background: rgba(255,255,255, 0.5);
    padding: 1em;
  }
  #dots {
    position: relative;
    margin: 0;
    padding: 0;
    background-repeat: no-repeat;
    width:1920px;
    height: 1078px;
    cursor: crosshair;
    background-image: url('/img/polacks.jpg');
  }
  
  #dots div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }
  </style>
  