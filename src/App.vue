<template>
  <Header/>
  <div class="container">
    <Balance :total="+total"/>
    <IncomeExpense :income="+income" :expenses="+expenses" />
    <TranscationList :transactions="transactions"
    @transactionDeleted="handleTransactionDeleted"/>
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
  </div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpense from './components/IncomeExpense.vue';
import TranscationList from './components/TranscationList.vue';
import AddTransaction from './components/AddTransaction.vue';

import { useToast } from 'vue-toastification';

import { ref, computed, onMounted } from 'vue';

onMounted(()=> {
  const saveTransactions = JSON.parse(localStorage.getItem('transaction'));
  }  
);

const toast = useToast();

const transactions = ref([]);
//算余额
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0); //调用数组reduce方法,它接受一个回调函数和一个初始值 0 作为参数。回调函数接受两个参数，
});
//算收入
const income = computed(() => {
  return transactions.value
    .filter((transactions)=>transactions.amount > 0)
    .reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0).toFixed(2); //调用数组filter方法过滤收入
});

//算支出
const expenses = computed(() => {
  return transactions.value
    .filter((transactions)=>transactions.amount < 0)
    .reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0).toFixed(2); //调用数组filter方法过滤支出
});

  const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
};


const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  });
  saveTransactionsToLocalStorage();
  toast.success("Succeeded added");
};


const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );
  saveTransactionsToLocalStorage();
  toast.success("Transaction Deleted");  
}




const saveTransactionsToLocalStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value))
};




</script>
  
<style>
</style>