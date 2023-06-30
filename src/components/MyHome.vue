<template>
  <MyLayout>
    <template #header>
      <MyHeader></MyHeader>
    </template>
    <template #resume>
      <MyResume
        :total-label="'Ahorro total'"
        :label="label"
        :total-amount="totalAmount"
        :amount="amount"
      >
        <template #graphic>
          <MyGraphic :amounts="amounts" @select="select" />
        </template>
        <template #action>
          <MyAction @create="create"></MyAction>
        </template>
      </MyResume>
    </template>
    <template #movements>
      <MyMovements @remove="remove" :movements="movements" />
    </template>
  </MyLayout>
</template>

<script>
import MyLayout from "./MyLayout";
import MyHeader from "./MyHeader";
import MyResume from "./resume/MyIndex";
import MyMovements from "./movements/MyIndex";
import MyAction from "./MyAction";
import MyGraphic from "./resume/MyGraphic";

export default {
  components: {
    MyLayout,
    MyHeader,
    MyResume,
    MyMovements,
    MyAction,
    MyGraphic,
  },
  data() {
    return {
      label: null,
      amount: null,
      movements: [],
    };
  },
  computed: {
    amounts() {
      const lastDays = this.movements
        .filter((m) => {
          const today = new Date();
          const oldDate = today.setDate(today.getDate() - 30);

          return m.time > oldDate;
        })
        .map((m) => m.amount);

      return lastDays.map((m, i) => {
        const lastMovements = lastDays.slice(0, i + 1);

        return lastMovements.reduce((suma, movement) => {
          return suma + movement;
        }, 0);
      });
    },
    totalAmount() {
      if (this.movements.length <= 0) return 0;
      return this.movements.reduce((suma, m) => {
        return suma + m.amount;
      }, 0);
    },
  },
  mounted() {
    const movements = JSON.parse(localStorage.getItem("movements"));
    if (Array.isArray(movements)) {
      this.movements = movements?.map((m) => {
        return { ...m, time: new Date(m.time) };
      });
    }
  },
  methods: {
    create(movement) {
      this.movements.push(movement);
      this.save();
    },
    remove(id) {
      const index = this.movements.findIndex((m) => m.id === id);
      this.movements.splice(index, 1);
      this.save();
    },
    save() {
      localStorage.setItem("movements", JSON.stringify(this.movements));
    },
    select(el, index) {
      this.amount = el;
      if (index >= 0) {
        const dataStaying = this.movements[index].time.toLocaleString("en-GB", {
          day: "numeric",
          month: "numeric",
          year: "numeric",
        });
        this.label = `ahorro hasta el ${dataStaying}`;
      }
    },
  },
};
</script>
