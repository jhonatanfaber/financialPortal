<template>
  <div class="currency-tab-wrapper">
    <ul>
      <li v-for="currency in currencies" :key="currency.id" @click="filterFunds(currency.id)" >
        <img :src="currency.icon" :alt="currency.name" width="20px">
        <span :style='[selectedFilter == "CurrencyTab" && currency.id == selectedCurrency? {"color":"#02b5c4"} : {"color":"#828282"}]'>{{currency.id}}</span>
      </li>
    </ul>
  </div>
</template>

<script>
import { mapGetters } from "vuex";

export default {
  data() {
    return {
      currencies: [
        {
          name: "All Currencies",
          id: "All",
          icon: require("./../assets/icons/world.svg")
        },
        {
          name: "European flag",
          id: "EUR",
          icon: require("./../assets/icons/european-flag.svg")
        },
        {
          name: "Japanese flag",
          id: "JPY",
          icon: require("./../assets/icons/japan-flag.png")
        },
        {
          name: "American flag",
          id: "USD",
          icon: require("./../assets/icons/united-states-flag.svg")
        }
      ]
    };
  },
  computed: {
    ...mapGetters(["funds", "selectedFamily",'selectedFilter', 'selectedCurrency'])
  },
  methods: {
    filterFunds(currentCurrency) {
      this.updateCurrentCurrency(currentCurrency);

      if (this.checkIfBothAreFilteredByAll(currentCurrency)) return;

      if (this.checkIfCurrentFamilyIsAll(currentCurrency)) return;

      if (this.checkIFCurrentFamilyIsNotAllAndCurrencyIsAll(currentCurrency))
        return;

      this.filterWhenBothAreNotAll(currentCurrency);
    },
    updateCurrentCurrency(currency) {
      this.$store.commit("updateSelectedCurrency", currency);
    },
    checkIfBothAreFilteredByAll(currentCurrency) {
      if (currentCurrency == "All" && this.selectedFamily == "All") {
        this.commitMutation(this.funds);
        return true;
      }
      return false;
    },
    checkIfCurrentFamilyIsAll(currentCurrency) {
      if (this.selectedFamily == "All") {
        let filteredAssets = this.funds.filter(fund => {
          return fund.currency == currentCurrency;
        });
        this.commitMutation(filteredAssets);
        return true;
      }
      return false;
    },
    checkIFCurrentFamilyIsNotAllAndCurrencyIsAll(currentCurrency) {
      if (currentCurrency == "All" && this.selectedFamily != "All") {
        let filteredAssets = this.funds.filter(fund => {
          return fund.risk_family == this.selectedFamily;
        });
        this.commitMutation(filteredAssets);
        return true;
      }
      return false;
    },
    filterWhenBothAreNotAll(currentCurrency) {
      let filteredAssets = this.funds.filter(fund => {
        return (
          fund.currency == currentCurrency &&
          fund.risk_family == this.selectedFamily
        );
      });
      this.commitMutation(filteredAssets);
    },
    commitMutation(list) {
      this.$store.commit("saveFiltered", list);
    }
  }
};
</script>


<style scoped>
.currency-tab-wrapper {
  width: 100%;
  height: 30%;
}

ul {
  height: 100%;
  list-style-type: none;
  padding: 0;
  display: flex;
  justify-content: space-around;
  flex-direction: column;
  align-items: center;
  position: relative;
  right: 40px;
}

li {
  display: flex;
  flex-direction: row;
  align-items: flex-start;
  justify-content: space-around;
  width: 25%;
  cursor: pointer;
}

span {
  width: 20px;
  font-weight: bold;
  color: #828282;
}
</style>
