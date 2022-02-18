<template>
  <div class="items">
    <div class="item">
      <div
        style="
          display: flex;
          justify-content: space-between;
          width: 100%;
          align-items: flex-end;
        "
      >
        <div class="item__trash">
          <img
            @click="deleteItem(item.id)"
            src="/assets/img/garbage.png"
            alt=""
          />
        </div>
        <div style="display: flex; width: 100%; justify-content: flex-end">
          <div
            style="
              display: flex;
              flex-direction: column;
              align-items: flex-end;
              justify-content: end;
              margin-right: 10px;
            "
          >
            <p style="font-weight: bold; margin: 0">{{ item.title.text }}</p>
            <p style="margin: 8px 0">{{ item.description }}</p>
          </div>
          <div class="item__img">
            <img style="width: 100%" :src="item.img" alt="" />
          </div>
        </div>
      </div>
      <div :style="{ backgroundColor: item.color }" class="item__color"></div>
    </div>
  </div>
</template>


<script>
import axios from "axios";
export default {
  name: "item",
  props: ["item"],
  methods: {
    async deleteItem(id) {
      try {
        await axios.delete(`http://localhost:3000/items/${id}`);
        this.$emit("item-deleted");
      } catch (e) {
        console.error(e);
      }
    },
  },
};
</script>
<style lang="scss">
.item {
  display: flex;
  align-items: center;
  flex-direction: column;
  width: 600px;
  background-color: #ece9f3;
  margin: 10px auto;
  padding: 12px 20px 8px 20px;
  border-radius: 10px;
}
.item__color {
  width: 100%;
  height: 7px;
  border-radius: 10px;
  margin: auto;
  margin-top: 8px;
}
.item__img {
  width: 80px;
  height: 80px;
  background-color: darkblue;
  border-radius: 10px;

  img {
    height: 80px;
    border-radius: 10px;
  }
}

.item__trash {
  width: 30px;
  height: 30px;
  img {
    width: 100%;
  }
}

@media screen and (max-width: 670px) {
  .item {
    width: 300px;
  }
}
</style>