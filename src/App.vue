<template>
  <Header></Header>
  <div class="container">
    <Balance :total="+total"></Balance>
    <IncomeExpenses :income="+income" :expenses="+expenses" />
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDelete" />
    <AddTansaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpenses from "./components/IncomeExpenses.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTansaction from "./components/AddTransaction.vue";
import { ref, computed, onMounted } from "vue";
import { useToast } from "vue-toastification";

const toast = useToast()

const transactions = ref([]);
onMounted(()=>{
  const savedTransactions = JSON.parse(localStorage.getItem('transaction'))

  if(savedTransactions){
    transactions.value = savedTransactions
  }

})
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});

const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0);
});

const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0);
});

const handleTransactionSubmitted = (transactionsData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionsData.text,
    amount: transactionsData.amount,
  });
  savedTransactionToLocalStorage()
  toast.success("Transaction added")
};

const generateUniqueId = () => {
  return Math.floor(Math.random() * 100000);
};
const handleTransactionDelete = (id) => {
  transactions.value = transactions.value.filter((transaction)=> transaction.id!=id)
  savedTransactionToLocalStorage
  toast.success("Transaction Deleted")
}

const savedTransactionToLocalStorage = () => {
  localStorage.setItem('transaction',JSON.stringify(transactions.value))
}
</script>
