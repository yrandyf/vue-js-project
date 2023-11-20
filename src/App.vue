<template>
  <Header />
  <div class="container">
    <Balance :total="total" />
    <IncomeExpenses :income="+income"
      :expenses="+expenses" />
    <TransactionList :transactions="transactions"
      @transactionDeleted="handleTransactionDeleted" />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import Header from './components/Header.vue'
import Balance from './components/Balance.vue'
import IncomeExpenses from './components/IncomeExpenses.vue'
import TransactionList from './components/TransactionList.vue'
import AddTransaction from './components/AddTransaction.vue'
import { ref, computed, onMounted } from 'vue'
import { useToast } from 'vue-toastification'

const toast = useToast();

onMounted(()=>{
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));
  if(savedTransactions) {
    transactions.value = savedTransactions;
  }
})
const transactions = ref([
  // { id: 1, text: 'Flower', amount: -19.99 },
  // { id: 2, text: 'Salary', amount: 12.99 },
  // { id: 3, text: 'Book', amount: 15.99 },
  // { id: 4, text: 'Camera', amount: 59 },
  // { id: 6, text: 'Food', amount: 150 },
]);

const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0)
});

// Get Income
const income = computed(() => {
  return transactions.value.filter((transaction) => transaction.amount > 0).reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0).toFixed(2);
})

// Get expenses
const expenses = computed(() => {
  return transactions.value.filter((transaction) => transaction.amount < 0).reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0).toFixed(2);
})

// Add transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  })
  saveTransactionsToLocalStorage();
  toast.success('Transaction Added');
};

// Generate Unique ID
const generateUniqueId = () => {
  return Math.floor(Math.random() * 10000000);
}

// Delete transaction 
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) =>
    transaction.id !== id
  )
  saveTransactionsToLocalStorage();
  toast.success('Transaction Deleted');
}

// Save to local storage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
};
</script>