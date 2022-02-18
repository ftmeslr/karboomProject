<template>
  <div>
    <div class="serchbox__box">
      <div style="margin: auto; display: flex; padding: 20px">
        <div class="serchbox__add" @click="addItem()">
          <img class="searchbox__img" src="/assets/img/plus.png" alt="" />
        </div>

        <form
          novalidate="novalidate"
          onsubmit="return false;"
          class="searchbox"
        >
          <div role="search" class="searchbox__wrapper">
            <input
              id="search-input"
              type="search"
              name="search"
              placeholder="Search "
              autocomplete="off"
              required="required"
              class="searchbox__input"
              v-model="searchQuery"
              @keyup="searchItems"
            />
            <button
              type="submit"
              title="Submit your search query."
              class="searchbox__submit"
            >
              <svg role="img" aria-label="Search">
                <use
                  xmlns:xlink="http://www.w3.org/1999/xlink"
                  xlink:href="#sbx-icon-search-13"
                ></use>
              </svg>
            </button>
            <button
              type="reset"
              title="Clear the search query."
              class="searchbox__reset hide"
            >
              <svg role="img" aria-label="Reset">
                <use
                  xmlns:xlink="http://www.w3.org/1999/xlink"
                  xlink:href="#sbx-icon-clear-3"
                ></use>
              </svg>
            </button>
          </div>
        </form>

        <div
          class="svg-icons"
          style="height: 0; width: 0; position: absolute; visibility: hidden"
        >
          <svg xmlns="http://www.w3.org/2000/svg">
            <symbol id="sbx-icon-clear-3" viewBox="0 0 40 40">
              <path
                d="M16.228 20L1.886 5.657 0 3.772 3.772 0l1.885 1.886L20 16.228 34.343 1.886 36.228 0 40 3.772l-1.886 1.885L23.772 20l14.342 14.343L40 36.228 36.228 40l-1.885-1.886L20 23.772 5.657 38.114 3.772 40 0 36.228l1.886-1.885L16.228 20z"
                fill-rule="evenodd"
              />
            </symbol>
            <symbol id="sbx-icon-search-13" viewBox="0 0 40 40">
              <path
                d="M26.806 29.012a16.312 16.312 0 0 1-10.427 3.746C7.332 32.758 0 25.425 0 16.378 0 7.334 7.333 0 16.38 0c9.045 0 16.378 7.333 16.378 16.38 0 3.96-1.406 7.593-3.746 10.426L39.547 37.34c.607.608.61 1.59-.004 2.203a1.56 1.56 0 0 1-2.202.004L26.807 29.012zm-10.427.627c7.322 0 13.26-5.938 13.26-13.26 0-7.324-5.938-13.26-13.26-13.26-7.324 0-13.26 5.936-13.26 13.26 0 7.322 5.936 13.26 13.26 13.26z"
                fill-rule="evenodd"
              />
            </symbol>
          </svg>
        </div>
      </div>
    </div>

    <div>
      <div
        v-for="item in items"
        :key="item.id"
        style="display: flex; align-items: center; justify-content: center"
      >
        <item @item-deleted="itemDeletedHandler($event)" :item="item"></item>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import item from "./components/item";
import { v4 as uuidv4 } from "uuid";

export default {
  name: "App",
  emits: ["item-deleted"],
  components: {
    item,
  },
  data() {
    return {
      items: [],
      searchQuery: null,
      timeOut: null,
      newItem: {
        title: {
          text: null,
        },
        id: null,
        description: "lorem ipsum dolor",
        img: "https://source.unsplash.com/random",
        color: "#00334e",
      },
    };
  },
  created() {
    this.showItems();
  },

  methods: {
    async showItems() {
      try {
        const res = await axios.get(`http://localhost:3000/items`);
        this.items = res.data;
      } catch (e) {
        console.error(e);
      }
    },

    itemDeletedHandler() {
      this.showItems();
    },
    async addItem() {
      try {
        if (!this.searchQuery) return;
        this.newItem.title.text = this.searchQuery;
        this.newItem.id = uuidv4();
        await axios.post(`http://localhost:3000/items`, this.newItem);
        this.searchQuery = "";
      } catch (e) {
        console.error(e);
      }
    },
    async searchItems() {
      try {
        clearTimeout(this.timeOut);
        if (this.searchQuery === null || this.searchQuery === undefined) return;
        var self = this;
        this.timeOut = setTimeout(async () => {
          const res = await axios.get(
            `http://localhost:3000/items?q=${self.searchQuery}`
          );
          self.items = res.data;
        }, 1000);
      } catch (e) {
        console.error(e);
      }
    },
  },
};
</script>

<style lang="scss">
.serchbox__box {
  width: 640px;
  background-color: #dce2e8;
  margin: auto;
  border-radius: 10px;
  display: flex;
  align-items: center;

  .serchbox__add {
    width: 37px;
    height: 37px;
    background-color: #fff;
    border-radius: 100%;
    margin-right: 10px;

    img {
      width: 20px;
    }
  }
}
.searchbox {
  display: inline-block;
  position: relative;
  width: 400px;
  height: 37px;
  white-space: nowrap;
  box-sizing: border-box;
  font-size: 13px;
  font-family: arial, sans-serif;
  .algolia-autocomplete {
    display: block;
    height: 100%;
  }
}
.searchbox__img {
  margin: auto;
  margin-top: auto;
  display: block;
  margin-top: 9px;
}
.searchbox__wrapper {
  width: 100%;
  height: 100%;
}

.searchbox__input {
  display: inline-block;
  border: 0;
  border-radius: 19px;
  box-shadow: inset 0 0 0 1px #d9d9d9;
  background: #ffffff;
  padding: 0;
  padding-right: 30px;
  padding-left: 37px;
  width: 100%;
  height: 100%;
  vertical-align: middle;
  white-space: normal;
  font-size: inherit;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
}

.searchbox__input::-webkit-search-decoration,
.searchbox__input::-webkit-search-cancel-button,
.searchbox__input::-webkit-search-results-button,
.searchbox__input::-webkit-search-results-decoration {
  display: none;
}

.searchbox__input:hover {
  box-shadow: inset 0 0 0 1px silver;
}

.searchbox__input:focus,
.searchbox__input:active {
  outline: 0;
  box-shadow: inset 0 0 0 1px #4098ce;
  background: #ffffff;
}

.searchbox__input::-webkit-input-placeholder {
  color: #aaaaaa;
}

.searchbox__input::-moz-placeholder {
  color: #aaaaaa;
}

.searchbox__input:-ms-input-placeholder {
  color: #aaaaaa;
}

.searchbox__input::placeholder {
  color: #aaaaaa;
}

.searchbox__submit {
  position: absolute;
  top: 0;
  right: inherit;
  left: 0;
  margin: 0;
  border: 0;
  border-radius: 18px 0 0 18px;
  background-color: rgba(255, 255, 255, 0);
  padding: 0;
  width: 37px;
  height: 100%;
  vertical-align: middle;
  text-align: center;
  font-size: inherit;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

.searchbox__submit::before {
  display: inline-block;
  margin-right: -4px;
  height: 100%;
  vertical-align: middle;
  content: "";
}

.searchbox__submit:hover,
.searchbox__submit:active {
  cursor: pointer;
}

.searchbox__submit:focus {
  outline: 0;
}

.searchbox__submit svg {
  width: 17px;
  height: 17px;
  vertical-align: middle;
  fill: #8949f0;
}

.searchbox__reset {
  position: absolute;
  top: 8px;
  right: 8px;
  margin: 0;
  border: 0;
  background: none;
  cursor: pointer;
  padding: 0;
  font-size: inherit;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  fill: rgba(0, 0, 0, 0.5);
  &.hide {
    display: none;
  }
}

.searchbox__reset:focus {
  outline: 0;
}

.searchbox__reset svg {
  display: block;
  margin: 4px;
  width: 13px;
  height: 13px;
}

.searchbox__input:valid ~ .searchbox__reset {
  display: block;
  -webkit-animation-name: sbx-reset-in;
  animation-name: sbx-reset-in;
  -webkit-animation-duration: 0.15s;
  animation-duration: 0.15s;
}

@media screen and (max-width: 670px) {
  .serchbox__box {
    width: 340px;
  }
  .searchbox__input {
    width: 240px;
  }
}
</style>
