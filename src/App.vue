<script setup>

import { ref, computed} from 'vue';
 
let id = 0;
const shoppingList = ref([
  { id: id++ , name: "Apple", quantity: 6, done: false },
  { id: id++ , name: "Banana", quantity: 4, done: true },
  { id: id++ , name: "Orange", quantity: 3, done: false },
  { id: id++ , name: "Milk", quantity: 2, done: true },
  { id: id++ , name: "Bread", quantity: 1, done: false },
  { id: id++ , name: "Eggs", quantity: 12, done: true },
  { id: id++ , name: "Cheese", quantity: 1, done: false },
  // { id: id++ , name: "Tomato", quantity: 5, done: false },
  // { id: id++ , name: "Potato", quantity: 10, done: true },
  // { id: id++ , name: "Carrot", quantity: 7, done: false },
]);

let itemToEditId = ref(null);
let itemName = ref('');
let itemQuantity = ref(0);
let errorMessage = ref('');
let isEdit = ref(false);

const addItem = () => {
  if( itemName.value !== '' && itemQuantity.value > 0 ) {
    shoppingList.value.push({
      id: id++,
      name : itemName.value,
      quantity: itemQuantity.value,
      done: false
    })
    itemName.value = '';
    itemQuantity.value = 0;

    if (errorMessage.value !== '') errorMessage.value = '';

  } else {
    errorMessage.value = 'Your shopping list is empty. Please add items.';
  }
}

const removeItem = (itemId) => {
  if( itemId !== undefined ) shoppingList.value = shoppingList.value.filter( item => item.id !== itemId );
}

const setToEditItem = (itemId) => { 
  if( itemId !== undefined ) {
    const itemToEdit = shoppingList.value.find( item => item.id === itemId);
    if( itemToEdit){
      itemToEditId.value = itemToEdit.id;
      itemName.value = itemToEdit.name;
      itemQuantity.value = itemToEdit.quantity;
      isEdit.value = true;
    } else {
      errorMessage.value = 'Item not found for editing.';
      itemToEditId.value = null;
      itemName.value = '';
      itemQuantity.value = 0;
      isEdit.value = false;
    }


  } else {
    errorMessage.value = 'Item not found for editing.';
  }
}

const editItem = ()=>{
  if( itemName.value !== '' && itemQuantity.value > 0 && itemToEditId.value !== null ) {
    const newItemName = itemName.value;
    const newItemQuantity = itemQuantity.value;
    
    shoppingList.value[itemToEditId.value] = {
      id: itemToEditId.value,
      name: newItemName,
      quantity: newItemQuantity,
      done: false
    }
    itemName.value = '';
    itemQuantity.value = 0;
    itemToEditId.value = null;
    isEdit.value = false;
  } else {
    errorMessage.value = 'Please fill in the item name and quantity before editing.';
  }
}

</script>

<template>
  <section class="grid grid-cols-2 h-screen">
    <div class="bg-yellow-500 flex justify-center items-center ">
      <h1 class="text-4xl font-semibold">Shopping List.</h1>
    </div>
    <div class=" bg-gray-100 ">
      <div class="max-w-lg mx-auto p-6">
        <div class=" ">
          <h2 class="text-2xl font-semibold">Your Items</h2>
          <span> Simplify your grocery shopping experience  </span>
          <!-- Error message -->
          
          <div id="errorMessage" class="text-red-500 mt-2 text-sm">{{ errorMessage }}</div>

          <!-- Add item form -->
          <div class="mt-4 flex items-center gap-3 ">
            <div class="grid grid-cols-10 gap-3 w-full">
              <input v-model="itemName" type="text" placeholder="Add an item..."
                class=" col-span-8 w-full py-2 px-4 bg-gray-200 focus:outline-yellow-400  " />
              <input v-model="itemQuantity" type="number" placeholder="0" min="0"
                class=" col-span-2 w-full py-2 px-2 bg-gray-200 focus:outline-yellow-400  " />
            </div>
            <button
              @click="!isEdit ? addItem() : editItem() "
              class=" bg-yellow-500 text-white px-4 py-2  hover:bg-yellow-600 transition duration-300 ease-in-out hover:cursor-pointer">
              {{ !isEdit ? 'Add' : 'Edit'  }}
            </button>
          </div> 
        </div>
        <!-- Item list content -->
        <div class="mt-6">
          <div>
            <ul v-if="shoppingList.length > 0" >
              <li v-for=" item in shoppingList" :key="item.id" class="flex justify-between items-center mb-2 bg-gray-200 p-2 rounded">
                <div class="flex items-center gap-2">
                  <input v-model="item.done" type="checkbox" :name="item.name" :id="item.name" />
                  <div>
                    <label :for="item.name" :class="{ 'line-through' : item.done }"  class="text-lg font-semibold" >{{ item.name }}</label>
                    <span :class="{ 'line-through' : item.done }" class="bg-yellow-500 px-4 py-1 rounded-full text-gray-50 text-xs ml-3 line-trough">Quantity : {{item.quantity}}</span>
                  </div>
                </div>
                <div>

                  <button 
                    v-if="!item.done"
                    @click="setToEditItem(item.id)"
                    class="ml-2 bg-green-500 text-white rounded p-1 hover:bg-green-600 transition duration-300 ease-in-out hover:cursor-pointer">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-4">
                      <path stroke-linecap="round" stroke-linejoin="round" d="m16.862 4.487 1.687-1.688a1.875 1.875 0 1 1 2.652 2.652L10.582 16.07a4.5 4.5 0 0 1-1.897 1.13L6 18l.8-2.685a4.5 4.5 0 0 1 1.13-1.897l8.932-8.931Zm0 0L19.5 7.125M18 14v4.75A2.25 2.25 0 0 1 15.75 21H5.25A2.25 2.25 0 0 1 3 18.75V8.25A2.25 2.25 0 0 1 5.25 6H10" />
                    </svg>
                  </button>

                  <button
                    @click="removeItem(item.id)"
                    class="ml-2 bg-gray-500 text-white rounded p-1 hover:bg-red-600 transition duration-300 ease-in-out hover:cursor-pointer">
                    <!-- Trash icon -->
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                      stroke="currentColor" class="size-4">
                      <path stroke-linecap="round" stroke-linejoin="round"
                        d="m14.74 9-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 0 1-2.244 2.077H8.084a2.25 2.25 0 0 1-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 0 0-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 0 1 3.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 0 0-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 0 0-7.5 0" />
                    </svg>
                  </button>
                </div> 
              </li>
            </ul>
            <p v-else class="text-gray-500">No items in your shopping list.</p>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
h1 {
  letter-spacing: 2px;
}

input[type="checkbox"] {
  appearance: none;

  width: 20px;
  height: 20px;
  border: 2px solid #ccc;
  border-radius: 50%;
  outline: none;
  cursor: pointer;
  transition: all 0.3s ease-in-out;
  background-color: #fff;
}

input[type="checkbox"]:checked {
  background-color: #facc15;
  /* Yellow */
  border-color: #f59e0b;
  /* Darker yellow */
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
}

input[type="checkbox"]:checked::before {
  content: "✔";
  display: flex;
  justify-content: center;
  align-items: center;
  color: white;
  font-size: 14px;
  font-weight: bold;
}
</style>
