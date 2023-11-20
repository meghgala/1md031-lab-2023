<template>
<header class="heading">
        <h1>Welcome to Burger Queen</h1>
        <img class="image" src="https://media.istockphoto.com/id/1156217454/photo/antique-electronic-decorative-lamp-in-factory.jpg?s=612x612&w=0&k=20&c=TcPiRwEIJ8CifE7u3Lru6R22xYy6FcNRLE8UGnXPLJw=" alt="Span" title="Another in-line element">
    </header>
    <main>
      <section class="menu">
            <h3>Make your selection</h3>
            <p>Welcome to our burger paradise! at Burger Queen, we take pride in offering a diverse range of mouthwatering burgers to satisfy every craving. Whether you're a meat lover, a vegetarian, or a dedicated vegan, we've got something delicious just for you.</p>
            <div class="wrapper">
              <Burger v-for="burger in burgers"
                      v-bind:burger="burger" 
                      v-bind:key="burger.name"
                      v-on:orderedBurger="addToOrder($event)"/>
            </div>
      </section>
        <section class="form">
            <h3>Customer Information</h3>
            <p>We would like you to fill out a short form to mention the delivery address and your email address so that we can deliver your meal to your doorstep</p>
            <p>
                <label for="firstname">Full name </label><br>
                <input type="text" id="firstname" v-model="fn" required="required" placeholder="Enter first & last name">
            </p>
            <p>
                <input type="email" id="email" v-model="em" required="required" placeholder="Enter e-mail address">
            </p>
            <p>
                <label for="payment">Payment Methods</label><br>
                <select id="payment" v-model="pay">
                    <option>Credit card</option>
                    <option>Debit card</option>
                    <option>Cash on delivery</option>
                    <option>Invoice</option>
                </select>
            </p>
              <div>
                <label>Select Gender</label><br>
                <input type="radio" id="male" v-model="gender"  value="male"/>
                <label for="male">Male</label><br>
              
                <input type="radio" id="female" v-model="gender" value="female"/>
                <label for="female">Female</label><br>
          
                <input type="radio" id="none" v-model="gender" value="none" checked/>
                <label for="none">Prefer to not mention</label>
              </div><br>
              
                <div id="map" class="map-container" @click="setLocation">
                  <img src="../../public/img/polacks.jpg" alt="Map Image" @click="setLocation">
                <div v-if="location.x !== 0 || location.y !== 0" class="target" :style="{ left: location.x + 'px', top: location.y + 'px' }">
                  T
                </div>
              
            </div>
        </section>
        <button type="button" @click="logdata">
            <img src="https://icons.iconarchive.com/icons/paomedia/small-n-flat/512/sign-check-icon.png" style="width: 15px; height: 15px;">
            Place Order
        </button>
    </main>
    <hr>
    <footer>&copy; Burger Queen Inc.</footer>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io();

// function MenuItem(name, imageUrl, kCal, containsGluten, containsLactose) {
//   this.name = name;
//   this.imageUrl = imageUrl;
//   this.kCal = kCal;
//   this.containsGluten = containsGluten;
//   this.containsLactose = containsLactose;
// }

// const burgersArray = [
//   new MenuItem("Chicken Burger", "https://images.unsplash.com/photo-1568901346375-23c9450c58cd?q=80&w=1899&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D", 300, false, true),
//   new MenuItem("Beef Burger", "https://usercontent.one/wp/www.burgerdudes.se/wp-content/uploads/2021/04/american_steakhouse_burgers_mc_original_dec22_med.jpg?media=1699436848", 400, true, false),
//   new MenuItem("Halloumi Burger", "https://images.happycow.net/venues/1024/13/99/hcmp139936_540748.jpeg", 500, false, false),
// ];

console.log(menu);
export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers:  menu ,
      fn: '', 
      em: '', 
      pay: 'Credit card',
      gender:'none',
      orderedBurgers:{},
      burger_name :'',
      burger_amt : '',
      location: { x: 0,
            y: 0
          }
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                                orderItems: ["Beans", "Curry"]
                              }
                 );
    },

    setLocation: function(event){
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      this.location = {
        x: event.clientX - 10 - offset.x,
        y: event.clientY - 10 - offset.y
      };
    },

    addToOrder: function(event){
      this.orderedBurgers[event.amount] = event.amount;
      this.orderedBurgers[event.name] = event.name;
      this.burger_name = this.orderedBurgers[event.name]
      this.burger_amt = this.orderedBurgers[event.amount]
      return this.burger_amt,this.burger_name
    },

    logdata: function() {
        console.log('Burger Ordered');
        console.log('Burger Name',this.burger_name)
        console.log('Burger Amount',this.burger_amt )
        console.log('Full Name:', this.fn);
        console.log('Email:', this.em);
        console.log('Payment Method:', this.pay);
        console.log('Gender:', this.gender);
        console.log('location', this.location)
        socket.emit("addOrder", {
          orderId: this.getOrderNumber(),
          details: this.location, 
          orderItems: [{ name: this.burger_name, amount: this.burger_amt }],
          customerName: this.fn,
          customerEmail: this.em,
          customerPayment: this.pay,
          customerGender: this.gender
    });
    },
  }
}
</script>

<style>
/* .map-container {
  width: 100%;
  height: 300px;
  overflow: auto; 
  position: relative; 
  background: url("../../public/img/polacks.jpg");
} */

#map {
  width: 100%;
  height: 400px;
  overflow: auto; 
  position: relative;
}

img {
  width: 100%; 
  height: auto; 
  display: block; 
}

  body {
    font-family: arial;
 }
 .menu{
    background-color: black;
    color: azure;
    margin-left: 2em;
    padding-left: 1em;
    border: dashed;
}

.wrapper{
    display: grid;
    grid-gap: 10px;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));

}
.wrapper img{
    height: 45%;
}

button{
    margin-left: 2.5em;
    margin-bottom: 1em;
}

.target {
    position: absolute;
    background-color: black; 
    color: white;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    text-align: center;
    font-weight: bold;
  }

button:hover{
    background-color: black;
    color: azure;
    cursor: pointer;
}

.heading{
    margin-left: 2em;
    height: 15em;
    overflow: hidden;
    margin-bottom: 1em;
}

.image{
    opacity: 0.7;
    width: 100%;
    height: auto;
}

.heading h1{
    position: absolute;
    color: black;
    padding-left: 33%;
    padding-top: 5%;
    z-index: 1;
}

.form{
    margin-top: 1em;
    margin-left: 2em;
    padding-left: 1em;
    padding-bottom: 0.5em;
    margin-bottom: 2em;
    border: dashed;
}



</style>