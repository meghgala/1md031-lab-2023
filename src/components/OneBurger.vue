<template>
    <div>
      <div id="burgers">
          <h4>{{burger.name}}</h4>
          <img v-bind:src="burger.imageUrl" class="burgerImg" alt="Span" title="Another in-line element">
          <ul>
              <span class="gluten"><li v-if="burger.containsGluten">Contains Gluten</li></span>
              <span class="gluten"><li v-if="burger.containsLactose">Contains Lactose</li></span>
              <li>{{ burger.kCal }} Calories</li>
              <div class="button-group">
                <button type="button" @click="decreaseAmount">-</button>
                <p>{{ amountOrdered }}</p>
                <button type="button" @click="increaseAmount">+</button>
              </div>
          </ul>
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
        amountOrdered: 0,
        }
      },
      methods:{
        increaseAmount: function(){
          this.amountOrdered++
          this.addBurger()
        },
        decreaseAmount: function(){
          if (this.amountOrdered > 0) {
          this.amountOrdered--;
        }
        },
        addBurger: function () {
          this.$emit('orderedBurger', { name: this.burger.name, amount: this.amountOrdered } );
      }
      }
  }
  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>

  .burgerImg{
    height: 8em;
    width: 12em;
}
.gluten{
    font-weight:bold;
}

.button-group {
  display: flex;
  align-items: center;
}

button {
  margin: 0 5px; 
}

  </style>
  