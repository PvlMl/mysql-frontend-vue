<template>
  <div>
    <div class="d-flex" style="justify-content: space-between">
      <p>Фильтрация поля:</p>
      <select class="mb-3" v-model="selectedField">
        <option
          v-for="option in fieldOptions"
          :key="option.text"
          :value="option.value"
        >
          {{ option.text }}
        </option>
      </select>
      <p>По условию:</p>
      <select class="mb-3" v-model="selectedСondition">
        <option
          v-for="option in conditionOptions"
          :key="option.text"
          :value="option.value"
        >
          {{ option.text }}
        </option>
      </select>
      <input
        type="text"
        class="mb-3"
        v-model="textArea"
        :disabled="!(selectedField && selectedСondition)"
      />
      <button
        class="mb-3 btn btn-primary"
        @click="goToPrevPage"
        :disabled="page === 1"
      >
        Пред.
      </button>
      <button
        class="mb-3 ml-3 btn btn-primary"
        @click="goToNextPage"
        :disabled="page * 10 >= items.length"
      >
        След.
      </button>
    </div>

    <table class="table table-bordered">
      <thead>
        <tr>
          <th>Дата</th>
          <th role="button" @click="sortByName">Название</th>
          <th role="button" @click="sortByQuantity">Количество</th>
          <th role="button" @click="sortByDistance">Расстояние</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in filteredItems" :key="item.id">
          <td>{{ item.id }}</td>
          <td>{{ item.title }}</td>
          <td>{{ item.likes }}</td>
          <td>{{ item.hits }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "HelloWorld",
  data() {
    return {
      items: [],
      page: 1,
      paginatedItems: [],
      selectedField: null,
      fieldOptions: [
        { text: "Дата", value: "id" },
        { text: "Название ", value: "title" },
        { text: "Количество", value: "likes" },
        { text: "Расстояние", value: "hits" },
      ],
      conditionOptions: [
        { text: "Равно", value: "isEqual" },
        { text: "Содержит ", value: "isIncludes" },
        { text: "Больше", value: "isMore" },
        { text: "Меньше", value: "isLess" },
      ],
      textArea: "",
      selectedСondition: null,
    };
  },
  methods: {
    sortByName() {
      this.paginatedItems.sort(function (a, b) {
        if (a.title > b.title) {
          return 1;
        }
        if (a.title < b.title) {
          return -1;
        }
        return 0;
      });
    },
    sortByQuantity() {
      this.items.sort((a, b) => a.likes - b.likes);
      this.paginatedItems = this.items.slice(
        (this.page - 1) * 10,
        this.page * 10
      );
    },
    sortByDistance() {
      this.items.sort((a, b) => a.hits - b.hits);
      this.paginatedItems = this.items.slice(
        (this.page - 1) * 10,
        this.page * 10
      );
    },
    goToNextPage() {
      if (this.page * 10 < this.items.length) this.page += 1;
    },
    goToPrevPage() {
      if (this.page > 1) this.page -= 1;
    },
    async getProducts() {
      try {
        const response = await axios.get("http://localhost:5000");
        this.items = response.data;
      } catch (err) {
        console.log(err);
      }
    },
  },
  computed: {
    filteredItems() {
      if (!this.textArea || !this.selectedField || !this.selectedСondition) {
        return this.paginatedItems;
      } else {
        if (this.selectedСondition === "isMore") {
          return this.paginatedItems.filter(
            (el) => el[this.selectedField] > this.textArea
          );
        } else if (this.selectedСondition === "isLess") {
          return this.paginatedItems.filter(
            (el) => el[this.selectedField] < this.textArea
          );
        } else if (this.selectedСondition === "isEqual") {
          return this.paginatedItems.filter(
            (el) => el[this.selectedField] == this.textArea
          );
        } else {
          return this.paginatedItems.filter((el) =>
            el[this.selectedField].includes(this.textArea)
          );
        }
      }
    },
  },
  watch: {
    page() {
      this.paginatedItems = this.items.slice(
        (this.page - 1) * 10,
        this.page * 10
      );
    },
  },
  // created() {
  //   this.getProducts();
  // },
  // проверка работоспособности таблицы, если нет возможности получить данные из локальной БД
  mounted() {
    const url = new URL("http://fakeapi.jsonparseronline.com/posts");
    fetch(url)
      .then((r) => r.json())
      .then((res) => (this.items = res))
      .then((res) => (this.paginatedItems = res.slice(0, 10)));
  },
};
</script>
