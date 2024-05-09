<template>
  <div>
    <el-container class="main-container">
      <el-header class="header-container">
        <div class="header-left">
          <el-text class="mx-1" style="color:#E5EAF3; font-size: 25px">Калькулятор книг</el-text>
        </div>
        <div class="header-right">
          <el-button type="primary" plain @click="openModal">Корзина</el-button>        
        </div>
      </el-header>
      
      
      <el-container class="dropdown-container">
        <el-row type="flex">
          <el-col :span="12">
            <el-select v-model="selectedCategory" style="width: 240px" placeholder="Выберите жанр" @change="handleCategoryChange">
              <el-option
                v-for="category in categories"
                :key="category.id"
                :label="category.name"
                :value="category.id"
              />
            </el-select>
          </el-col>

          <el-col :span="12">
            <el-select v-model="selectedBook" style="width: 240px" placeholder="Выберите книгу" @change="handleBookChange">
              <el-option
                v-for="item in filteredItems"
                :key="item.id"
                :label="item.name"
                :value="item"
              />
            </el-select>
          </el-col>
        </el-row>
          </el-container>


          <div class="info-container" style="display: flex; justify-content: center;">
            <div v-if="selectedBook" style="display: flex; align-items: center; ">
              <div class="image-container">
              <img :src="selectedBookImage"  style="width: 200px" >
              </div>
            <div class="info-content-container" >
              <p>Жанр: {{ selectedBookCategory.name }}</p>
              <p>Книга: {{ selectedBook.name }}</p>
              <el-input v-model="quantity" placeholder="Количество" style="width: 172px; margin: 15px 0;"></el-input>
              <el-button type="primary" style="margin: 15px 0;" @click="addToCart">Добавить в корзину</el-button>
              <el-text class="mx-1" style="display: block; color:#E5EAF3; font-size: 25px">Итого: {{ totalPrice }} руб.</el-text>
            </div>
            </div>
          </div>

    </el-container>
  </div>
</template>

<script>
import dDogImage from '../assets/d-dog.jpg';
import dRebekkaImage from '../assets/d-rebekka.jpg';
import dSherlokImage from '../assets/d-sherlok.jpg';
import fNeukrImage from '../assets/f-neukr.jpg';
import fOnoImage from '../assets/f-ono.jpg';
import fPotterImage from '../assets/f-potter.jpg';
import pDetiImage from '../assets/p-deti.jpg';
import pOstrovImage from '../assets/p-ostrov.jpg';
import pWhiteImage from '../assets/p-white.jpg';

import { ElMessage, ElMessageBox } from 'element-plus'

export default {
  data() {
    return {
      selectedCategory: null,
      selectedBook: null,
      selectedBookCategory: null,
      cart: [], 
      categories: [
        { id: 1, name: 'Детектив' },
        { id: 2, name: 'Фэнтези' },
        { id: 3, name: 'Приключения' },
      ],
      items: [
        { 
        id: 1, 
        name: '«Собака Баскервилей» Конан Дойл', 
        categoryId: 1, 
        price: 500, 
        image: dDogImage 
      },
        { 
        id: 2, 
        name: '«Ребекка» Дафна Дюморье', 
        categoryId: 1,
        price: 450, 
        image: dRebekkaImage 

      },
        { 
        id: 3, 
        name: '«Шерлок Холмс» Конан Дойл', 
        categoryId: 1,
        price: 600, 
        image: dSherlokImage 

      },
        { 
        id: 4, 
        name: '«Неукротимая планета» Гарри Гаррисон', 
        categoryId: 2,
        price: 600, 
        image: fNeukrImage 

      },
        { 
        id: 5, 
        name: '«Оно» Стивен Кинг', 
        categoryId: 2,
        price: 700, 
        image: fOnoImage 

      },
        { 
        id: 6, 
        name: '«Гарри Поттер» Дж.К. Роулинг', 
        categoryId: 2,
        price: 250, 
        image: fPotterImage 

      },
        { 
        id: 7, 
        name: '«Дети капитана Гранта» Жюль Верн', 
        categoryId: 3,
        price: 425, 
        image: pDetiImage 

      },
        { 
        id: 8, 
        name: '«Остров сокровищ» Роберт Стивенсон', 
        categoryId: 3,
        price: 270, 
        image: pOstrovImage 

      },
        { 
        id: 9, 
        name: '«Белый клык» Джек Лондон', 
        categoryId: 3,
        price: 550, 
        image: pWhiteImage 

      },      ],
      quantity: 1,
    };
  },
  computed: {
    filteredItems() {
      return this.items.filter(item => item.categoryId === this.selectedCategory);    },
    selectedBookImage() {
      return this.selectedBook ? this.selectedBook.image : '';
    },
    totalPrice() {
      if (this.selectedBook) {
        return this.selectedBook.price * this.quantity;
      }
      return 0;
    },
       totalCartPrice() {
      return this.cart.reduce((total, item) => total + item.quantity * item.price, 0);
    },
  },
  methods: {
    openModal() {
      if (this.cart.length > 0) {
    const cartContent = this.cart.map(item => `${item.name} Количество: ${item.quantity} - Цена: ${item.quantity * item.price} руб.,<hr style="border-top: 1px solid #ccc;">`).join('\n\n');
    ElMessageBox.alert(`${cartContent}\n\nОбщая стоимость корзины: ${this.totalCartPrice} руб.`, 'Корзина', {
      confirmButtonText: 'OK',
      dangerouslyUseHTMLString: true, 
    });
      } else {
        ElMessageBox.alert('Корзина пуста', 'Корзина', {
          confirmButtonText: 'OK',
        });
      }
    },
    handleCategoryChange() {
      this.selectedBook = null;
    },
    handleBookChange() {
      this.selectedBookCategory = this.categories.find(cat => cat.id === this.selectedBook.categoryId);
    },
    addToCart() {
      if (this.selectedBook) {
        const cartItem = {
          name: this.selectedBook.name,
          price: this.selectedBook.price,
          quantity: this.quantity,
        };
        this.cart.push(cartItem); 
        ElMessage.success(`Книга "${this.selectedBook.name}" добавлена в корзину.`);
      } else {
        ElMessage.error('Выберите книгу перед добавлением в корзину.');
      }
    },
  },

};
</script>

<style scoped>
.main-container .el-header{
  position: relative;
  background-color: var(--el-color-primary-light-3);
  text-align: center;
  padding: 10px;
}

.dropdown-container {
  justify-content: center;
  margin: 20px;

}
.el-select {
  margin:20px;
}
.info-container {
margin: 70px;
}
.info-content-container {
  text-align: left;
  padding: 10px;
  color: var(--el-color-primary-light-3);
font-size: 20px;
width: 317px;
}
.header-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}


.header-right {
  margin-right: 20px;
}

</style>
