<script setup>
  import {ref,reactive, watch, TransitionGroup} from 'vue'

  let valueProductInput = ref('')

  const initialData = [
    {
      id: Date.now()+Math.random(),
      name:'Апельсины',
      isCrossOut:false
    },
    {
      id: Date.now()+Math.random(),
      name:'Яблоки',
      isCrossOut:false
    },
    {
      id: Date.now()+Math.random(),
      name:'Мандарины',
      isCrossOut: false
    },
    {
      id: Date.now()+Math.random(),
      name:'Огурцы',
      isCrossOut: true
    }
  ]

  let products = reactive(
    JSON.parse(localStorage.getItem('productList')) || initialData
  )

  const moveProduct = (product, i) => {
 
    if (!product.isCrossOut) {
      product.isCrossOut = !product.isCrossOut
      products.splice(i, 1)
      products.push(product)
      return
    }

    const idx = products.findIndex(pr=>pr.isCrossOut)
    product.isCrossOut = !product.isCrossOut

    if (idx===i) return

    products.splice(i, 1)
    products.splice(idx, 0, product)

  }

  const deleteProduct = (i)=>{
    products.splice(i, 1)
  }

  const addProduct = ()=>{
  
    if (!valueProductInput.value.trim()){
      alert('введите название продукта')
      return
    }

    products.unshift({
      id: Date.now(),
      name:  valueProductInput.value,
      isCrossOut: false
    })

    valueProductInput.value=''
  }

  watch(products, (newValue)=>{
     localStorage.setItem('productList', JSON.stringify(newValue))
  },
  { deep: true })
  
</script>

<template>
  <div class="wrapper">
    <div class="form">
      <h3> Форма для добавления продукта </h3>
      <input class="input" placeholder="Название продукта" v-model="valueProductInput"/> 
      <button @click="addProduct" class="btn"> Добавить продукт </button>
    </div>

    <h2> Список продуктов</h2>
    <p> Чтобы зачеркнуть продукт или вернуть продукт из числа зачеркнутых произведите дабл клик по карточке.</p>

    <TransitionGroup name="list" tag="ul" class="list">
      <li 
        v-for="product, i in products"
        :key="product.id"
        @dblclick="moveProduct(product, i)"
        :class="{
            'liElemCrossedOut': product?.isCrossOut,
            'liElem': true
          }"
        
      > 
        <span 
          :class="{
            'crossOut': product?.isCrossOut
          }"
        > 
          {{ product?.name }}
       </span>
       <button  @click="deleteProduct(i)" class="deleteBtn"> Удалить </button>
      </li>
    </TransitionGroup>

  </div>
</template>

<style scoped>

.list-move, 
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(100px);
}

p, h2, h3 {
  margin: 0px;
}

.wrapper {
  display: flex;
  flex-direction: column;
  gap: 16px;
  padding: 64px 32px;
  max-width: 500px;
}

.list {
  display: flex;
  flex-direction: column;
  gap: 16px;
  margin-left: 0;
  padding-left: 0;
}

.form {
  display: flex;
  flex-direction: column;
  gap: 16px;
  border: 1px solid blue;
  padding: 10px;
  margin-bottom: 16px;
}
.liElem {
  display: flex;
  justify-content: space-between;
  align-items: center;
  cursor: pointer;
  border: 1px solid blue;
  padding: 8px;
  transition: border-color 0.3s ease-in, opacity 0.3s ease-in;
}

.liElem:hover {
 border-color:aquamarine
}
.input {
  padding: 8px 16px;
}

.crossOut {
  text-decoration: line-through;
}

.btn {
  padding: 16px 32px;
  border-radius: 12px;
  color:white;
  background-color: blue;
  font-size: 18px;
  font-weight: 400;
  cursor: pointer;
  transition: background-color 0.3s ease-in;
  border: none;
  width: fit-content;
  display: block;
}

.btn:hover {
  background-color:black;
} 

.deleteBtn {
  cursor: pointer;
  border: 1px solid red;
  padding: 4px;
  background-color: blanchedalmond;
  border-radius: 4px;
  min-width: 100px;
}

.deleteBtn:hover {
  font-weight: bold;
}

.liElemCrossedOut {
  opacity: 0.5;
}

</style>
